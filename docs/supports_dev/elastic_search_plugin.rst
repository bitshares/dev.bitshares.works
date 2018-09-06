
.. _elastic-search:

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
- The number of github issues in regards to new api calls to lookup account history in different ways. elasticsearch-plugin will allow users to search account history querying directly into the database without the bitshares-core team creating and maintaining API calls for every case.

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

The `elasticsearch-plugin` is uses the basics of `account_history_plugin`.

Here is how the current account_history plugin works basically:

- with every signed block arriving to the plugin the ops are extracted from it.
- each op is added to ohi(operation history index) and to ath(account transaction history index).
- both indexes keep growing as new block gets in.
- with the memory reduction techniques currently available the 2 indexes can remove early ops of accounts reducing ram size.

And now what `elasticsearch-plugin` attempts to do:

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

The name of the created index in ES will be `graphene`

Accessing data inside operations
--------------------------------------

There are cases where data coming from account transaction history index, operation history index and block data is not enough. We may want to index fields inside operations themselves like the fees, the asset id or the amount of transfer to make fast queries into them.

Data inside operations is saved as a text fields into ES, this means that we can't fast search into them as the data is not indexed, we can, still search by other data and filter converting the op text to json in client side. This is possible and relatively easy so please consider.

Still, a workaround the limitation is available. A `visitor` that can be turned on/off by the command line. As an example something in common all ops have is a fee field with asset_id and amount. In `elasticserch-plugin` v1 when visitor is `true` this 2 values will be saved meaning clients can know total chain fees collected in real time, total fees in asset, fees by op among other things.

As a poc we also added amount and asset_id of transfer operations to illustrate how easy is to index more data for any competent graphene developer.

## Hardware needed

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

then: `apt-get update`

if you don't have add-apt-repository command you can install it by::

    apt-get install software-properties-common

install java 8::

    apt-get install oracle-java8-installer


	
Install ES:
----------------

Get version 6 file at: https://artifacts.elastic.co/downloads/elasticsearch/elasticsearch-6.2.0.zip

**Note:** Plugin works with currently last stable version of elastic(6.2.0)

Please do this as a non root user as ES will not run as root.

download as::

`wget https://artifacts.elastic.co/downloads/elasticsearch/elasticsearch-6.2.0.zip`

unzip::

`unzip elasticsearch-6.2.0.zip`

and run::

    cd elasticsearch-6.2.0.zip/
    ./bin/elasticsearch

You can put this as a service, the binary haves a `--daemonize` option, can run inside screen or any other option that suits you in order to keep the database running.

Please note ES does not run a the root user, if you are a root user you need to first make a normal user account by::

`adduser elastic`

After created: `su elastic`. Execute the `wget` and `unzip` commands from above as normal user in created user home dir.

Install curl
---------------------

We need curl to send requests from the c++ plugin into the ES database::

    apt-get install libcurl4-openssl-dev

Running
================

Make sure ES is running, can start it by::

    ./elasticsearch --daemonize

ES will listen on localhost port 9200 `127.0.0.1:9200`

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

| parameter |    |
|-----------|----|
| elasticsearch-node-url | The url od elasticsearch - default: http://localhost:9200/ |
| elasticsearch-bulk-replay | The number of lines(ops * 2) to send to database in replay state - default: 10000 |
| elasticsearch-bulk-sync | The number of lines(ops * 2) to send to database at syncronized state - default: 100 |
| elasticsearch-logs | Save logs to database - default: true |
| elasticsearch-visitor | Index visitor additional inside op data - default: true |

Starting Node
-------------------

ES plugin is not active by default, we need to start it with the plugins parameter. An example of starting a node with ES plugin may be::

    programs/witness_node/witness_node --data-dir data/my-blockprod 
                                       --rpc-endpoint "127.0.0.1:8090" 
                                       --plugins "witness elasticsearch market_history" 
                                       --elasticsearch-bulk-replay 10000 
                                       --elasticsearch-logs true 
                                       --elasticsearch-visitor true

**Note:** elasticsearch plugin and account_history plugin can not run the 2 at the same time.

Checking if it is working
--------------------------------

A few minutes after the node start the first batch of 5000 ops will be inserted to the database. If you are in a desktop linux you may want to install https://github.com/mobz/elasticsearch-head and see the database from the web browser to make sure if it is working. This is optional.

If you only have command line available you can query the database directly throw curl as::

    root@NC-PH-1346-07:~/bitshares/elastic/bitshares-core# curl -X GET 'http://localhost:9200/graphene-*/data/_count?pretty=true' -H 'Content-Type: application/json' -d '
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

**Important: Replay with ES plugin will be always slower than the "save to ram" `account_history_plugin` so expect to wait more to be in sync than usual.**

