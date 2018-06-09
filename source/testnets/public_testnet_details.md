## Public Testnet Details

### The Open Public Testnet information

- Web wallet: [public testnet](http://testnet.bitshares.eu/)
- Seed node: `testnet.bitshares.eu:11010`
- Chain-id: `39f5e2ede1f8bc1a3a54a7914414e3779e33193f1f5693510e73cb7a87617447`
- [Genesis Block - for Testnet](https://github.com/bitshares/bitshares-core/blob/testnet/genesis.json)
- Sources:
   - [Full Node](https://github.com/bitshares/bitshares-core) (branch testnet)
   - [Wallet](https://github.com/bitshares/bitshares-ui)
   - [Faucet](https://faucet.testnet.bitshares.eu/)
   - [Testnet - Python Scripts](https://github.com/BitSharesEurope/testnet-pythonscripts)
- Full nodes:

In order to run a full node, get the [sources](https://github.com/BitShares/bitshares-core) (branch testnet) and compile. Then run the node with:

    $ programs/witness_node/witness_node --genesis-json=genesis.json

> Warning: On this network, **everything** is allowed. As a consequence the outside price of any token on this blockchain is exactly **zero**. A reset of the blockchain may occur at any moment!

- **Core Unit**: `TEST`
   - 10k TEST for free upon registration

***
