### Setup Private Testnet --create-genesis-json creates witness_node_data_dir #1118

***

#### Issue:

Testing setup [Private Testnet steps](https://dev.bitshares.works/en/master/development/testnets/private_testnet.html)

(step 3) When I run `./witness_node --create-genesis-json=my-genesis.json`, creating a `my-genesis.json` and also `witness_node_data_dir` folder.

(step 6) Creates a "data" Directory and also config.ini to use. The `witness_node_data_dir` folder won't be used (doesn't need to be created).

***
*This document purpose: after some research, found how the situation is occurring. We decided to keep the code as it is and add the information clearly as a `warning` (in the Private Testnet setup section).*

***

#### Research:
I checked a witness_node executable main() for a part of another issue and created a [coding flowchart](../knowledge_base/shared_files/witness_node_ex-codeflows.pdf) (in page 8) to view the flow.

#### Findings:
You may or may not have own data directory that does not matter. If --data-dir is not set in a command line and when witness_node starts (and not find a config.ini), a witness_node_data_dir will be created (if not exists). So, if you create own data directory and forget to set the --data-dir when you start a witness_node, you get new default data directory and config.ini to start the witness_node.

#### how...
in the documentation clearly mention (warn) to users; you have to define a data directory if you have own data directory (with config.ini), otherwise, witness_node_data_dir will be created and used a default config.ini.

***

The warnings were added...

In [Private Testnets](https://dev.bitshares.works/en/master/development/testnets/private_testnet.html#genesis-file)

## 3. Creating a Genesis File for a Private Testnet

The genesis.json is the initial state of the network. We create a new genesis json file named `my-genesis.json` for a Private Testnet in the same folder just created.

    ./witness_node --create-genesis-json=my-genesis.json

The `my-genesis.json` file will be created in the `[Testnet-Home]` folder. Once this task is done, the witness node will terminate on its own. 

> **Note:** `witness_node` startup will create a `witness_node_data_dir` as a default data directory (with config.ini). If you want to use a different folder name and directory for the data, you have to use `--data-dir` in a startup command line and set your data directory folder path. Otherwise, the `witness_node_data_dir` folder will be created and use a default config.ini file to start the witness_node.

and...


## 8. Starting Block Production

Now run witness_node again:

    ./witness_node --data-dir data/my-blockprod --enable-stale-production --seed-nodes "[]"

> **warning:** If you want to use a different folder name and directory for the data, you have to use `--data-dir` in a startup command line and set your data directory folder path. Otherwise, the `witness_node_data_dir` folder will be created and used a default config.ini file to start the witness_node!!

***
  
(*created:7/9/2018*)
