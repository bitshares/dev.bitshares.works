## Traders


#### Table of Contents:


Blockchain-based decentralized exchanged (DEX) are slightly different to centralized exchanges and as a consequence, dealing with the DEX programmatically via APIs differs from centralized approaches as well. However, our developers have put quite some efforts into making the DEX as easy to use as their centralized counterparts and offer an API to the public exchange data that is very similar. 

However, private exchange APIs are different due to the fact that no entity except yourself can access your funds. For this reason, trading in the DEX requires that you have the private key to your account installed in an application that can construct and sign the corresponding transactions for you. One of these applications is the CLI Wallet which, after installation and configuration, offers a your own private API.


## Public API

The best way to get public data on markets is via websocket connection to a public full-node which provides traders with

- a ticker
- order books
- trade history
- and more.

BitShares Public Full Nodes:[the latest List](https://github.com/bitshares/bitshares-ui/blob/staging/app/api/apiConfig.js#L67)

#### How to interact
- [Websocket Calls & Notifications](/core/api/websocket_calls_notifications.md#websocket-calls-notifications) : Interface with Graphene-based blockchain (e.g. BitShares) 
- [Blockchain API](https://github.com/cedar-book/core.dev/tree/master/core/api#blockchain-apis) :  a list of available calls

## Private API

As briefly mentioned above, trading in the DEX programmatically requires that you run your own CLI Wallet. Here is the information how to use the [Cli-Wallet and configure](/core/wallet/cli_wallet.md#connecting-a-wallet) it properly so that it can be used as a **private API server**.

After installation and configuration of the private API, we can use RPC to create orders, cancel orders, create and adjust call orders, and more. The CLI wallet offers a wide range of [Wallet API Calls](https://bitshares.org/doxygen/classgraphene_1_1wallet_1_1wallet__api.html) that can be used to mange your account and trade in the DEX.

#### [Wallet API Calls](https://bitshares.org/doxygen/classgraphene_1_1wallet_1_1wallet__api.html)  

- General Calls
- Wallet Calls
- Account Calls
- Trading Calls
- Asset Calls
- Governance
- Privacy Mode
- Blockchain Inspection
- Transaction Builder


### Supporting Libraries

Python Module for the DEX
- [pybitshares](http://pybitshares.com/) offers a Trading module specifically for the decentralized exchange (DEX).


***

(ref)
- http://docs.bitshares.org/integration/traders/index.html?highlight=traders





