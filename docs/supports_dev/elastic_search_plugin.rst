
.. _elastic-search-plugin:

***********************
ElasticSearch Plugin 
***********************

ElasticSearch Plugin v1

The document explains motivations, technical challenges and sample use of a new plug-in created for bitshares to store account history data into an elasticsearch database.

---------

.. contents:: Table of Contents
   :local:
   
-------

Motivation
====================

There are 2 main problems this plug-in tries to solve:

- The amount of RAM needed to run a full node. Current options for this are basically store account history: yes/no, store some of the account history, track history for specific accounts, etc. Plugin allows to have all the account history data without the amount of RAM required in current implementation.
- The number of github issues in regards to new api calls to lookup account history in different ways. ``elasticsearch-plugin`` will allow users to search account history querying directly into the database without the bitshares-core team creating and maintaining API calls for every case.

Additionally we are after a secure way to store error free full account data. The huge amount of operations data involved in bitshares blockchain and the way it is serialized makes this a big challenge.

Even more, saving data to Elastic Search database(from now on: ES) will bring additional value to clients connected to the new full node with real time statistics and fast access to data.

The Database Selection
==========================

Elastic search was selected for the main following reasons:

- open source.
- fast.
- index oriented.
- easy to install and start using.
- send data from c++ using curl.
- scalable and decentralized nodes of data.

Technical
==========================

The ``elasticsearch-plugin`` is uses the basics of ``account_history_plugin``.

Here is how the current account_history plugin works basically:

- with every signed block arriving to the plugin the ops are extracted from it.
- each op is added to ohi(operation history index) and to ath(account transaction history index).
- both indexes keep growing as new block gets in.
- with the memory reduction techniques currently available the 2 indexes can remove early ops of accounts reducing ram size.

And now what ``elasticsearch-plugin`` attempts to do:

- with every signed block arriving to the plugin the ops are extracted from it, just as in account_history.
- create indexes ohi and ath and store current op there just as account history do. this a temp indexation of 1 op only that is done to remain compatible with the previous numbers used as id(1.11.X and 2.9.X).
- send ath and ohi plus additional block data and visitor data into ES(actually we send them in bulks not one by one as we will explain below).
- remove everything in the compatibility temporal indexes except for current operation. This way the indexes always have just 1 item and don't use any ram.

Replay and _bulk
--------------------------

As mentioned we don't send to ES the operations one by one, this is because in a replay the number of ops will be so big that performance will decrease drastically and time to be in sync will be too much. For this reason we use the ES bulk api and send by default 10000 lines when we are in replay and downgrade this to 100 lines after we are in sync.

ES bulk format is one line of meta-data and one line of data itself, so 10000 is actually 2500 operations we send on every bulk and 100 is actually 50 ops in real time mode.

This values are available as plugin options to the user to change.

The optimal number of docs to bulk is hardware dependent.

The name of the created index in ES will be ``graphene``

Accessing data inside operations
--------------------------------------

There are cases where data coming from account transaction history index, operation history index and block data is not enough. We may want to index fields inside operations themselves like the fees, the asset id or the amount of transfer to make fast queries into them.

Data inside operations is saved as a text fields into ES, this means that we can't fast search into them as the data is not indexed, we can, still search by other data and filter converting the op text to json in client side. This is possible and relatively easy so please consider.

Still, a workaround the limitation is available. A ``visitor`` that can be turned on/off by the command line. As an example something in common all ops have is a fee field with ``asset_id`` and ``amount``. In ``elasticserch-plugin`` v1 when visitor is ``true`` this 2 values will be saved meaning clients can know total chain fees collected in real time, total fees in asset, fees by op among other things.

As a poc we also added amount and asset_id of transfer operations to illustrate how easy is to index more data for any competent graphene developer.

 
Hardware needed
----------------------------------

It is very recommended that you use SSD disks in your node if you are trying to synchronize bitshares blockchain. It will make the task a lot faster.

You need 500 gigs of space to be safe for a while, 32 gigs or more of ram is recommended.

After elasticsearch is installed increase heap size depending in your RAM:

