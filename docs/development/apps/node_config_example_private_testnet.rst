
.. _bts-config-ini-eg-private-testnet:

Configuration for Private Testnets
===================================

This section shows the BitShres Blockchain configuration file ``config.ini`` parameter items. The configuration file locates in your data directory and the most parameter items are commented and contain the essential settings to adjust.


.. WARNING:: For a Private testnet, you must add a seed node of your own, you should not connect to the mainnet nor the public testnet.


Example - config.ini (private testnet)
------------------------------------


As a default, most parameters have been commented. You have to uncomment to set the values.

::

	p2p-endpoint = 127.0.0.1:11010
	rpc-endpoint = 127.0.0.1:11011
	# seed_node =                   // add a seed node of your own
	
	genesis-json = genesis/my-genesis.json

	private-key = ["BTS....","5KQwr...."]  //Set your key pairs
	# private-key = ["BTS6MRyAjQq8ud7hVNYcfnVPJqcVpscN5So8BhtHuGYqET5GDW5CV","5KQwrPbwdL6PhXujxW37FSSQZ1JiwsST4cqQzDeyXtP79zkvFD3"]
	
	witness-id = "1.6.1"
	witness-id = "1.6.2"
	witness-id = "1.6.3"
	witness-id = "1.6.4"
	witness-id = "1.6.5"
	witness-id = "1.6.6"
	witness-id = "1.6.7"
	witness-id = "1.6.8"
	witness-id = "1.6.9"
	witness-id = "1.6.10"
	witness-id = "1.6.11"
	

	# Logging configuration is loaded from logging.ini by default.
	# If logging.ini exists, logging configuration added in this file will be ignored.

	
	
Example - logging.ini
---------------------

::

	# declare an appender named "stderr" that writes messages to the console
	[log.console_appender.stderr]
	stream=std_error

	# declare an appender named "default" that writes messages to default.log
	[log.file_appender.default]
	# filename can be absolute or relative to this config file
	filename=logs/default/default.log
	# Rotate log every ? minutes, if leave out default to 60
	rotation_interval=60
	# how long will logs be kept (in days), if leave out default to 1
	rotation_limit=7

	# declare an appender named "p2p" that writes messages to p2p.log
	[log.file_appender.p2p]
	# filename can be absolute or relative to this config file
	filename=logs/p2p/p2p.log
	# Rotate log every ? minutes, if leave out default to 60
	rotation_interval=60
	# how long will logs be kept (in days), if leave out default to 1
	rotation_limit=7

	# declare an appender named "rpc" that writes messages to rpc.log
	[log.file_appender.rpc]
	# filename can be absolute or relative to this config file
	filename=logs/rpc/rpc.log
	# Rotate log every ? minutes, if leave out default to 60
	rotation_interval=60
	# how long will logs be kept (in days), if leave out default to 1
	rotation_limit=7

	# route any messages logged to the default logger to the "stderr" appender and
	# "default" appender we declared above, if they are info level or higher
	[logger.default]
	level=info
	appenders=stderr,default

	# route messages sent to the "p2p" logger to the "p2p" appender declared above
	[logger.p2p]
	level=warn
	appenders=p2p

	# route messages sent to the "rpc" logger to the "rpc" appender declared above
	[logger.rpc]
	level=error
	appenders=rpc

|

Example - Set up the Second Node
--------------------------------

If you want to set up a second node (with the same genesis file) and connect it to the first node by using the ``p2p-endpoint`` of the first node as the ``seed-node`` for the second. The below are example settings.


**Node-001: config.ini**

::

	p2p-endpoint = 127.0.0.1:11010
	# seed-node =                // add a seed node of your own
	
	rpc-endpoint = 127.0.0.1:11011
	
	
**Node-002: config.ini**

  - Set the Node-001's ``p2p-endpoint`` as the Node-002's ``seed-node``. 
  
::

	p2p-endpoint = 127.0.0.1:11015
	seed-node = 127.0.0.1:11010	
	
	rpc-endpoint = 127.0.0.1:11020
		
	

