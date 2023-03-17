# Install Dev Environment

## Install prerequisites
To install the dev environment in your local machine, you'll need some prerequisite software already installed:

- Git
- Docker: install docker and "docker compose"
- Node.js
- Rust

If you haven't installed wasm32-unknown-unknown target or the nightly version, you might be prompted to `rustup` and install them according to the instructions.

- `rustup target add wasm32-unknown-unknown`
- `rustup install nightly`

One of the build scripts that are used in the following tutorials might prompt that `protoc` couldn't be found. You can [follow the instructions to install it](https://grpc.io/docs/protoc-installation/#install-using-a-package-manager):

- **On a Mac**: `brew install protobuf`
- **On Ubuntu**: `apt install -y protobuf-compiler

## Troubleshooting the Dev Environment
The most common cause of errors during the following tutorials generally either arise from an improperly configured local environment or from not having the most up to date repo.

- Misconfigured local environments can be fixed by following the error messages requesting missing packages. The prerequisites at the top of this document should be sufficient but everyone's local development environment will be unique.
- `git pull` your current tutorial's repo to make sure you have the latest files.

If you either change your local software environment or update your local repos, you'll need to stop any existing TEA runtime and docker images.  These are the steps for refreshing the `dev-runner` repo and associated docker images:

- Press CTRL-C to stop the TEA runtime server.
- From the root of the dev-runner directory, issue the following command: `docker-compose down`
-  Use `docker images` to see the currently running docker images. 
- Use `docker rmi the_images_of_the_two_:dev_image` to fully remove the existing older images  (look for the two images with "dev" tag running under the tearust repo:
	docker rmi tearust/parent-instance-client:dev
	docker rmi tearust/runtime:dev
- After you've built the tutorials using your latest updates you'd run `docker-compose up` from the `dev-runner` directory.