::

    elastic@alfredo-worker2:~/elasticsearch-5.6.3/config$ vi jvm.options

::

    ..
    # Xms represents the initial size of total heap space
    # Xmx represents the maximum size of total heap space

    -Xms12g
    -Xmx12g
    ...


Installation
==========================

Basically there are 2 things you need: elasticsearch and curl for c++. elasticsearch need java so those are the 3 things you will need to have. The following are instructions tested in debian(ubuntu - mint) based linux versions.

Install java:
---------------

download the jre, add sudo to the start of the commands if installing from a non root user:::

    apt-get install default-jre

we are going to need the jdk too::

    apt-get install default-jdk

add repository to install oracle java8::

in ubuntu::

    add-apt-repository ppa:webupd8team/java

in debian::

    add-apt-repository "deb http://ppa.launchpad.net/webupd8team/java/ubuntu xenial main"

then:: 

    apt-get update

if you don't have add-apt-repository command you can install it by::

    apt-get install software-properties-common

install java 8::

    apt-get install oracle-java8-installer


	
Install ES:
----------------

Get version 6 file at: https://artifacts.elastic.co/downloads/elasticsearch/elasticsearch-6.2.0.zip

.. Note:: Plugin works with currently last stable version of elastic(6.2.0)

Please do this as a non root user as ES will not run as root.

download as::

   wget https://artifacts.elastic.co/downloads/elasticsearch/elasticsearch-6.2.0.zip

unzip::

   unzip elasticsearch-6.2.0.zip

and run::

    cd elasticsearch-6.2.0.zip/
    ./bin/elasticsearch

You can put this as a service, the binary haves a ``--daemonize`` option, can run inside ``screen`` or any other option that suits you in order to keep the database running.

Please note ES does not run a the root user, if you are a root user you need to first make a normal user account by::

   adduser elastic

After created: ``su elastic``. Execute the ``wget`` and ``unzip`` commands from above as normal user in created user home dir.

Install curl
---------------------

We need curl to send requests from the c++ plugin into the ES database::

    apt-get install libcurl4-openssl-dev

Running
================

Make sure ES is running, can start it by::

    ./elasticsearch --daemonize

ES will listen on localhost port 9200 ``127.0.0.1:9200``

Clone repo and install bitshares::

    git clone https://github.com/bitshares/bitshares-core
    cd bitshares-core
    git submodule update --init --recursive
    BOOST_ROOT=$HOME/opt/boost_1_63_0
    cmake -DBOOST_ROOT="$BOOST_ROOT" -DCMAKE_BUILD_TYPE=RelWithDebInfo .
    make

Arguments
---------------

The ES plugin have the following parameters passed by command line::

**parameter**

:elasticsearch-node-url: The url od elasticsearch - default: http://localhost:9200/ 
:elasticsearch-bulk-replay: The number of lines(ops * 2) to send to database in replay state - default: 10000 
:elasticsearch-bulk-sync:  The number of lines(ops * 2) to send to database at syncronized state - default: 100 
:elasticsearch-visitor:   Index visitor additional inside op data - default: false
:elasticsearch-basic-auth:   Send auth data i nthe form "username:password" - default: no auth ""
:elasticsearch-index-prefix:   A prefix for your indexes - default: "bitshares-"

Starting Node
-------------------

ES plugin is not active by default, we need to start it with the``plugins`` parameter. An example of starting a node with ES plugin on the simplest form with all the default options will be::

   programs/witness_node/witness_node --plugins "witness elasticsearch"

.. Note:: ``elasticsearch`` plugin and ``account_history`` plugin can not run the 2 at the same time.


Checking if it is working
--------------------------------

A few minutes after the node start the first batch of 5000 ops will be inserted to the database. If you are in a desktop linux you may want to install https://github.com/mobz/elasticsearch-head (only works with elasticsearch 5) and see the database from the web browser to make sure if it is working. This is optional.

