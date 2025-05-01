# Universaldot Node : Blockchain for creating digital economies

[![Check Set-Up & Build](https://github.com/UniversalDot/universal-dot-node/actions/workflows/check.yml/badge.svg)](https://github.com/UniversalDot/universal-dot-node/actions/workflows/check.yml)
[![Test](https://github.com/UniversalDot/universal-dot-node/actions/workflows/test.yml/badge.svg)](https://github.com/UniversalDot/universal-dot-node/actions/workflows/test.yml)

![Logo](https://github.com/UniversalDot/documents/blob/master/logo/universaldot-logo/rsz_jpg-02.jpg)

In the past, people have created organizations and corporations by obtaining legal status from a government or a state. In the future, organizations and corporations will be created digitally and will have global instead of local reach. UNIVERSALDOT Foundation provides the needed infrastructure for people to organize themselves by creating digital identities and tasks. We enable the creation of a new digital future.

This repository provides Node implementation based [Substrate](https://www.substrate.io/) node. FRAME pallets are imported via git from our own [pallets](https://github.com/UniversalDot/pallets) repository.


## Pallets

- Profile - Enables users to create unique profile as identity
- Task - Creates the interaction between different users. Some users need some tasks to completed while others wish to complete tasks.
- Dao - Complex Task require more effort from a community rather than single users. This is accomplished by creating decentralized autonomous organizations.
- Did - Allows transfer of assets to other Accounts.
- Grant - Allows grants to be requested by accounts that have 0 balance. Grants are awarded each block to random grant requesters from a Treasury Account. 


## Getting Started


<!-- ### Using Nix

Install [nix](https://nixos.org/) and optionally [direnv](https://github.com/direnv/direnv) and
[lorri](https://github.com/target/lorri) for a fully plug and play experience for setting up the
development environment. To get all the correct dependencies activate direnv `direnv allow` and
lorri `lorri shell`. -->

### Rust Setup

First, complete the [basic Rust setup instructions](./docs/rust-setup.md).

### Run

Use Rust's native `cargo` command to build and launch the template node:

```sh
cargo run --release -- --dev
```

### Build

The `cargo run` command will perform an initial build. Use the following command to build the node
without launching it:

```sh
cargo build --release
```

### Embedded Docs

Once the project has been built, the following command can be used to explore all parameters and
subcommands:

```sh
./target/release/node-template -h
```

## Run

The provided `cargo run` command will launch a temporary node and its state will be discarded after
you terminate the process. After the project has been built, there are other ways to launch the
node.

### Single-Node Development Chain

This command will start the single-node development chain with non-persistent state:

```bash
./target/release/node-template --dev
```

Purge the development chain's state:

```bash
./target/release/node-template purge-chain --dev
```

Start the development chain with detailed logging:

```bash
RUST_BACKTRACE=1 ./target/release/node-template -ldebug --dev
```

> Development chain means that the state of our chain will be in a tmp folder while the nodes are
> running. Also, **alice** account will be authority and sudo account as declared in the
> [genesis state](https://github.com/substrate-developer-hub/substrate-node-template/blob/main/node/src/chain_spec.rs#L49).
> At the same time the following accounts will be pre-funded:
> - Alice
> - Bob
> - Alice//stash
> - Bob//stash

In case of being interested in maintaining the chain' state between runs a base path must be added
so the db can be stored in the provided folder instead of a temporal one. We could use this folder
to store different chain databases, as a different folder will be created per different chain that
is ran. The following commands shows how to use a newly created folder as our db base path.

```bash
// Create a folder to use as the db base path
$ mkdir my-chain-state

// Use of that folder to store the chain state
$ ./target/release/node-template --dev --base-path ./my-chain-state/

// Check the folder structure created inside the base path after running the chain
$ ls ./my-chain-state
chains
$ ls ./my-chain-state/chains/
dev
$ ls ./my-chain-state/chains/dev
db keystore network
```


### Connect with Polkadot-JS Apps Front-end

Once the node is running locally, you can connect it with **Polkadot-JS Apps** front-end
to interact with your chain. [Click
here](https://polkadot.js.org/apps/#/explorer?rpc=ws://localhost:9944) connecting the Apps to your
local node.

### Multi-Node Local Testnet

If you want to see the multi-node consensus algorithm in action, refer to our
[Start a Private Network tutorial](https://docs.substrate.io/tutorials/v3/private-network).

## Node Structure

A Substrate project such as this consists of a number of components that are spread across a few
directories.

### Node

A blockchain node is an application that allows users to participate in a blockchain network.
Substrate-based blockchain nodes expose a number of capabilities:

- Networking: Substrate nodes use the [`libp2p`](https://libp2p.io/) networking stack to allow the
  nodes in the network to communicate with one another.
- Consensus: Blockchains must have a way to come to
  [consensus](https://docs.substrate.io/v3/advanced/consensus) on the state of the
  network. Substrate makes it possible to supply custom consensus engines and also ships with
  several consensus mechanisms that have been built on top of
  [Web3 Foundation research](https://research.web3.foundation/en/latest/polkadot/NPoS/index.html).
- RPC Server: A remote procedure call (RPC) server is used to interact with Substrate nodes.

There are several files in the `node` directory - take special note of the following:

- [`chain_spec.rs`](./node/src/chain_spec.rs): A
  [chain specification](https://docs.substrate.io/v3/runtime/chain-specs) is a
  source code file that defines a Substrate chain's initial (genesis) state. Chain specifications
  are useful for development and testing, and critical when architecting the launch of a
  production chain. Take note of the `development_config` and `testnet_genesis` functions, which
  are used to define the genesis state for the local development chain configuration. These
  functions identify some
  [well-known accounts](https://docs.substrate.io/v3/tools/subkey#well-known-keys)
  and use them to configure the blockchain's initial state.
- [`service.rs`](./node/src/service.rs): This file defines the node implementation. Take note of
  the libraries that this file imports and the names of the functions it invokes. In particular,
  there are references to consensus-related topics, such as the
  [longest chain rule](https://docs.substrate.io/v3/advanced/consensus#longest-chain-rule),
  the [Aura](https://docs.substrate.io/v3/advanced/consensus#aura) block authoring
  mechanism and the
  [GRANDPA](https://docs.substrate.io/v3/advanced/consensus#grandpa) finality
  gadget.

After the node has been [built](#build), refer to the embedded documentation to learn more about the
capabilities and configuration parameters that it exposes:

```shell
./target/release/node-template --help
```

### Runtime

In Substrate, the terms
"[runtime](https://docs.substrate.io/v3/getting-started/glossary#runtime)" and
"[state transition function](https://docs.substrate.io/v3/getting-started/glossary#state-transition-function-stf)"
are analogous - they refer to the core logic of the blockchain that is responsible for validating
blocks and executing the state changes they define. The Substrate project in this repository uses
the [FRAME](https://docs.substrate.io/v3/runtime/frame) framework to construct a
blockchain runtime. FRAME allows runtime developers to declare domain-specific logic in modules
called "pallets". At the heart of FRAME is a helpful
[macro language](https://docs.substrate.io/v3/runtime/macros) that makes it easy to
create pallets and flexibly compose them to create blockchains that can address
[a variety of needs](https://www.substrate.io/substrate-users/).

Review the [FRAME runtime implementation](./runtime/src/lib.rs) included in this template and note
the following:

- This file configures several pallets to include in the runtime. Each pallet configuration is
  defined by a code block that begins with `impl $PALLET_NAME::Config for Runtime`.
- The pallets are composed into a single runtime by way of the
  [`construct_runtime!`](https://crates.parity.io/frame_support/macro.construct_runtime.html)
  macro, which is part of the core
  [FRAME Support](https://docs.substrate.io/v3/runtime/frame#support-crate)
  library.

### Pallets

The runtime in this project is constructed using many FRAME pallets that ship with the
[core Substrate repository](https://github.com/paritytech/substrate/tree/master/frame) and a
pallets that is [defined in the `pallets`](https://github.com/UniversalDot/pallets) repository.

A FRAME pallet is compromised of a number of blockchain primitives:

- Storage: FRAME defines a rich set of powerful
  [storage abstractions](https://docs.substrate.io/v3/runtime/storage) that makes
  it easy to use Substrate's efficient key-value database to manage the evolving state of a
  blockchain.
- Dispatchables: FRAME pallets define special types of functions that can be invoked (dispatched)
  from outside of the runtime in order to update its state.
- Events: Substrate uses [events and errors](https://docs.substrate.io/v3/runtime/events-and-errors)
  to notify users of important changes in the runtime.
- Errors: When a dispatchable fails, it returns an error.
- Config: The `Config` configuration interface is used to define the types and parameters upon
  which a FRAME pallet depends.

### Run in Docker

First, install [Docker](https://docs.docker.com/get-docker/) and
[Docker Compose](https://docs.docker.com/compose/install/).

We publish the latest standalone node to the [Docker Hub](https://hub.docker.com/r/universaldot/node), from where it can be pulled and ran locally with relatively low effort and high compatibility.

To pull the image locally, run the following command in your terminal.

```bash
docker pull universaldot/node
```

Furthermore, we provide a [Docker-Compose](https://github.com/UniversalDot/compose-service) service that is able to start a blockchain with basic front-end application. 

Then run the following command to start a single node development chain.

```bash
./scripts/docker_run.sh
```

This command will firstly compile your code, and then start a local development network. You can
also replace the default command
(`cargo build --release && ./target/release/node-template --dev --ws-external`)
by appending your own. A few useful ones are as follow.

```bash
# Run Substrate node without re-compiling
./scripts/docker_run.sh ./target/release/node-template --dev --ws-external

# Purge the local dev chain
./scripts/docker_run.sh ./target/release/node-template purge-chain --dev

# Check whether the code is compilable
./scripts/docker_run.sh cargo check
```

#### Development
A `develop` image is also available, which is automatically updated on each push to the `develop` branch.
To pull/update the image locally, run the following command in your terminal.

    docker pull universaldot/node:develop

To run the image interactively, exposing the port and removing the container on exit:

    docker run -it --rm -p 9944:9944 universaldot/node:develop

Or alternatively to use a single container to preserve any data during development:

    docker run -d -p 9944:9944 --name node universaldot/node:develop

### Regenerate Weights for pallets

- Each pallet task, profile and dao contains weights for extrinsics in `weights.rs` file for respective pallet directory.
- `weights.rs` contains command to regenerate weights. The command looks like following:

```bash
./target/release/node-template benchmark pallet --chain dev --execution wasm --wasm-execution compiled --pallet 'pallet_profile' --extrinsic '*' --steps 100 --repeat 50 --output ./pallets/profile/src/weights.rs --template .maintain/frame-weight-template.hbs
```

or

```bash
./target/release/node-template benchmark --chain dev --execution wasm --wasm-execution compiled --pallet 'pallet_profile' --extrinsic '*' --steps 100 --repeat 50 --output ./pallets/profile/src/weights.rs --template .maintain/frame-weight-template.hbs
```

depending on cli version.

- Make sure node is built in release mode with runtime-benchmark features enabled.
    `cargo build --release --features runtime-benchmarks`
- For more information on benchmarking including recommended hardware check https://docs.substrate.io/v3/runtime/benchmarking/


