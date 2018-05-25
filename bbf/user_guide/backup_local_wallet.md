## Backups and Restore your Wallet

### Contents:
- [Create Local Wallet Backup](/bbf/user_guide/backup_local_wallet.md#create-local-wallet-backup)
   - [Backup Types](/bbf/user_guide/backup_local_wallet.md#backup-types)
   - [How to Create a backup (.bin) file](/bbf/user_guide/backup_local_wallet.md#how-to-create-a-backup-bin-file)
   - [How to Create a Brainkey (Advanced User Only)](/bbf/user_guide/backup_local_wallet.md#how-to-create-a-brainkey-advanced-user-only)
- [Restore / Import](/bbf/user_guide/backup_local_wallet.md#restore--import)
   - [Restore / Import Options](/bbf/user_guide/backup_local_wallet.md#restore--import-options)
   - [How to Restore from a backup (.bin) file](/bbf/user_guide/backup_local_wallet.md#how-to-restore-from-a-backup-bin-file)
   - [How to Check *Active Local Wallet* name](/bbf/user_guide/backup_local_wallet.md#how-to-check-active-local-wallet-name)
   - [How to Recover Account with Brain key](/bbf/user_guide/backup_local_wallet.md#how-to-recover-account-with-brain-key)
   
   
***

> If you use a Cloud Wallet, you would not need to create a backup.

## Create Local Wallet Backup
It is recommended to make regular backups of your Local. Please note that in order to recover from a backup you will also need to provide the passphrase (password) because backups are encrypted. Hence, if you either lose your wallet or your passphrase you will be unable to access any of your funds again.

You are the only individual that has access to your account and funds, it is your responsibility to make a secure backup of your registered account.

### Backup Types
There are three types of backups.

| type |    |
|-------|--------------|
| Create local wallet backup | create a Binary File (.bin)  |
| Create brainkey backup | give you long random phrases. You need to write down.  |
| Create favorites backup |  |

***

### How to Create a backup (.bin) file

1. Open a Side menu and select [**Settings**]
1. In [**Settings**], select [**Backup**]. 
1. Make sure the backup type is *Create local wallet backup*
1. Click [**CREATE BACKUP NOW**]
1. Check your backup file name (e.g. bts_somrthing_20180420.bin)
1. Click [**DOWNLOAD**]

#### Store this backup in at least two secure locations only accessible by you

<p align="center">
  <img src="https://github.com/cedar-book/btsdoc-portal/blob/master/bbf/img/backup-1.png" width="670" title="Backup">
</p>

<p align="center">
  <img src="https://github.com/cedar-book/btsdoc-portal/blob/master/bbf/img/backup-2.png" width="670" title="Backup">
</p>

***

#### The Brain Key

The brain key is used as source for all cryptographic keys generated in the wallet. If you have it secured, you will be able to regain access to your accounts and funds (unless the access keys have been changed)

### How to Create a Brainkey (Advanced User Only)

1. Open a Side menu and select [**Settings**]
1. In [**Settings**], select [**Backup**]. 
1. (#1) Make sure the backup type is *Create brainkey backup*
1. (#2) Type in a password
1. (#3) Click [**SHOW BRAINKEY**]
1. (#4) Write down *Brainkey* (i.g. very random long phrases)
1. (#5) Click [**I'VE WRITTEN IT DOWN**] 

#### Write it down!! Anyone with access to your recovery key will have access to funds with in the local wallet.

<p align="center">
  <img src="https://github.com/cedar-book/btsdoc-portal/blob/master/bbf/img/backup-brainkey.png" width="670" title="Backup">
</p>

<p align="center">
  <img src="https://github.com/cedar-book/btsdoc-portal/blob/master/bbf/img/backup-brainkey2.png" width="670" title="Backup">
</p>

***
***

## Restore / Import

> We assume you have created a backup file (.bin) and use a Local wallet.

### Restore / Import Options

| option |    |
|-------|--------------|
| Restore from a backup file (.bin) | restore from a backup file and a password  |
| Import a private key |   |
| Import a BTS 0.9.3c key export file (.json) |  |
| Restore using a local wallet brainkey | use a password and a Brain key |
| Restore favorites using a json file |  |


### How to Restore from a backup (.bin) file

1. Open a Side menu and select [**Settings**]
1. In [**Settings**], select [**Restore/Import**]. 
1. (#1) Make sure you selected *Restore from a backup file (.bin)*
1. (#2) Click [**Browse...**] to find a backup file. 
1. (#3) Type in a password
1. (#4) Click [**SUBMIT**]
1. (#5) Type in *New Local Wallet Name* if you want to change the backup file name.
1. (#6) Click [**ACCEPT**]
1. (#7) **Ready to Restore** - below "RESTORE(..... WALLET)" is a button. Click it. 
1. You will find "Successfully  restored (....)wallet.  Done!!
1. (#8) Click [**DASHBOARD**]

<p align="center">
  <img src="https://github.com/bitshares/how.bitshares.works/blob/master/bbf/img/restore1.png" width="670" title="Backup">
</p>

<p align="center">
  <img src="https://github.com/bitshares/how.bitshares.works/blob/master/bbf/img/restore3.png" width="670" title="Restore">
</p>
<p align="center">
  <img src="https://github.com/bitshares/how.bitshares.works/blob/master/bbf/img/restore4.png" width="670" title="Restore">
</p>
<p align="center">
  <img src="https://github.com/bitshares/how.bitshares.works/blob/master/bbf/img/restore5.png" width="670" title="Restore">
</p>
<p align="center">
  <img src="https://github.com/bitshares/how.bitshares.works/blob/master/bbf/img/restore6.png" width="670" title="Restore">
</p>

### How to Check *Active Local Wallet* name

- [**Settings**] - [**Local Wallet**] - Active Local Wallet

<p align="center">
  <img src="https://github.com/bitshares/how.bitshares.works/blob/master/bbf/img/restore7.png" width="670" title="Restore">
</p>

***

### How to Recover Account with Brain key

1. Open a Side menu and select [**Settings**]
1. In [**Settings**], select [**Restore/Import**]. 
1. (#1) Make sure you selected *Restore using a local wallet brainkey*
1. (#2) Type in a password
1. (#3) Type in a password (Confirm)
1. (#4) Type in *Local Wallet Name* if needed. (e.g. "default-test-brainkey-restore")
1. (#5) Type in **BRAINKEY**
1. (#6) Click [CREATE NEW LOCAL WALLET]
1. (#7) Click [DONE]
1. (#8) Let's check *ACTIVE LOCAL WALLET* name (Go to [Settings] - [Local Wallet])

<p align="center">
  <img src="https://github.com/bitshares/how.bitshares.works/blob/master/bbf/img/brainkey1.png" width="670" title="Backup">
</p>
<p align="center">
  <img src="https://github.com/bitshares/how.bitshares.works/blob/master/bbf/img/brainkey2.png" width="670" title="Restore">
</p>
<p align="center">
  <img src="https://github.com/bitshares/how.bitshares.works/blob/master/bbf/img/brainkey3.png" width="670" title="Restore">
</p>
<p align="center">
  <img src="https://github.com/bitshares/how.bitshares.works/blob/master/bbf/img/brainkey4.png" width="670" title="Restore">
</p>
<p align="center">
  <img src="https://github.com/bitshares/how.bitshares.works/blob/master/bbf/img/brainkey5.png" width="670" title="Restore">
</p>


***
