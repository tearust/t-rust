## The basic and advanced topic in this article

You do not need to understand all the content in this article. Most of them are advanced topic that only for those advanced readers. It is totally find to skip this article and continue this tutorial as long as you understand the following items:

* The request and response are all what the actors do. 
* Copy and paste the type definition from the boilerplate and add your own types follow the same template. Pay attention on the parameter types. 
* In most cases, it should work just fine. If not, please come back to read the advanced topics below. Good luck!

## What does \[derived(TypeId)\] mean?

In our hello_world tutorial, you have found the Requests and Response defined in the codec/src/lib.rs like this:

````
#[derive(Debug, Clone, Serialize, Deserialize, TypeId)]
#[response(())]
pub struct GreetingsRequest(pub String);

#[derive(Debug, Clone, Serialize, Deserialize, TypeId)]
pub struct AddRequest(pub i32, pub i32);

#[derive(Debug, Clone, Serialize, Deserialize, TypeId)]
pub struct AddResponse(pub i32);
````

Even you are a Rust veteran you may still want to ask what does the TypeId mean? or what does response(()) mean?

A type with `TypeId` derive means it could be sent throw actor calls, it is always ok to be used a a response but it still need a response type to be used as a request, since a request should have a response type.

Note that a response of the unit type `()` is logically the same as what it means in rust's type system. `()` means the value exists, but without any state provided, as opposite to the never type `!`, which means it does not ever exist. If some operation results in `Ok(())`, it at least means the operation is completed successfully.

A request must have a response, and a `#[response(())]` enables it to be used as a request, that returns a `()` as response. By default, a type with `TypeId` derive do not attach a response type, which means it cannot be used as a request, unless an explicit `#[response(SomeResponseType)]` is provided.  But as for types named with `Request` suffix, which means it must be a request type and should have a response type, it automatically attaches a response type definition to the same name with the `Request` suffix replaced to `Response`. A macro cannot check whether a type with some name exists, therefore missing of this response type would be a compiling error.

As for types named with `Request` suffix but also with a `#[response(SomeResponseType)]`, the default behavior is overridden, since the explicit attribute always have the highest priority.

Eg.

````

#[derive(TypeId)]  
struct SomeNormalType; // No response type defined, so that it could only be used as a response

#[derive(TypeId)]  
#[response(())]  
struct SomeOtherNormalType; // This type could be used as a request with response of type `()`

#[derive(TypeId)]  
struct SomeRequest; // This binds `SomeResponse` as its response type

#[derive(TypeId)]  
struct SomeResponse; // No response type defined, so that it could only be used as a response, as what it is

#[derive(TypeId)]  
struct SomeOtherRequest; // This binds `SomeOtherResponse` as its response type, but `SomeOtherResponse` does not exist, so it becomes a compiling error.  
````

The serialized request and response types are identified by the type id as a head of the binary data, so these types have to implement the trait `TypeId`, which works different from the rust's `std::any::TypeId`, whose generated identifier is only guaranteed to be unique within a compilation, but it is obviously insufficient for the types that are to be transmitted through processes and wasm instances that are loaded from different compilations.

My workaround is to define the trait `TypeId` along with its derive macro, which is a pure function evaluated during the compile time, whose only input is the code of the type definition, as a token stream, lexed by the rust compiler, and the only output is an extra token stream in order to emit extra definitions (mainly to implement the trait with the same name as the macro), that are to be compiled along with the original type definition.

What a derive macro do is to parse the token stream into an abstract syntax tree (aka. AST), and analyze the AST to get all information in need to generate extra definitions, such as `impl` blocks. Note that input tokens come with its span information, including the package name, version, module path, filename, line & column number, and whether it is from the hand-written code or generated from a previous macro.

Note that derive macro, as a procedure macro, is evaluated right before the souce being parsed into an AST, while it is far before the typed high-level intermediate representation (aka. THIR) stage, that is where type information is generated. Thus a procedure macro (or anything else in rust code) does not have the ability to scan all types.