A synchronized node will look like this(screen capture 20/12/2017)::

    root@NC-PH-1346-07:~# curl -X GET 'http://localhost:9200/graphene-*/data/_count?pretty=true' -H 'Content-Type: application/json' -d '
    {
        "query" : {
            "bool" : { "must" : [{"match_all": {}}] }
        }
    }
    '
    {
      "count" : 107156155,
      "_shards" : {
        "total" : 135,
        "successful" : 135,
        "skipped" : 0,
        "failed" : 0
      }
    }
    root@NC-PH-1346-07:~# 

**Important: We have reports of the need of more than 200G of disk space at 2018-03-20 to save all the history and the logs for them. Please make sure you have enough disk before synchronizing.**

**Important: The synchronization of a full live blockchain from scratch can take up to 3 days! Please be prepared for big waiting time.**

Indexes
============

The plugin creates monthly indexes in the ES database, index names are as graphene-2016-05 and contain all the operations made inside the monthly period.

List your indexes as::

    NC-PH-1346-07:~# curl -X GET 'http://localhost:9200/_cat/indices' 
    yellow open logs             PvSvmtcFSCCF_-3HBjJaXg 5 1   358808 0   5.8gb   5.8gb
    yellow open graphene-2017-09 ZUUi5xeuTeSb7QPP1Pnk0A 5 1 11075939 0   8.4gb   8.4gb
    yellow open graphene-2017-04 HtCKnM_UTLyDWBR7-PopGQ 5 1  3316120 0   2.4gb   2.4gb
    yellow open graphene-2017-10 SAUFtI0CRx6MnISukeqA-Q 5 1  9326346 0   7.1gb   7.1gb
    yellow open graphene-2017-07 CTWIim51SZy0Ir55sjlT7w 5 1 17890903 0  12.8gb  12.8gb
    yellow open graphene-2017-01 sna676yfR1OT5ww9HRiVZw 5 1  1197124 0 917.7mb 917.7mb
    yellow open graphene-2016-06 FZ_FyTLwQCKObjKgLQcF3w 5 1   656358 0 473.9mb 473.9mb
    yellow open graphene-2017-03 Ogsw0PlbRAWeHRS86o4vTA 5 1  2167788 0   1.6gb   1.6gb
    yellow open graphene-2017-11 -9-oz-DRRTOMLdZVOUV5xw 5 1 14107174 0  10.8gb  10.8gb
    yellow open graphene-2017-02 ZE3LxFWkTeO7CBsxMMmIFQ 5 1  1104282 0 822.4mb 822.4mb
    yellow open graphene-2017-12 bZRFc5aLSlqbBMYEnKmF7Q 5 1  8758628 0     8gb     8gb
    yellow open graphene-2015-11 iXyIZkj_T0O-NjPxEf0L_A 5 1   301079 0 193.1mb 193.1mb
    yellow open graphene-2017-05 ipVA6yEKTH68nEucBIZP7w 5 1 10278394 0   7.5gb   7.5gb
    yellow open graphene-2017-08 Z2C_pciwQSevJzcl9kRhhQ 5 1  9916248 0   7.5gb   7.5gb
    yellow open graphene-2016-04 vtQSi-kdQ0OblRq2naPu-A 5 1   413798 0 297.4mb 297.4mb
    yellow open graphene-2016-12 hsohZ0cfTGSPPVWUM_Fgeg 5 1   917034 0 711.3mb 711.3mb
    yellow open graphene-2016-01 PzjbXCXFShCzZFyTBgv2IQ 5 1   372772 0 247.1mb 247.1mb
    yellow open graphene-2016-03 8j1yyLIeTUWXb6TZntqQhg 5 1   597461 0   431mb   431mb
    yellow open graphene-2016-08 6qBByeOPSkCDqWYrOR_qPg 5 1   551835 0 389.3mb 389.3mb
    yellow open graphene-2015-10 DTHVtNpIT2KAoMiO4-c61w 5 1   161004 0 122.2mb 122.2mb
    yellow open graphene-2017-06 Jei8GvRYSkif4zZ-V9mFRg 5 1 10795239 0   8.1gb   8.1gb
    yellow open .kibana          8q5behLlQRi_hxKSWurYDw 1 1        7 0  42.7kb  42.7kb
    yellow open graphene-2016-05 s7Ie2crTS_uismCP5U2dIQ 5 1   498493 0 353.4mb 353.4mb
    yellow open graphene-2016-07 7yx03R2hQX6XyOR8Sdu7ZA 5 1   609087 0 435.8mb 435.8mb
    yellow open graphene-2016-02 0SVSrhn3QK2AxWNv9NRASQ 5 1   468174 0 321.8mb 321.8mb
    yellow open graphene-2016-10 C28juWi8Rf-1A0h2YnShBw 5 1   416570 0 299.9mb 299.9mb
    yellow open graphene-2016-09 3rHI_5HFR3SFl9bhroL5EA 5 1   409164 0 296.1mb 296.1mb
    yellow open graphene-2015-12 aLlUxO_tSG-CMC3SPcFgkw 5 1   349985 0 222.4mb 222.4mb
    yellow open graphene-2016-11 i00upS94Ruii1zJYXI0S9Q 5 1   495556 0 367.3mb 367.3mb
    root@NC-PH-1346-07:~# 

