# SSV-Subgraph

This Subgraph is indexing the [SSVNetwork smart contract](https://goerli.etherscan.io/address/0xC3CD9A0aE89Fff83b71b58b6512D43F8a41f363D) through the Events emitted.

## Install dependencies

In your terminal, from the root folder of this project, run:

```sh
yarn install
```

## Deploy

### Deploy to Subgraph studio

#### Authenticate with Subgraph Studio

To obtain a deploy key, navigate to [Subgraph Studio](https://thegraph.com/studio/). You can also create your Subgraph from the WebApp and skip the [next step](#create-a-subgraph-on-studio).

In your terminal, run:

```
graph auth --studio <STUDIO_KEY>  
```

#### Create a Subgraph on Studio

In your terminal run:

```
graph create --studio <SUBGRAPH_NAME>
```

Where `<SUBGRAPH_NAME>` is the name you are choosing for your Subgraph

#### Deploy

In your terminal run:

```
graph deploy --studio <SUBGRAPH_NAME>
```

Where `<SUBGRAPH_NAME>` is the name you are choosing for your Subgraph

#### Deploy this Subgraph for Mainnet

##### Obtain Mainnet ABI

Make sure to download Mainnet's smart contracts ABI [from here](https://github.com/bloxapp/ssv-network/tree/contract-abi/docs/mainnet/v1.0.0) and substitute it to the [`SSVNetwork.json` file in the `abis` directory](//abis/SSVNetwork.json).

##### Rebuild the project for Mainnet

The `networks.json` file contains a series of configurations specific to each network where the SSV Network smart contracts have been deployed. To switch from one to the other, simply run this command in your terminal:

```sh
graph build --network mainnet
```

##### Deploy the project for Mainnet

Using the same network configuration file, it is possible to deploy for different networks. For example, to deploy for Mainnet, run this command in your terminal:

```sh
graph deploy --network mainnet --studio <SUBGRAPH_NAME>
```

### Deploy Subgraph locally

(see [this section](#run-local-graph-node) for how to run a local Graph node)

To deploy the Subgraph to the local node, use the following command:

```bash
graph deploy --node http://localhost:8020 --ipfs http://localhost:5001 ssv-network
```

Wait for the Subgraph to index⏳

## Query indexed data

The Subgraph provides a GraphQL playground at the following URL: [http://localhost:8000/subgraphs/name/ssv-network/](http://localhost:8000/subgraphs/name/ssv-network/)

### Query example

Test the Subgraph by running this query:

```graphql
query MyQuery {
  validators(where: {owner_: {id: "0xaA184b86B4cdb747F4A3BF6e6FCd5e27c1d92c5c"}}) {
    id
    owner {
      id
    }
    operators {
      id
    }
    cluster {
      id
    }
    active
  }
  clusters(where: {owner_: {id: "0xaA184b86B4cdb747F4A3BF6e6FCd5e27c1d92c5c"}}) {
    id
    owner {
      id
    }
    operatorIds
    validatorCount
    balance
    active
  }
}
```

## Run local Graph Node

The Subgraph can be deployed on the Graph Network, but for development purposes, a local Graph Node has been utilized. A guide on running a [local Graph Node on Docker can be found here](https://github.com/graphprotocol/graph-node/tree/master/docker).

### Note on M1 chips

- TheGraph's official image is not M1 optimized
    - A “Full” Docker was tested, but didn't work because the node's container kept crashing
- I have tried to build an M1-optimized image, [as detailed here](https://github.com/graphprotocol/graph-node/tree/master/docker#running-graph-node-on-an-macbook-m1), it failed with `error: could not compile graph-node`
- In the end, solution was to run ipfs and postgres in Docker, while compiling and running Graph Node ([as suggested here](https://github.com/graphprotocol/graph-node/issues/2325#issuecomment-944844838))

```bash
cargo run -p graph-node --release -- \
  --postgres-url postgresql://graph-node:let-me-in@localhost:5432/graph-node \
  --ethereum-rpc https://goerli.infura.io/v3/c0c2ce3b9a6f43b4b6dd9f1a7b002fc7 \
  --ipfs 127.0.0.1:5001
```

## Project scaffolding

The initial version of the Subgraph was automatically generated by the `init` scaffolding command:

```bash
graph init --allow-simple-name --node http://localhost:8020 \
--from-contract 0xC3CD9A0aE89Fff83b71b58b6512D43F8a41f363D \
--network goerli --abi ~/dev/ssv-network/SSVNetwork.json \
--protocol ethereum --contract-name SSVNetwork --index-events \
--start-block 9203578 ssv-network ssv-subgraph
```

> ⚠️ Make sure to download the contracts ABI ([obtained here](docs.ssv.network/developers/smart-contracts)) and provide the path to it via the `--abi` parameter.