If you only have command line available you can query the database directly throw curl as:::

	root@NC-PH-1346-07:~/bitshares/elastic/bitshares-core# curl -X GET 'http://localhost:9200/bitshares-*/data/_count?pretty=true' -H 'Content-Type: application/json' -d '
	{
		"query" : {
			"bool" : { "must" : [{"match_all": {}}] }
		}
	}
	'
	{
	  "count" : 360000,
	  "_shards" : {
		"total" : 5,
		"successful" : 5,
		"skipped" : 0,
		"failed" : 0
	  }
	}
	root@NC-PH-1346-07:~/bitshares/elastic/bitshares-core# 

	
360000 records are inserted at this point of the replay in ES, means it is working.

.. Attention:: **Important:** Replay with ES plugin will be always slower than the "save to ram" ``account_history_plugin`` so expect to wait a lot more to be in sync than usual. With the recommended hardware the synchronization can take 30 hours.


A synchronized node will look like this(screen capture 02/08/2018)::

	root@NC-PH-1346-07:~# curl -X GET 'http://localhost:9200/bitshares-*/data/_count?pretty=true' -H 'Content-Type: application/json' -d '
	{
		"query" : {
			"bool" : { "must" : [{"match_all": {}}] }
		}
	}
	'
	{
	  "count" : 391390823,
	  "_shards" : {
		"total" : 175,
		"successful" : 175,
		"skipped" : 0,
		"failed" : 0
	  }
	}
	root@NC-PH-1346-07:~# 

	
.. Attention::  **Important:** We have reports of the need of more than 250G of disk space at 2018-08-02 to save all the history and the logs for them. Please make sure you have enough disk before synchronizing.


Indexes
============

The plugin creates monthly indexes in the ES database, index names are as ``graphene-2016-05`` and contain all the operations made inside the monthly period.

