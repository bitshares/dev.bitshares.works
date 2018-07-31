## Public Testnet - Quick Start Guide

#### Table of Contents:
- [Use UI Wallet on Public Testnet](/core/testnets/public_testnet_details.md#use-the-ui-wallet-on-public-testnet)
- [Use CLI on Public Testnet](/core/testnets/public_testnet_details.md#use-the-cli-on-public-testnet)
- [Other References](/core/testnets/public_testnet_details.md#other-references)

***

BitShares has Public Testnet for UI Web Wallet and CLI. The following is the instructions. 

### Use the UI Wallet on Public Testnet

1. Go to a Web Wallet - Public testnet: (http://testnet.bitshares.eu/) 
2. Create an account
   - [CREATE ACCOUNT] button is for a CLOUD Wallet 
   - [advanced form] button is for a LOCAL Wallet
3. Receive Testnet Cote asset *TEST*.
   - You will have `10k TEST` in your account for free upon registration.
4. Set a Faucet address
   - Faucet: (https://faucet.testnet.bitshares.eu/)
   - Go to [Setting] menu - [Faucet] and set the above Faucet address 
  
> Faucet role: The faucet address is used to pay the registration fee for new users.

*** 

### Use the CLI on Public Testnet

In this section, we show you how to set up on **Ubuntu 16.04 LTS(64 bit)** environment as an example.

More options to see:[Installation Guide](/core/installation/README.md#installation)

1. Install Dependencies

         sudo apt-get update
         sudo apt-get install autoconf cmake git libboost-all-dev libssl-dev g++ libcurl4-openssl-dev

2. Build BitShares Core - Select **testnet** branch

*In order to run a full node, get the sources (branch testnet) and compile*

         git clone https://github.com/bitshares/bitshares-core.git bitshares-core-testnet
         cd bitshares-core-testnet    
         git checkout testnet
         cmake

3. After compile, run the node with: 

        $ programs/witness_node/witness_node --genesis-json=genesis.json

> Warning: On this network, **everything** is allowed. As a consequence, the outside price of any token on this blockchain is exactly **zero**. A reset of the blockchain may occur at any moment!


***

### Other References

- [Testnet - Python Scripts](https://github.com/BitSharesEurope/testnet-pythonscripts)

- Seed node: `testnet.bitshares.eu:11010`
- Chain-id: `39f5e2ede1f8bc1a3a54a7914414e3779e33193f1f5693510e73cb7a87617447`
- [Genesis Block - for Testnet](https://github.com/bitshares/bitshares-core/blob/testnet/genesis.json)

***