As for each type for which a `TypeId` derive is applied, the derive macro generates a definition that `impl`s  `TypeId for` such type, of which the only trait member is a const string that would be used as the unique id among compilations. The derive macro generates the type id by concating the package name with version, the module path, and the name of such type, which could guarantees that, for any crate that reference `tea-sdk` of a certain version, no matter how and where it is compiled, the type id is ensured to be certain and unique.

Additionally, rust allows extra attributes to be attached after the derive macro (aka. helper attributes), as extra arguments for the derive macros. The `TypeId` derive macro looks for `response` helper attributes to indicate whether and `for` what type the derive macro should generate `impl Request`, of which the only trait member is `type Response` that indicates the response type of such type (when used as a request).

The ideal design is to check whether a `Response` suffixed type exists for the `Request` suffixed type, otherwise it uses `()` instead to automatically bind the response type without errors, but as explained above, a procedure macro is unable to access the type information, but only the literal code information of the type definition where the derive macro is attached, as token stream, so it infers the `Response` suffixed name as the response type regardless of whether it exists. Therefore a `#[response(())]` is needed to tell the macro to use `()` instead.

## Better example for Requests and Response definition

The tutorial is mean to be simple and clear. So we intend to have only a few examples of request/response. A better example for different types of request/response would be inside the sdk codebase. It is located inside the sdk code repo, under actorx/examples. The Github link is ` https://github.com/tearust/sdk/blob/master/actorx/examples/codec/src/lib.rs`

Here are the example code at the time of this document was written:

````
#![feature(min_specialization)]

use serde::{Deserialize, Serialize};
use tea_sdk::{actorx::ActorId, serde::TypeId};

pub const WASM_ID: ActorId = ActorId::Static(b"com.tea.examples-actor");
pub const NATIVE_ID: ActorId = ActorId::Static(b"com.tea.time-actor");

#[derive(Serialize, Deserialize, TypeId)]
#[response(())]
pub struct GreetingsRequest(pub String);

#[derive(Serialize, Deserialize, TypeId)]
pub struct AddRequest(pub u32, pub u32);

#[derive(Serialize, Deserialize, TypeId)]
pub struct AddResponse(pub u32);

#[derive(Serialize, Deserialize, TypeId)]
pub struct GetSystemTimeRequest;

#[derive(Serialize, Deserialize, TypeId)]
pub struct GetSystemTimeResponse(pub u128);

#[derive(Serialize, Deserialize, TypeId)]
pub struct FactorialRequest(pub u64);

#[derive(Serialize, Deserialize, TypeId)]
pub struct FactorialResponse(pub u64);
````

There might be some updates over time. 

In the actor/src/lib.rs you can find how they are implemented:

````
#![feature(min_specialization)]
#![feature(async_fn_in_trait)]
#![allow(incomplete_features)]

use crate::error::Result;
use tea_actorx_examples_codec::{
	AddRequest, AddResponse, FactorialRequest, FactorialResponse, GetSystemTimeRequest,
	GetSystemTimeResponse, GreetingsRequest, NATIVE_ID, WASM_ID,
};
use tea_sdk::{
	actorx::{actor, hooks::Activate, println, ActorId, HandlerActor},
	serde::handle::handles,
};

pub mod error;

actor!(Actor);

#[derive(Default)]
pub struct Actor;

impl HandlerActor for Actor {
	fn id(&self) -> Option<ActorId> {
		Some(WASM_ID)
	}
}

#[handles]
impl Actor {
	async fn handle(&self, _: Activate) -> Result<_> {
		println!("Activate!");
		Ok(())
	}

	async fn handle(&self, GreetingsRequest(name): _) -> Result<_> {
		let GetSystemTimeResponse(time) = NATIVE_ID.call(GetSystemTimeRequest).await?;
		println!("Hello {name}, the system time is {time}.");
		Ok(())
	}

	async fn handle(&self, AddRequest(lhs, rhs): _) -> Result<_> {
		Ok(AddResponse(lhs + rhs))
	}

	async fn handle(&self, FactorialRequest(arg): _) -> Result<_> {
		Ok(FactorialResponse(if arg <= 2 {
			arg
		} else {
			arg * WASM_ID.call(FactorialRequest(arg - 1)).await?.0
		}))
	}
}
````

You can find more examples from other projects. 
