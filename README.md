![DIF Logo](https://raw.githubusercontent.com/decentralized-identity/universal-resolver/master/docs/logo-dif.png)

# Universal Resolver Driver: `did:lto`

This is a [Universal Resolver](https://github.com/decentralized-identity/universal-resolver/) driver for **did:lto** identifiers.

## Specifications

* [Decentralized Identifiers](https://w3c.github.io/did-core/)
* [LTO Network DID documentation](https://docs.ltonetwork.com/protocol/identities/decentralized-identifiers)

## Example DID

```
did:lto:3JugjxT51cTjWAsgnQK4SpmMqK6qua1VpXH
```

## Environment variables
The universal resolver can connect to one testnet node and one mainnet node. The nodes MUST run the [LTO indexer service](https://github.com/ltonetwork/indexer) with DID indexing enabled.

### LTO_TESTNET_NODE
Defines the URL to index service of the LTO testnet node. eg https://testnet.lto.network/index.

### LTO_MAINNET_NODE
Defines the URL to index service of the LTO mainnet node. eg https://nodes.lto.network/index.

## Build and Run

### Docker compose

When using docker-compose, the setup will include a full node and indexer for both testnet and mainnet. This is recommended for production.

```
docker compose up
curl -X GET http://localhost:8080/1.0/identifiers/did:lto:3N39xg4xauubonEWf2BUCp1DmGGQ3U21jJT
```

### Docker run

With docker run, it's possible to connect to existing nodes.

```
docker build -f ./Dockerfile . -t ltonetwork/uni-resolver-driver-did-lto

export LTO_TESTNET_NODE=https://testnet.lto.network/index
export LTO_MAINNET_NODE=https://nodes.lto.network/index
docker run -p 8080:8080 ltonetwork/uni-resolver-driver-did-lto

curl -X GET http://localhost:8080/1.0/identifiers/did:lto:3N39xg4xauubonEWf2BUCp1DmGGQ3U21jJT
```

## About

The LTO indexer service is able to resolve DID documents for a specific network. The universal resolver driver is merely a proxy that routes the request to the appropriate node based on the address.

The second byte of the address is the network ID, which is `T` for testnet and `L` for mainnet. The address is base58 encoded, so we can't use this byte directly. However, all addresses starting with `3N` or `3M` are testnet, and addresses starting with `3J` are mainnet.

In addition to proxying the request, the driver also modifies the response body using LUA, replacing the DID resolution metadata.
