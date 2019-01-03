
.. _bts-config-ini-eg:

Configuration
====================

This section shows the BitShres Blockchain configuration file ``config.ini`` parameter items. The configuration file locates in your data directory and the most parameter items are commented and contain the essential settings to adjust.

.. note:: The below cinfig.ini output is an example (default values). You should modify ``private-key`` and debug-private-key`` and adjust other parameter values respectively. 


Example - config.ini
---------------------

Example of a ``config.ini`` file in the data directory.

As a default, most parameters have been commented. You have to uncomment to set the values.

::

	# Endpoint for P2P node to listen on
	# p2p-endpoint = 

	# P2P nodes to connect to on startup (may specify multiple times)
	# seed-node = 

	# JSON array of P2P nodes to connect to on startup
	# seed-nodes = 

	# Pairs of [BLOCK_NUM,BLOCK_ID] that should be enforced as checkpoints.
	# checkpoint = 

	# Endpoint for websocket RPC to listen on
	# rpc-endpoint = 

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

	# Number of IO threads, default to 0 for auto-configuration
	# io-threads = 

	# Whether allow API clients to subscribe to universal object creation and removal events
	# enable-subscribe-to-all = 

	# Whether to enable tracking of votes of standby witnesses and committee members. Set it to true to provide accurate data to API clients, set to false for slightly better performance.
	# enable-standby-votes-tracking = 

	# Enable block production, even if the chain is stale.
	enable-stale-production = false

	# Percent of witnesses (0-99) that must be participating in order to produce blocks
	required-participation = false

	# ID of witness controlled by this node (e.g. "1.6.5", quotes are required, may specify multiple times)
	# witness-id = 

	# Tuple of [PublicKey, WIF private key] (may specify multiple times)
	private-key = ["BTS6MRyAjQq8ud7hVNYcfnVPJqcVpscN5So8BhtHuGYqET5GDW5CV","5KQwrPbwdL6PhXujxW37FSSQZ1JiwsST4cqQzDeyXtP79zkvFD3"]

	# Tuple of [PublicKey, WIF private key] (may specify multiple times)
	debug-private-key = ["BTS6MRyAjQq8ud7hVNYcfnVPJqcVpscN5So8BhtHuGYqET5GDW5CV","5KQwrPbwdL6PhXujxW37FSSQZ1JiwsST4cqQzDeyXtP79zkvFD3"]

	# Account ID to track history for (may specify multiple times)
	# track-account = 

	# Keep only those operations in memory that are related to account history tracking
	partial-operations = 1

	# Maximum number of operations per account will be kept in memory
	max-ops-per-account = 100

	# Elastic Search database node url(http://localhost:9200/)
	# elasticsearch-node-url = 

	# Number of bulk documents to index on replay(10000)
	# elasticsearch-bulk-replay = 

	# Number of bulk documents to index on a syncronied chain(100)
	# elasticsearch-bulk-sync = 

	# Use visitor to index additional data(slows down the replay(false))
	# elasticsearch-visitor = 

	# Pass basic auth to elasticsearch database('')
	# elasticsearch-basic-auth = 

	# Add a prefix to the index(bitshares-)
	# elasticsearch-index-prefix = 

	# Save operation as object(false)
	# elasticsearch-operation-object = 

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

	# Elasticsearch node url(http://localhost:9200/)
	# es-objects-elasticsearch-url = 

	# Basic auth username:password('')
	# es-objects-auth = 

	# Number of bulk documents to index on replay(10000)
	# es-objects-bulk-replay = 

	# Number of bulk documents to index on a synchronized chain(100)
	# es-objects-bulk-sync = 

	# Store proposal objects(true)
	# es-objects-proposals = 

	# Store account objects(true)
	# es-objects-accounts = 

	# Store asset objects(true)
	# es-objects-assets = 

	# Store balances objects(true)
	# es-objects-balances = 

	# Store limit order objects(true)
	# es-objects-limit-orders = 

	# Store feed data(true)
	# es-objects-asset-bitasset = 

	# Add a prefix to the index(objects-)
	# es-objects-index-prefix = 

	# Keep only current state of the objects(true)
	# es-objects-keep-only-current = 

	# Group orders by percentage increase on price. Specify a JSON array of numbers here, each number is a group, number 1 means 0.01%. 
	tracked-groups = [10,100]


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

	
	
.. Note:: Folders and files are considered to be relative to the working directory (i.e. the directory from which the executables are launched from)

	
