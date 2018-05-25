
Questions - CLI Wallet
----------------------

    
How can I close the CLI client in a clean way?
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

In Windows closing the whole window produces a nasty exception. In
Windows you can try `ctrl-d` which stops the process but stil produces a
nasty exception.



How can I import to my CLI client a wallet originally created in the web GUI?
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

CLI and WEB wallet are two separated applications. They use separated
ways to represent backups. You can currently only manually import keys
from the GUI into the CLI.


 

How can I transfer a single account to a cli wallet
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

If you have a need to run just the cli wallet, you will likely want to
transfer an existing account from the Web wallet or your light wallet
into the cli. This guide assumes that you already have your web wallet
working and properly connected to a full node. Something like this:

.. code-block:: sh

      ./cli-wallet -s wss://bitshares.openledger.info/ws

You should get a prompt

.. code-block:: sh

      new >>>

Now set a password for the wallet

.. code-block:: sh

      new >>> set_password my_password
      locked >>> unlock my_password
      unlocked >>>

Now you need to go to your bitshares 2.0 webwallet or lite client and 
select the account you wish to bring across. Click on permissions followed
by the key icon within Active Permissions. This will reveal your private key.
Copy it to the clipboard.

.. image:: account-active-permissions.png

Now let's import the account into this wallet

.. code-block:: sh

      new >>> import_key <accountname> THISISTHEKEYTHATYOUCOPIED

 
  
Why does the CLI client crash immediately when I try to run it for the first time?
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

The CLI client is unable to run on its own, i.e. without being connected
to the witness node (via a web socket connection). So to successfully
run the CLI client you need to do this:

* make sure you have this entry uncommented in the
  `witness_node_data_dir/config.ini` file  
  `rpc-endpoint = 127.0.0.1:8090`
* before you start the CLI client, you need to start the witness node
  (and wait a while till it's up and running)


|