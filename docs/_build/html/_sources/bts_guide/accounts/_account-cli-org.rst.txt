
.. _account-import-to-cli-ORG:

import a GUI-wallet account
======================================



Without already having an account, or knowing someone that has an account, it is not possible. You can’t create accounts out of nowhere.

But you can work around it by importing an active key of a **Lifetime Member** account that has funds:

**GUI**

#. In the GUI, open the side menu
#. Select the [Permissions] of an account that is funded and has a LTM status
#. Click on the public key on the ACTIVE tab. It opens a private key viewer.
#. Click [Show] and copy the private key. (with Login)


**CLI**

#. In the cli-wallet run a command: ``import_key <account_name> <private_key>``
#. Then run: ``suggest_brain_key`` and copy the brain key. (You might want to make a backup of your brain key somewhere.)
#. Create a new account with this command: ``create_account_with_brain_key <brainkey> <new_account_name> <imported_name> <imported_name> true``


This will create a new account called \<new_account_name\> and set the registrar and referrer to \<imported_name\>. The brainkey can be used to regenerate the account (even in the GUI wallet). You can manually delete the other active key from the wallet.json file.

|