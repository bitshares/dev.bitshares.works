## Permissions
#### Table of Contents:

- [Permissions - in Wallet Settings](#permissions---in-wallet-settings)
  - [Permissions Tabs](#permissions-tabs)
- [Public Key and Private Key](#public-key-and-private-key)
  - [Where are public/private keys? ](#where-are-publicprivate-keys)
  - [How to find each private key](#how-to-find-each-private-key)
  - [How to change Cloud wallet password](#how-to-change-cloud-wallet-password)

***
In BitShares, each account is separated into

|   permission type       |  note            |
| ------------------- |---------- |
| Owner Permission    | This permission has administrative powers over the whole account and should be considered for ‘backup’ strategies. |                           
| Active Permission | Allows to access funds and some account settings, but cannot change the owner permission and is thus considered the “online” permissions.|     

Both can be defined in the Permissions tab of your account using so called *authorities* together with a so called *threshold* that has to be exceeded in order for a transaction to be valid.

- **Authorities** : In BitShares an authority consists of one or many entities that authorize an action, such as transfers or trades. 

  - An authority consists of one or several pairs of an account name with a weight.
  - In order to obtain a valid transaction, the sum of the weights from signing the parties has to exceed the threshold as defined in the permissions.

- **Hierarchical Corporate Accounts**

BitShares designs permissions around people, rather than around cryptography, making it easy to use. Every account can be controlled by any weighted combination of other accounts and private keys. This creates a hierarchical structure that reflects how permissions are organized in real life, and makes multi-user control over funds easier than ever. Multi-user control is the single biggest contributor to security, and, when used properly, it can virtually eliminate the risk of theft due to hacking.

Read more about [Multi-Signature](https://github.com/bitshares/dev.bitshares.works/blob/master/core/knowledge_base/multi-signature.md#multi-signature)

***

## Permissions - in Wallet Settings

<p align="center">
  <img src="https://github.com/bitshares/how.bitshares.works/blob/master/bbf/images/permissions-active2.png" width="650" title="Permissions">
</p>

#### Permissions Tabs

| | tab |     |
|--|----------|----------|
| 1 | Active Permissions | Active permissions define the accounts that have permission to spend funds for this account. |
| 2 | Owner Permissions | Owner permissions define who has control over the account. Owners may overwrite all keys and change any account settings. |
| 3 | Memo Key | The memo key is where you receive memos, in order to decode the memos you need to control the private key for the public key <br/> By using a public/private key pair without spending authority, you may give read-only access to your memos to third parties. |
| 4 | Cloud Wallet | You can use this feature, if you want to change your **Cloud wallet** password.|

*** 
## Public Key and Private Key

### Where are public/private keys?

In this section, we will show you how to find your private key.  **Please check no one around you to keep secret your private keys!**

You can find each **Public key** and **Private key** on the Permissions page.  

<p align="center">
  <img src="https://github.com/bitshares/how.bitshares.works/blob/master/bbf/images/permissions-active3.png" width="650" title="Permissions Keys">
</p>

***

### How to find each private key

> Note: If you cannot click nor find a link, *login* to your wallet. 

#### Active and Owner – Private keys
- Go to [**Settings**] - [**Permissions**] 
- Click a private key number or a key image. 
- A Private key viewer form opens. You will find a Public Key and a [**SHOW**] button like below.
- Click the [**SHOW**] button. You will find your Private key under the Public key. 

<p align="center">
  <img src="https://github.com/bitshares/how.bitshares.works/blob/master/bbf/images/permissions-active4b.png" width="500" title="Permissions popup">
</p>

#### Memo – Private keys
- Go to [**Settings**] - [**Permissions**] - [**Memo key**] tab
- Click a key image. (It seems a private key number does not have a link.)
- A Private key viewer form opens. You will find a Public Key and a [**SHOW**] button like below.
- Click the [**SHOW**] button. You will find your Private key under the Public key.  

***

### How to change Cloud wallet password

In this section, we will show you how to change your Cloud Wallet password. 

Click **Cloud Wallet Tab**

If you want to change your *Cloud Wallet* password, use this page. You will change your password and your keys during the process. 

<p align="center">
  <img src="https://github.com/bitshares/how.bitshares.works/blob/master/bbf/images/permissions-cloud2.png" width="650" title="cloud wallet pwd">
</p>

**Steps**
		
**1. Save your old Memo key (optional)**

When you replace the memo key you will not be able to see old memos, so if you have a lot of those we recommend saving the old memo private key. 

- Go to [**Settings**] - [**Permissions**] - [**Memo key**] tab
- Click a key image. 
- A Private key viewer form opens. You will find a Public Key and a [**SHOW**] button like below.
- Click the [**SHOW**] button. Log in to your wallet if necessary. You will find your Private key under the Public key.  
- Write down and save the Private key.


**2. Set new password**

You can use your desired new password or use the auto-GENERATED PASSWORD.

- (#1)Type in [**PASSWORD**]
- (#2)Conform the password [**CONFIRM PASSWORD**]

**3. Generate new keys**

In this example, we decided to keep old Memo keys.

- (#3)Click [**USE**] to generate new Active key
- (#4)Click [**USE**] to generate new Owner key
- (#5)Click [**SAVE**] 
- Confirm the Transaction 


<p align="center">
  <img src="https://github.com/bitshares/how.bitshares.works/blob/master/bbf/images/permissions-cloud3.png" width="650" title="cloud wallet pwd">
</p>

**4. Remove old keys**

In order to remove access using your old password, you need to remove the keys corresponding to the old password.  

- Log out, and log back in with **your new password**.
- Go to [**Settings**] - [**Permissions**] - [**Active key**] tab

You will see the two public keys. The light blue colored key is your new public key that is belongs to your new password. You want to keep only this key!

- (#6)Click [**REMOVE**] next to the plain colored key. In this case, remove "P5J3maQ7kCDxaUfbBCRKwTwWnPwCp6h5sZU6va7C9sYW6".
- Now go to the Owner tab and do the same for the old owner key.
- (#7)Click [**SAVE**] -- *Do not forget to save!* 
- Confirm the Transaction


<p align="center">
  <img src="https://github.com/bitshares/how.bitshares.works/blob/master/bbf/images/permissions-removekey1.png" width="650" title="cloud wallet pwd">
</p>


<p align="center">
  <img src="https://github.com/bitshares/how.bitshares.works/blob/master/bbf/images/permissions-removekey2.png" width="650" title="cloud wallet pwd">
</p>




***