List your indexes as::

	NC-PH-1346-07:~# curl -X GET 'http://localhost:9200/_cat/indices' 
	yellow open bitshares-2018-02 voS1uchzSxqxqkiaKNrEYg 5 1  18984984 0  10.8gb  10.8gb
	yellow open bitshares-2018-06 D6wyX58lRyG3QOflmPwJZw 5 1  28514130 0  15.6gb  15.6gb
	yellow open bitshares-2017-10 73xRTA-fSTm479H4kOENuw 5 1   9326346 0   5.2gb   5.2gb
	yellow open bitshares-2016-08 -MMp3VGGRZqG2YL1LQunbg 5 1    551835 0 270.1mb 270.1mb
	yellow open bitshares-2016-07 Ao56gO9LQ-asMhX50rbcCg 5 1    609087 0 303.2mb 303.2mb
	yellow open bitshares-2018-05 9xuof-PiRQWburpW8ZXHVg 5 1  29665610 0  17.3gb  17.3gb
	yellow open bitshares-2017-01 SpfwEzGcSoy9Hd6c6fzv2g 5 1   1197124 0   659mb   659mb
	yellow open bitshares-2017-12 tF5af4OvTLqcx3IYUJSQig 5 1  13244366 0   7.5gb   7.5gb
	yellow open bitshares-2016-03 yy91IvyATOCEoFHjgDbalg 5 1    597461 0 297.4mb 297.4mb
	yellow open bitshares-2015-12 z-ZAZqHsQL2EDNpf3_ghGA 5 1    349985 0 151.3mb 151.3mb
	yellow open bitshares-2017-07 OOr_xW4STsCm3sev1xtTRQ 5 1  17890903 0   9.6gb   9.6gb
	yellow open bitshares-2016-04 jt9q50ADQuylV4l25zGAaw 5 1    413798 0 205.6mb 205.6mb
	yellow open bitshares-2016-11 mWz7DpjSQyqJ_rL8gtMqWw 5 1    495556 0   260mb   260mb
	yellow open bitshares-2016-12 2qht_wrXTUmNqDvczpHYzw 5 1    917034 0 506.6mb 506.6mb
	yellow open bitshares-2016-10 vAMb0kW6Stqz6CNbuu7PEQ 5 1    416570 0 208.8mb 208.8mb
	yellow open bitshares-2015-11 ETNFuF3sTPe-gTSzX3bdIg 5 1    301079 0 131.9mb 131.9mb
	yellow open bitshares-2017-08 73Q2Asw-Rf228oQLoSCLGw 5 1   9916248 0   5.6gb   5.6gb
	yellow open bitshares-2016-05 3c95AvKcQk2puBwVt_HIqQ 5 1    498493 0   246mb   246mb
	yellow open bitshares-2017-02 lsiiz7PmS2q9_P2BQpNkNQ 5 1   1104282 0 586.7mb 586.7mb
	yellow open bitshares-2017-11 4pqwIRdWSwSe5198YNz-Nw 5 1  14107174 0     8gb     8gb
	yellow open bitshares-2018-07 fdmfXLqSTESODyLI_7cjXg 5 1 133879948 0  51.3gb  51.3gb
	yellow open bitshares-2016-06 Is11IdcnT8mfBPpoLUjJyw 5 1    656358 0 330.3mb 330.3mb
	yellow open bitshares-2018-04 MEA8fCsgSbOVXa0Z05cfsA 5 1  20940461 0  11.9gb  11.9gb
	yellow open bitshares-2018-03 fMjxhFwHSP-6ewrl0Ns6ZQ 5 1  20335546 0    12gb    12gb
	yellow open bitshares-2017-09 o-b2Bf3LR0-J1kUiv4FpHA 5 1  11075939 0   6.3gb   6.3gb
	yellow open bitshares-2018-01 jw9rYlmTSvuLC1hHcYyU4Q 5 1  19396703 0  11.2gb  11.2gb
	yellow open bitshares-2018-08 EDRxQvxhQJe3Vam_FxZMWg 5 1   8038498 0     3gb     3gb
	yellow open bitshares-2016-09 fo2AL0y7T_q_HtXEYCv35Q 5 1    409164 0 203.4mb 203.4mb
	yellow open bitshares-2016-01 3sjjs-4oQMm5HG-vUTuyoA 5 1    372772 0 168.7mb 168.7mb
	yellow open bitshares-2017-03 ZxjWksRyTaGstm6T2Kxl9A 5 1   2167788 0   1.1gb   1.1gb
	yellow open bitshares-2016-02 toWbFwI-RB2wEGrR8873rQ 5 1    468174 0 222.7mb 222.7mb
	yellow open bitshares-2017-05 IEZQ-rtmQU2kKNcRb58Egg 5 1  10278394 0   5.6gb   5.6gb
	yellow open bitshares-2017-04 S1h2eBGiS3quNJU7CqPR7Q 5 1   3316120 0   1.8gb   1.8gb
	yellow open bitshares-2017-06 0HYkECRbSwGDrmDFof8nqA 5 1  10795239 0     6gb     6gb
	yellow open bitshares-2015-10 XyKOlrTWSK6vQgdXm8SAtQ 5 1    161004 0  84.5mb  84.5mb
	root@NC-PH-1346-07:~# 

If you don't see any index here then something is wrong with the bitshares-core node setup with elasticsearch plugin.

Pre-Define Settings
=========================

By default data indexes will be created with default elasticsearch settings. Node owner can tweak the default settings for all the ``bitshares-*`` indexes before the addition of any data.

An example of a good index configuration is as follows::

  todo
  

Usage
==================

After your node is in sync you are in possession of a full node without the ram issues. A synchronized witness_node with ES will be using less than 10 gigs of ram::

	 total          8604280K
	root@NC-PH-1346-07:~# pmap 2183

What client side apps can do with this new data is kind of unlimited to client developer imagination but lets check some real world examples to see the benefits of this new feature.
What client side apps can do with this new data is kind of unlimited to client developer imagination but lets check some real world examples to see the benefits of this new feature.



Get operations by account, time and operation type
-----------------------------------------------------------

References: 

- https://github.com/bitshares/bitshares-core/issues/358
- https://github.com/bitshares/bitshares-core/issues/413
- https://github.com/bitshares/bitshares-core/pull/405 
- https://github.com/bitshares/bitshares-core/pull/379 
- https://github.com/bitshares/bitshares-core/pull/430 
- https://github.com/bitshares/bitshares-ui/issues/68

