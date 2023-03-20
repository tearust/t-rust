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

The purpose of the `dev-runner` repo is to allow any user to recreate the TEA runtime on their local machine. The TEA Project is a decentralized system that needs multiple nodes for both its hosting infrastructure and [state machine](../../z_glossary/state_machine.md) where a [remote attestation](../../z_glossary/remote_attestation.md) process ensures that all nodes are trustable. In the simplified local runtime, there's only one [hosting node](../../z_glossary/hosting_cml.md) ("B-node") and one state machine node ("A-node"). And there's no remote attestation since this is a simulated environment with single nodes on each level.

After you have dowloaded the `dev-runner` to your local computer, note that other repos will possibly interact with your local **dev-runner** environment to the sibling folder. For example, `tutorial-v1` will write a wasm file to `dev-runner` during the build process for that tutorial. 

## Troubleshooting the Dev Environment

The most common cause of errors during the following tutorials generally either arise from an improperly configured local environment or from not having the most up to date repo.

- Misconfigured local environments can be fixed by following the error messages requesting missing packages. The prerequisites at the top of this document should be sufficient but everyone's local development environment will be unique.
- `git pull` your current tutorial's repo to make sure you have the latest files.

If you either change your local software environment or update your local repos, you'll need to stop any existing TEA runtime docker images.  These are the steps for refreshing the `dev-runner` repo and associated docker images:

- Press CTRL-C to stop dev-runner.
- Delete the `.tokenstate`. This file contains the local state's persistent storage in local dev mode. If you don't delete it, the next time you start the dev-runner will continue from the last saved [state](../../z_glossary/state.md) . This may cause conflicts if you've updated your code logic.
- If there are any updates of the dev-runner docker images, use `docker pull the_images_of_the_two_:dev_image` to update the existing older images  if there's an updated image available:
```
docker pull tearust/parent-instance-client:dev
docker pull tearust/runtime:dev
```
- After you've built the tutorials using your latest updates you'd run `docker-compose up` from the `dev-runner` directory.