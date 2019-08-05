
.. _private-testnet-guidev2:

************************
Private Testnet Set up v2
************************

Set up BitShares Private Testnet 
In this section you will perform…

Your home Directory $Home/

* Download BitShares Core (Testnet branch)
* Create ``bitshares-testnet`` directory to download the source files under your $Home directory (e.g., $Home/bitshares-testnet)
* Perform the initial compilation to build two program files (witness_nide and cli_wallet)
* Create a ``genesis`` directory inside a ``programs`` directory ($Home/bitshares-testnet/programs/)
* Copy a ``genesis-dev.json`` file into the ``genesis`` folder as a ``my-genesis.json``
* Customize the my-ginesis.json file
* Embed the my-genesis.json file (*understand the benefits)
* Create a ``Testnet-Home`` directory under %Home directory (e.g., $Home/Testnet-Home/)
* Cory the two program files (witness_node and cli_wallet) in the ``Testnet-Home`` directory
* Create a ``genesis`` directory under $Home directory
* Copy customized my-genesis.json into the genesis directory
* Create a ``data-bts`` directory for the data files
* Run ``witness_node`` with parameters. This command will create the BitShares blockchain data folders and files and a configuration file ``config.ini``. Also, you should find a chain ID on a screen. 
* Close the witness_node
* Set up configuration
* Start ``witness_node`` again with the parameter for the data directory
* Create a new CLI Wallet
* Set a password for the CLI Wallet 
