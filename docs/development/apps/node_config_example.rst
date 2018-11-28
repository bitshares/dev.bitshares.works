
.. _bts-config-ini-eg:

Configuration
====================

The configuration file ``config.ini`` in your data directory is commented and contains the following essential settings:

.. attention:: We suggest you to examine BitShares configuration items if you are interested starting a node.  


Example
----------------

Example of a ``config.ini`` file in the data directory.

As a default, most parameters have been commented. You have to uncomment to set values.

::

	# Endpoint for P2P node to listen on
	#p2p-endpoint = 0.0.0.0:11010 

	# P2P nodes to connect to on startup (may specify multiple times)
	# seed-node = 

	# JSON array of P2P nodes to connect to on startup
	# seed-nodes = 

	# Pairs of [BLOCK_NUM,BLOCK_ID] that should be enforced as checkpoints.
	# checkpoint = 

	# Endpoint for websocket RPC to listen on
	#rpc-endpoint = 127.0.0.1:8090

	# Endpoint for TLS websocket RPC to listen on
	# rpc-tls-endpoint = 

	# The TLS certificate file for this server
	# server-pem = 

	# Password for this certificate
	# server-pem-password = 

	# File to read Genesis State from
	# genesis-json = 

	# Block signing key to use for init witnesses, overrides genesis file
	# dbg-init-key = 

	# JSON file specifying API permissions
	# api-access = 

	# Space-separated list of plugins to activate
	# plugins = 

	# Enable block production, even if the chain is stale.
	enable-stale-production = false

	# Percent of witnesses (0-99) that must be participating in order to produce blocks
	required-participation = false

	# ID of witness controlled by this node (e.g. "1.6.5", quotes are required, may specify multiple times)
	# witness-id = 

	# Tuple of [PublicKey, WIF private key] (may specify multiple times)
	#private-key = ["BTS6MRyAjQq8...........GDW5CV","5KQwrPbwdL..............yXtP79zkvFD3"]

	# Tuple of [PublicKey, WIF private key] (may specify multiple times)
	debug-private-key = ["BTS6MRyAjQq8...........GDW5CV","5KQwrPbwdL..............yXtP79zkvFD3"]

	# Account ID to track history for (may specify multiple times)
	# track-account = 

	# Keep only those operations in memory that are related to account history tracking
	partial-operations = 1

	# Maximum number of operations per account will be kept in memory
	max-ops-per-account = 1000

	# Elastic Search database node url
	# elasticsearch-node-url = 

	# Number of bulk documents to index on replay(5000)
	# elasticsearch-bulk-replay = 

	# Number of bulk documents to index on a syncronied chain(10)
	# elasticsearch-bulk-sync = 

	# Log bulk events to database
	# elasticsearch-logs = 

	# Use visitor to index additional data(slows down the replay)
	# elasticsearch-visitor = 

	# Track market history by grouping orders into buckets of equal size measured in seconds specified as a JSON array of numbers
	bucket-size = [60,300,900,1800,3600,14400,86400]

	# How far back in time to track history for each bucket size, measured in the number of buckets (default: 1000)
	history-per-size = 1000

	# Will only store this amount of matched orders for each market in order history for querying, or those meet the other option, which has more data (default: 1000)
	max-order-his-records-per-market = 1000

	# Will only store matched orders in last X seconds for each market in order history for querying, or those meet the other option, which has more data (default: 259200 (3 days))
	max-order-his-seconds-per-market = 259200

	# RPC endpoint of a trusted validating node (required)
	# trusted-node = 

	# Block number after which to do a snapshot
	# snapshot-at-block = 

	# Block time (ISO format) after which to do a snapshot
	# snapshot-at-time = 

	# Pathname of JSON file where to store the snapshot
	# snapshot-to = 

	# Elasticsearch node url
	# es-objects-elasticsearch-url = 

	# Log bulk events to database
	# es-objects-logs = 

	# Number of bulk documents to index on replay(5000)
	# es-objects-bulk-replay = 

	# Number of bulk documents to index on a syncronied chain(10)
	# es-objects-bulk-sync = 

	# Store proposal objects
	# es-objects-proposals = 

	# Store account objects
	# es-objects-accounts = 

	# Store asset objects
	# es-objects-assets = 

	# Store balances objects
	# es-objects-balances = 

	# Store limit order objects
	# es-objects-limit-orders = 

	# Store feed data
	# es-objects-asset-bitasset = 

	# Group orders by percentage increase on price. Specify a JSON array of numbers here, each number is a group, number 1 means 0.01%. 
	tracked-groups = [10,100]

	[log.console_appender.stderr]
	stream=std_error

	# declare an appender named "p2p" that writes messages to p2p.log
	[log.file_appender.p2p]
	filename=logs/p2p/p2p.log
	# filename can be absolute or relative to this config file

	# route any messages logged to the default logger to the "stderr" logger we
	# declared above, if they are info level are higher
	[logger.default]
	level=info
	appenders=stderr

	# route messages sent to the "p2p" logger to the p2p appender declared above
	[logger.p2p]
	level=info
	appenders=p2p


	
	
.. Note:: Folders and files are considered to be relative to the working directory (i.e. the directory from which the executables are launched from)

	
