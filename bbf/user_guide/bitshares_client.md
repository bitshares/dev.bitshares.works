## BitShares Accounts

### Contents:
- [BitShares Client](/bbf/user_guide/bitshares_client.md#bitshares-client)
   - [Light Client](/bbf/user_guide/bitshares_client.md#light-client)
   - [Web Client](/bbf/user_guide/bitshares_client.md#web-client)
- [Wallet & Login Mode](/bbf/user_guide/bitshares_client.md#wallet--login-mode)
   - [Cloud Wallet Login](/bbf/user_guide/bitshares_client.md#cloud-wallet-login)
   - [Local Wallet Login](/bbf/user_guide/bitshares_client.md#local-wallet-login)

***

## BitShares Client
You have sole control of your accounts and funds and they are created on your computer (within the light-client or the browser web-client). Hence, you will only be able to access your account on the computer that you have used to register and create your account, unless you create a backup your wallet and import it somewhere else.

### Light Client
Download client files and install BitShares Wallet to your computer.

- [Download the Official Light Client](http://bitshares.org/download/)
- [BitShares-UI – Latest Release](https://github.com/bitshares/bitshares-ui/releases)

**BitShares-UI**: 
This is a light wallet that connects to a BitShares API provided by the witness_node executable. It stores all keys locally in the browser, never exposing your keys to anyone as it signs transactions locally before transmitting them to the API server which then broadcasts them to the blockchain network. The wallet is encrypted with a password of your choosing and encrypted in a browser database.

### Web Client

Access the network and open the wallet in the browsers via one of our partners:

- wallet.bitshares.org https://wallet.bitshares.org
- OpenLedger: https://openledger.info
- (more...)

***

## Wallet & Login Mode

You might've noticed BitShares has two types of Login Mode. In this section, we will explain those differences.

#### Where to find out your wallet Login Mode

[Settings] - [General] - **LOGIN MODE**

<p align="center">
  <img src="https://github.com/cedar-book/btsdoc-portal/blob/master/bbf/img/login-mode-set.png" width="600" title="Github Logo">
</p>


### Cloud Wallet Login

If you registered with a username and password, you have a cloud wallet. Although nothing is technically stored in the cloud, we use the term Cloud Wallet because **you can use these credentials (username and password) from any web browser at any time to gain access to your account.**

The cloud wallet only allows for a single account to be accessed at a time. This is generally the correct choice for a new user. While is possible to change the auto-generated password, we do not recommend doing so at this time. The team is working on a responsible way to manage the password change that requires no technical knowledge. We will make an announcement once it’s released.

#### Login Form 
*You can login from any browsers.*

<p align="center">
  <img src="https://github.com/cedar-book/btsdoc-portal/blob/master/bbf/img/login-cloud.png" width="380" title="Cloud Login">
</p>

### Local Wallet Login

The local wallet creates a database within your browser. This means that access to your funds it tied to that browser only. If you attempt to access your local wallet from any other computer, or any other browser, it will fail unless you actively import your backup file from the original browser backup file. The process actually easy.

#### Login Form 
*You can only access your funds from the same computer and web browser that you have used to register and create your account.*

<p align="center">
  <img src="https://github.com/cedar-book/btsdoc-portal/blob/master/bbf/img/login-local.png" width="380" title="Local Login">
</p>

***


