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

## Install local development environment: Dev-Runner

**GEORGE**  TODO://

A few points George, make sure you have them
- the purpose of dev-runner
- clone to the sibling folder. Make sure the dev-runner and tutorial-v1 side by side


## Troubleshooting the Dev Environment

The most common cause of errors during the following tutorials generally either arise from an improperly configured local environment or from not having the most up to date repo.

- Misconfigured local environments can be fixed by following the error messages requesting missing packages. The prerequisites at the top of this document should be sufficient but everyone's local development environment will be unique.
- `git pull` your current tutorial's repo to make sure you have the latest files.

If you either change your local software environment or update your local repos, you'll need to stop any existing TEA runtime docker images.  These are the steps for refreshing the `dev-runner` repo and associated docker images:

- Press CTRL-C to stop the dev-runner.
- Delete the `.tokenstate`. This file contains the local state persistent storage in local dev mode. If you do not delete it, next time when you start the dev-runner, it will continue the state from last time. It may cause conflict if you update your code logic.
- If there is any update of dev-runner docker image, use docker pull the_images_of_the_two_:dev_image to update the existing older images  if there's an updated image available:
```
docker pull tearust/parent-instance-client:dev
docker pull tearust/runtime:dev
```
- After you've built the tutorials using your latest updates you'd run `docker-compose up` from the `dev-runner` directory.