# Install Dev Environment

If you haven't installed wasm32-unknown-unknown target or the nightly version, you might be prompted to `rustup` and install them according to the instructions.

* `rustup target add wasm32-unknown-unknown`
* `rustup install nightly`

You might also be prompted that `protoc` couldn't be found. You can [follow the instructions to install it](https://grpc.io/docs/protoc-installation/#install-using-a-package-manager):

* **On a Mac**: `brew install protobuf`
* **On Ubuntu**: \`apt install -y protobuf-compiler
