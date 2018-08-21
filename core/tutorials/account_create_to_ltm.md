## How to create, register and upgrade an account to Lifetime Membership

Without already having an account, or knowing someone that has an account, it is not possible. You can’t create accounts out of nowhere.

But you can work around it by importing an active key of a **Lifetime Member** account that has funds:

1. In the GUI, open the side memu and select the permissionss of an account that is funded and has a LTM status.
1. Click on the BTS public key on the ACTIVE tab, a private key viewer opens. 
1. Click [Show] and copy the private key.
1. In the cli-wallet run: `import_key <account_name> <private_key>`
1. Then run: `suggest_brain_key` and copy the brain key. (You might want to make a backup of your brain key somewhere.)
1. Create a new account with this command: `create_account_with_brain_key <brainkey> <new_account_name> <imported_name> <imported_name> true`

This will create a new account called <new_account_name> and set the registrar and referrer to <imported_name>. The brainkey can be used to regenerate the account (even in the GUI wallet). You can manually delete the other active key from the wallet.json file.
