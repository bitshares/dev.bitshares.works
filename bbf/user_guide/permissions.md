## Permissions
### Contents:
- [Permissions - in Wallet Settings](/bbf/user_guide/permissions.md#permissions---in-wallet-settings)
   - Permissions Tabs
- [Public Key and Private Key](/bbf/user_guide/permissions.md#public-key-and-private-key)
   - Where are they? 
- [Cloud Wallet tab option](/bbf/user_guide/permissions.md#cloud-wallet-tab-option)

***
In BitShares, each account is separated into

- **Active Permission**: Allows to access funds and some account settings, but cannot change the owner permission and is thus considered the “online” permissions.
- **Owner Permission**: This permission has administrative powers over the whole account. 

Both can be defined in the Permissions tab of your account using so called *authorities* together with a so called *reshold* that has to be exceeded in order for a transaction to be valid.

**Authorities:** In BitShares an *authority* consists of one or many entities that authorize an action, such as transfers or trades.  An authority consists of one or several pairs of an account name with a *weight*.

***

## Permissions - in Wallet Settings

<p align="center">
  <img src="https://github.com/cedar-book/btsdoc-portal/blob/master/bbf/img/permissions-active2.png" width="650" title="Permissions">
</p>

#### Permissions Tabs

| | tab |     |
|--|----------|----------|
| 1 | Active Permissions | Active permissions define the accounts that have permission to spend funds for this account. |
| 2 | Owner Permissions | Owner permissions define who has control over the account. Owners may overwrite all keys and change any account settings. |
| 3 | Memo Key | The memo key is where you receive memos, in order to decode the memos you need to control the private key for the public key. <br/> By using a public/private key pair without spending authority, you may give read-only access to your memos to third parties. |
| 4 | Cloud Wallet | If you want to be able to login from anywhere using only your password and account name, you can use this page to add new keys to your permissions. If you also want to remove the original keys, you can do so in the active/owner permissions tabs. <br/> The cloud wallet uses a password in combination with your account name to generate your private keys on the fly. It restricts you to only logging in with one account at a time, in return for ease of use and convenience. |

*** 
## Public Key and Private Key

### Where are they?

We can find each Public key in the Permissions page. We can also find each Private key on the same page. In this section, we will show you how to find your private key.  **They are your private keys, please keep secret.**

<p align="center">
  <img src="https://github.com/cedar-book/btsdoc-portal/blob/master/bbf/img/permissions-active3.png" width="650" title="Permissions Keys">
</p>

### How to find each private key

> Note: If you cannot click nor find a link, *login* to your wallet. 

#### Active and Owner – Private keys
- Click a private key number or a key image. 
- A Private key viewer form opens. You will find a Public Key and a [SHOW] button like below.
- Click the [SHOW] button. You will find your Private key under the Public key. 

<p align="center">
  <img src="https://github.com/cedar-book/btsdoc-portal/blob/master/bbf/img/permissions-active4b.png" width="500" title="Permissions popup">
</p>

#### Memo – Private keys
- Click a key image. (It seems a private key number does not have a link.)
- A Private key viewer form opens. You will find a Public Key and a [SHOW] button like below.
- Click the [SHOW] button. You will find your Private key under the Public key.  


***

## Cloud Wallet tab option
(*working in progress*)

#### How to use this option
<p align="center">
  <img src="https://github.com/cedar-book/btsdoc-portal/blob/master/bbf/img/permissions-cloud2.png" width="650" title="Permissions">
</p>


***