This is one of the issues that has been requested constantly. It can be easily queried with ES plugin by calling the _search endpoint doing:::

	curl -X GET 'http://localhost:9200/bitshares-*/data/_search?pretty=true' -H 'Content-Type: application/json' -d '
	{
		"query" : {
			"bool" : { "must" : [{"term": { "account_history.account.keyword": "1.2.282"}}, {"range": {"block_data.block_time": {"gte": "2015-10-26T00:00:00", "lte": "2015-10-29T23:59:59"}}}] }
		}
	}
	'

.. Note:: Response is removed from the samples to save space in the document. If you are here you may want to see the response in your own place.


Filter based on block number or block range
-----------------------------------------------------

https://github.com/bitshares/bitshares-core/issues/61

::

	curl -X GET 'http://localhost:9200/bitshares-*/data/_search?pretty=true' -H 'Content-Type: application/json' -d '
	{
		"query" : {
			"bool" : { "must" : [{"term": { "account_history.account.keyword": "1.2.356589"}}, {"range": {"block_data.block_num": {"gte": "17824289", "lte": "17824290"}                                                                                                                  
	}}] }                                                          
		}
	}
	'

Get operations by transaction hash
-------------------------------------------

Refs: https://github.com/bitshares/bitshares-core/pull/373

The ``get_transaction_id`` can be done as::

	curl -X GET 'http://localhost:9200/bitshares-*/data/_search?pretty=true' -H 'Content-Type: application/json' -d '
	{
		"query" : {
			"bool" : { "must" : [{"term": { "block_data.block_num": 19421114}},{"term": { "operation_history.trx_in_block": 0}}] }
		}
	}
	'

The above will return all ops inside trx, if you only need the trx_id field you can add ``source`` and just return the fields you need::

	curl -X GET 'http://localhost:9200/bitshares-*/data/_search?pretty=true' -H 'Content-Type: application/json' -d '
	{
		"_source": ["block_data.trx_id"],
		"query" : {
			"bool" : { "must" : [{"term": { "block_data.block_num": 19421114}},{"term": { "operation_history.trx_in_block": 0}}] }
		}
	}
	'

The ``get_transaction_from_id`` is very easy::

	curl -X GET 'http://localhost:9200/bitshares-*/data/_search?pretty=true' -H 'Content-Type: application/json' -d '
	{
		"query" : {
			"bool" : { "must" : [{"term": { "block_data.trx_id": "6f2d5064637391089127aa9feb36e2092347466c"}}] }
		}
	}
	'

Going forward
=======================

The reader will need to learn more about elasticsearch and lucene query language in order to make more complex queries.

All needed can be found at https://www.elastic.co/guide/en/elasticsearch/reference/6.2/index.html

By the same team of elasticsearch there is a front end named kibana (https://www.elastic.co/products/kibana). It is very easy to install and can do pretty good stuff like getting very detailed stats of the blockchain network.

More visitor code = more indexed data = more filters to use
----------------------------------------------------------------

Just as an example, it will be easy to index asset of trading operations by extending the visitor code of them. point 3 of https://github.com/bitshares/bitshares-core/issues/358 request trading pair, can be solved by indexing the asset of the trading ops as mentioned.

Remember ES already have all the needed info in the ``op`` text field of the ``operation_history`` object. Client can get all the ops of an account, loop throw them and convert the op string into json being able to filter by the asset or any other field needed. There is no need to index everything but it is possible.

Note on Duplicates
==========================

By using the ``op_type`` = ``create`` on each bulk line we send to the database and as we use an unique ID(ath id(2.9.X)) the plugin will not index any operation twice. If the node is on a replay, the plugin will start adding to database when it find a new record and never before.

Wrapper
==================

It is not recommended to expose the elasticsearch api fully to the internet. Instead, applications will connect to a wrapper for data:

https://github.com/oxarbitrage/bitshares-es-wrapper

Elasticsearch database will listen in localhost and the wrapper in the same machine will expose the reduced set of API calls to the internet.

-----------------

|

Contributor: @oxarbitrage
    

