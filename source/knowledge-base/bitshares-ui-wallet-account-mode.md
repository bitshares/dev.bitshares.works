# BitShares UI - How to create and manage Cloud wallet and/or Local wallet Accounts 

This document purpose: To explain BitShares UI wallet Client and wallet types and answer some of frequently asked questions about Wallet and Account.  And also, show how to create a Cloud wallet and a Local wallet. 

#### Contents:
- [BitShares Client](/source/knowledge-base/bitshares-ui-wallet-account-mode.md#bitshares-client)
  - Lite Client
  - Web Client
- [BitShares Wallet Types and Login](/source/knowledge-base/bitshares-ui-wallet-account-mode.md#bitshares-wallet-types-and-login)
  - Cloud Wallet 
  - Local Wallet
- [Frequently asked Questions](/source/knowledge-base/bitshares-ui-wallet-account-mode.md#frequently-asked-questions)
- [Light wallet or Web wallet?](/source/knowledge-base/bitshares-ui-wallet-account-mode.md#light-wallet-or-web-wallet)
- [How to Create an Wallet](/source/knowledge-base/bitshares-ui-wallet-account-mode.md#how-to-create-an-wallet) (Cloud wallet) 
- [How to Create an Wallet](/source/knowledge-base/bitshares-ui-wallet-account-mode.md#how-to-create-an-wallet---advanced) - Advanced (Local wallet)

***

## BitShares Client
- BitShres wallet has two client types. 
  - **Light Client** : Download and install BitShares wallet to your computer. **This does not mean that you will have a Local wallet.**
    - [BitShares-UI – Latest Release](https://github.com/bitshares/bitshares-ui/releases)
  - **Web Client** : Access the network and open the wallet in the browsers.
    - wallet.bitshares.org https://wallet.bitshares.org
  
## BitShares Wallet Types and Login   

### Cloud Wallet
- BitShare UI wallet will create a **Cloud wallet** as a default wallet. (i.e., [CREATE ACCOUNT])
- The Cloud wallet allows you to login from any web browser at any time to gain access to your account by using your credentials (username and password).
- The Cloud wallet only allows for a single account to be accessed at a time. 
- If you have a Cloud wallet, you don't need to worry about a backup. (*You don't have the functionality in the Cloud wallet*).
- **You cannot just switch from a Cloud wallet to a Local wallet.** 
  - Even you import Private keys (was in the Cloud wallet) to the Local wallet, you do not have a brain key to associate with the Private keys you imported. Therefor, a brainkey restore won't find those Private keys. (No meaning to do a brainkey backup and restore.)
  
### Local Wallet
- **If you know you want to have a Local wallet, use an [advanced form] link on the Welcome to BitShares form and create a backup file.** 
- The Local wallet creates a correct pair of keys (a brainkey and private keys) and save the information to your browser.
- The Local wallet creates a database with in your browser. This means that you can only access your funds from the same computer and web browser that you have used to register and create your account.  If you attempt to access your local wallet from any other computer, or any other browser, it will fail unless you actively import your backup file from the original browser backup file.
- You have to create a backup files to manage the Cloud wallet account. 
- The Cloud wallet has Backup options. Go to [Settings] - [Backup] to find. 
  - **Create local wallet backup** : create a Binary File (.bin) backup.
  - **Create brainkey backup** : give you long random phrases. You need to write down and keep it in a safe place.
  - The backup files can be used to move your local wallet to different computers or different browsers. In order to restore your local wallet you will need the backup file and your password! Therefor, it’s extremely important you create a backup and keep a safe place.
  
***

## Frequently asked Questions
- **Can I switch (change the Wallet Mode or import private keys) my Cloud wallet to a Local wallet?**
  - No.
- **I have a Cloud wallet. Can I have a Local wallet?** 
  - Yes. But you will have to create new account for the Local wallet. 
- **How can I move my funds from a Cloud wallet to a Local wallet?**
  - We mentioned before. You have to create new account for the Local wallet. You can create the Local wallet by using an [**advanced form**] link on Welcome to BitShares form. After you created new Local wallet, send your funds from your old account (Cloud wallet) to new account (Local wallet). And create a backup!!
- **Can I change a Cloud wallet password?** 
  - Yes. 
  - Go to [How to change a password if using a Cloud Wallet](https://github.com/bitshares/bitshares-ui/wiki/Cloud-Wallet-Login-and-changing-password) : from BitShares UI wiki
- **Can I change a Local wallet password?** 
  - Yes
  - Go to [**Settings**] - [**Password**] - Change your password. Use this page. 
- **There is [Create Account] in a Side navigation menu. Can I create and add new account in the same wallet I logged in?**
  - Yes. However, the account you logged in must have a LifeTime Membership (LTM) stats.

***

## Light wallet or Web wallet?
Before we create a wallet, let’s check which wallet you want to have as your BitShares wallet. Quick check the below chart.

<p align="center">
  <img src="/source/imgs/ui-login/BitShares-wallet-flow.png" width="620" title="welcome_bitshares">
</p>

***

## How to Create an Wallet 
This example will vreate a **Cloud wallet**.

This is very important. Please create a strong password and **write down** or **save** it to a safe place!! Do not lose it. **ONLY you can open your wallet again. No one can help.**


<p align="center">
  <img src="/source/imgs/ui-login/welcome-bitshares1.png" width="340" title="cloud_wallet">
</p>

- 1.Click [**CREATE ACCOUNT**]

It opens a next form to input your BitShares account name and a password. 

- 2.Type in [**ACCOUNT NAME**].  You can create your unique BitShares account name.
- 3.Set a password. You can use a _GENERATED PASSWORD_ or create own strong password.
- 4.Type or paste your password to confirm.
- 5.Check the check boxes. **Make sure you read before you check!**
- 6.Click [**CREATE ACCOUNT**]

<p align="center">
  <img src="/source/imgs/ui-login/create-account1.png" width="280" title="welcome_bitshares">
</p>

Before you submit, check your password one last time. It's very important!!
 
- 7.Click **[SHOW ME MY PASSWORD**] and double check if you have a correct password.
- 8.Click [**OK,TAKE ME TO THE DASHBOARD**]

<p align="center">
  <img src="/source/imgs/ui-login/create-account4.png" width="280" title="cloud_wallet">
</p>

#### Login Form for Cloud wallet

<p align="center">
  <img src="/source/imgs/ui-login/login-cloud-wallet2.png" width="460" title="cloud_wallet">
</p>


***

## How to Create an Wallet - Advanced
This example will create a **Local wallet**.

<p align="center">
  <img src="/source/imgs/ui-login/local-login1.png" width="340" title="local_wallet">
</p>

- 1.Click [**advanced form]**



- 2.Type in [**ACCOUNT NAME**]. You can create your unique BitShares account name.
- 3.Set a password. Create own strong password.
- 4.Type or paste your password to confirm.
- 5.Click [**CREATE ACCOUNT**]

**If this is yore first account, a faucet will pay the registration fee for you!**

<p align="center">
  <img src="/source/imgs/ui-login/local-login2a.png" width="280" title="local_wallet">
</p>

**Your Web Browser is your Wallet: Please read the information below on the form.**

<p align="center">
  <img src="/source/imgs/ui-login/local-login2b.png" width="280" title="local_wallet">
</p>

- Click [**CREATE BACKUP NOW**]

<p align="center">
  <img src="/source/imgs/ui-login/local-login3.png" width="280" title="local_wallet">
</p>

- Click [**DOWNLOAD**] to save a backup (.bin) file

<p align="center">
  <img src="/source/imgs/ui-login/local-login4.png" width="280" title="local_wallet">
</p>

Congratulation, you’re ready!

<p align="center">
  <img src="/source/imgs/ui-login/local-login5.png" width="280" title="local_wallet">
</p>

#### Login Form for Local wallet

<p align="center">
  <img src="/source/imgs/ui-login/login-local2.png" width="460" title="local_wallet">
</p>



***

Thank you for @svk!

***