If you don't see any index here then something is wrong with the bitshares-core node setup with elasticsearch plugin.

Pre-Define Settings
=========================

By default data indexes will be created with default elasticsearch settings. Node owner can tweak the default settings for all the graphene-* indexes before the addition of any data.

An example of a good index configuration is as follows::

    $ curl -XPUT 'http://localhost:9200/_template/graphene' -d '{
      "index_patterns" : ["graphene-*"],
      "settings": { "number_of_shards": 2,
        "index": {
          "translog": {
            "retention": {
              "size": "512mb", "age": "300s"
            }
          }
        }
      }
    }' -H 'Content-Type: application/json

@HarukaMa said: "This template would apply those settings to all newly created index prefixed with graphene-. It's one time so there will be no need to specify them for every new index. In this settings I have also reduced translog age to 15min to minimize the storage usage, but I think that's optional."

Usage
==================

After your node is in sync you are in possession of a full node without the ram issues. A synchronized witness_node with ES will be using less than 10 gigs of ram::

     total          9817628K
    root@NC-PH-1346-07:~# pmap 2183

Compare against a traditional full node::

     total         60522044K
    [bitshares@lantea ~]$

Please note start command was with `markets_history` plugin activated, only consensus data should use even less than 10 gigs.

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

This is one of the issues that has been requested constantly. It can be easily queried with ES plugin by calling the _search endpoint doing::

    curl -X GET 'http://localhost:9200/graphene-*/data/_search?pretty=true' -H 'Content-Type: application/json' -d '
    {
        "query" : {
            "bool" : { "must" : [{"term": { "account_history.account.keyword": "1.2.282"}}, {"range": {"block_data.block_time": {"gte": "2015-10-26T00:00:00", "lte": "2015-10-29T23:59:59"}}}] }
        }
    }
    '

**Note:** Response is removed from the samples to save space in the document. If you are here you may want to see the response in your own place.

Filter based on block number or block range
-----------------------------------------------------

https://github.com/bitshares/bitshares-core/issues/61

::

    curl -X GET 'http://localhost:9200/graphene-*/data/_search?pretty=true' -H 'Content-Type: application/json' -d '
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

The `get_transaction_id` can be done as::

    curl -X GET 'http://localhost:9200/graphene-*/data/_search?pretty=true' -H 'Content-Type: application/json' -d '
    {
        "query" : {
            "bool" : { "must" : [{"term": { "block_data.block_num": 19421114}},{"term": { "operation_history.trx_in_block": 0}}] }
        }
    }
    '

The above will return all ops inside trx, if you only need the trx_id field you can add `source` and just return the fields you need::

    curl -X GET 'http://localhost:9200/graphene-*/data/_search?pretty=true' -H 'Content-Type: application/json' -d '
    {
        "_source": ["block_data.trx_id"],
        "query" : {
            "bool" : { "must" : [{"term": { "block_data.block_num": 19421114}},{"term": { "operation_history.trx_in_block": 0}}] }
        }
    }
    '

The `get_transaction_from_id` is very easy::

    curl -X GET 'http://localhost:9200/graphene-*/data/_search?pretty=true' -H 'Content-Type: application/json' -d '
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

Remember ES already have all the needed info in the op text field of the operation_history object. Client can get all the ops of an account, loop throw them and convert the op string into json being able to filter by the asset or any other field needed. There is no need to index everything but it is possible.

Note on Duplicates
==========================

By using the `op_type` = `create` on each bulk line we send to the database and as we use an unique ID(ath id(2.9.X)) the plugin will not index any operation twice. If the node is on a replay, the plugin will start adding to database when it find a new record and never before.

Wrapper
==================

It is not recommended to expose the elasticsearch api fully to the internet. Instead, applications will connect to a wrapper for data::

https://github.com/oxarbitrage/bitshares-es-wrapper

Elasticsearch database will listen in localhost and the wrapper in the same machine will expose the reduced set of API calls to the internet.

-----------------

#### Contributor:
- @oxarbitrage
    

|

