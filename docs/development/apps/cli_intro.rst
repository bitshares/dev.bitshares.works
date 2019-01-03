

.. _cli-wallet-intro:

*********************************
Cli Wallet and the Connectivity 
*********************************

CLI Client and Wallet
================================================

Architecture
---------------

.. image:: ../../../../_static/structures/bitshares-architecture-clinotop.png
        :alt: BitShares Architecture
        :width: 750px
        :align: center
		

The Nodes are connected to the network and verify all transactions and block produces. The **cli_wallet** does not offer P2P or blockchain capabilities.  

Launching the cli_wallet requires a trusted Full Node to interact with the blockchain. You might use a localhost or own node to connect. Also, you can select one of `BitShares Public Full Nodes <https://github.com/bitshares/bitshares-ui/blob/staging/app/api/apiConfig.js>`_, or use API Server that runs by **businesses** or **individuals**.

When you start the cli_wallet first time, it creates a local ``wallet.json`` file that contains the encrypted private keys. The key is required to access the funds and add new data to the blockchain with a signature from a private key.


.. _cli-wallet-json-eg:

Outputs - wallet.json
--------------------------

**Example 1: wallet.json**

- The example below, 
  - we imported ``nathan`` account (*our Test account) to a cli wallet, 
  - upgraded ``nathan`` account to a LifeTime Member account, and 
  - tried to cerate new account ``my-account`` by using ``nathan`` account as both the referrer and registrar.
  

.. code-block:: json

	{
	  "chain_id": "4018d7844c78f6a6c41c6a552b898022310fc5dec06da467ee7905a8dad512c8",
	  "my_accounts": [{
		  "id": "1.2.298",
		  "membership_expiration_date": "1969-12-31T23:59:59",
		  "registrar": "1.2.298",
		  "referrer": "1.2.298",
		  "lifetime_referrer": "1.2.298",
		  "network_fee_percentage": 2000,
		  "lifetime_referrer_fee_percentage": 8000,
		  "referrer_rewards_percentage": 0,
		  "name": "nathan",
		  "owner": {
			"weight_threshold": 1,
			"account_auths": [],
			"key_auths": [[
				"BTS7T2swLv3QqBAzP1hByB5khUjNFEahtp1fYEHL2GFubMvNGVXyG",
				1
			  ]
			],
			"address_auths": []
		  },
		  "active": {
			"weight_threshold": 1,
			"account_auths": [],
			"key_auths": [[
				"BTS7T2swLv3QqBAzP1hByB5khUjNFEahtp1fYEHL2GFubMvNGVXyG",
				1
			  ]
			],
			"address_auths": []
		  },
		  "options": {
			"memo_key": "BTS7T2swLv3QqBAzP1hByB5khUjNFEahtp1fYEHL2GFubMvNGVXyG",
			"voting_account": "1.2.1090",
			"num_witness": 0,
			"num_committee": 0,
			"votes": [],
			"extensions": []
		  },
		  "statistics": "2.6.298",
		  "whitelisting_accounts": [],
		  "blacklisting_accounts": [],
		  "whitelisted_accounts": [],
		  "blacklisted_accounts": [],
		  "cashback_vb": "1.13.121",
		  "owner_special_authority": [
			0,{}
		  ],
		  "active_special_authority": [
			0,{}
		  ],
		  "top_n_control_flags": 0
		}
	  ],
	  "cipher_keys": "b973b57b9a1de8e6d6d7588f62d61e1cc6a229c802bf5a3fefbedbebc8b579370e77c2053458a00f13b674371b2e6313febedf7f099864de66e8fcb86d87bd6c8961ece89454ba0a50a965da0b6e67958e491d2df55ebf7cc07c5f4d1e8f7f662347714011b7980657ad5cb4c72d822efd9119a64cfc804c89f635ba567905463e1521c57f41fbb55e43766f8af5cbd02860ac34ef5772753fded808c8db37c6",
	  "extra_keys": [[
		  "1.2.298",[
			"BTS6MRyAjQq8ud7hVNYcfnVPJqcVpscN5So8BhtHuGYqET5GDW5CV"
		  ]
		]
	  ],
	  "pending_account_registrations": [[
		  "my-account",[
			"5JVfJT1vTE4qdHcuFJxKJjcQdCPeNHt3WvudQzzCQ4LSq1H31gV",
			"5JL97KJMpMAayXFaa3HZGbLtbgEAoBUiQWd4XqZGBiHPxo3JYmB"
		  ]
		]
	  ],
	  "pending_witness_registrations": [],
	  "labeled_keys": [],
	  "blind_receipts": [],
	  "ws_server": "ws://localhost:8090",
	  "ws_user": "",
	  "ws_password": ""
	} 



**Example 2: wallet.json**

.. code-block:: json

	{
		"chain_id": "4018d7844c78f6a6c41c6a552b898022310fc5dec06da4222222",
		"my_accounts": [],
		"cipher_keys": "4144454a976266ed15f736df4f5645e60bace86eb87cb5b59b0c8f48b75c6131167807c403a56060528b7dae993de667736d5ab9ef1f60fb340c4aa70437ec7a2534bbdab051b9d2d1871111111",
		"extra_keys": [],
		"pending_account_registrations": [],
		"pending_witness_registrations": [],
		"labeled_keys": [],
		"blind_receipts": [],
		"ws_server": "ws://localhost:8090",
		"ws_user": "",
		"ws_password": ""
	}


|

|

