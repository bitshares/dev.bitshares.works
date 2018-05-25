

Public Testnet How to
========================

We took the opportunity to write the process of customizing and
deploying a graphene blockchain as well as the faucet, web wallet and
feed scripts into a tutorial for everyone to use.


1.Installation/Configuration of Witness
----------------------------------------

Fork CNX's code base
^^^^^^^^^^^^^^^^^^^^^^^^^^^^

.. code-block:: sh

   git clone https://github.com/cryptonomex/graphene
   mv graphene/ graphene-testnet
   cd graphene-testnet/
   git branch testnet
   git remote set-url origin https://github.com/BitSharesEurope/graphene-testnet
   git push origin testnet

Installation according to :doc:`../installation/Build`

Configuration
^^^^^^^^^^^^^^^^^^^^^^^^^^^^

Blockchain Parameters
~~~~~~~~~~~~~~~~~~~~~~~~~

The blockchain parameters can be modified in the
`libraries/chain/include/graphene/chain/config.hpp
<https://github.com/cryptonomex/graphene/blob/master/libraries/chain/include/graphene/chain/config.hpp>`_
file:

.. code-block:: sh

   vim libraries/chain/include/graphene/chain/config.hpp

Default Seed Node List
~~~~~~~~~~~~~~~~~~~~~~~~~~~

We can add a default list of seed nodes that the witness is supposed to
try to connect to in `libraries/app/application.cpp
<https://github.com/cryptonomex/graphene/blob/master/libraries/app/application.cpp>`_
and will add the IP/Address and port of the machine we are going to
setup later already:

    testnet.bitshares.eu:11010

The full changeset can be seen in the corresponding `git commit
<https://github.com/BitSharesEurope/graphene-testnet/commit/94f0a95be6f80cb5a7926ba6cc920dd795eb3a19>`_

Initial Compilation
^^^^^^^^^^^^^^^^^^^^^^^^^^

.. code-block:: sh

   cmake .
   make

We first need to compile the graphene toolkit so that we can let it
generate a plain genesis file in the proper format.



2.Genesis Configuration
------------------------

We will create a genesis file named ``my-genesis.json`` that contains
the genesis block:

.. code-block:: sh

   mkdir -p genesis
   programs/witness_node/witness_node --create-genesis-json genesis/my-genesis.json
   vim genesis/my-genesis.json

The ``my-genesis.json`` is the initial state of the network.

Genesis editing
~~~~~~~~~~~~~~~

If you want to customize the network's initial state, edit ``my-genesis.json``.
This allows you to control things such as:

- The accounts that exist at genesis, their names and public keys
- Assets and their initial distribution (including core asset)
- The initial values of chain parameters
- The account / signing keys of the ``init`` witnesses (or in fact any account at all).

The chain ID is a hash of the genesis state.  All transaction signatures
are only valid for a single chain ID.  So editing the genesis file will
change your chain ID, and make you unable to sync with all existing
chains (unless one of them has exactly the same genesis file you do).

Writing final genesis
^^^^^^^^^^^^^^^^^^^^^^^^^

We now copy our gensis template file over to the graphene root
directory:::

    $ cp genesis/my-genesis.json genesis.json
    $ vim genesis.json
    $ git add genesis.json
    $ git commit -m "Added genesis.json"

The **initial timestamp** needs to be pasted into ``genesis.json`` file
in the initial_timestamp field. Choose it relatively close to the future
where you can generate the genesis block (e.g. now plus 10 minutes).

Including Genesis into the binaries
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

To let the binaries know about your new genesis block, we need to
recompile it and provide ``cmake`` with the parameter to identify the
genesis block properly:

::

    $ make clean
    $ find . -name "CMakeCache.txt" | xargs rm -f
    $ find . -name "CMakeFiles" | xargs rm -Rf
    $ cmake -DGRAPHENE_EGENESIS_JSON="$(pwd)/genesis.json" .

You can add the ``GRAPHENE_EGENESIS_JSON`` to the default parameters by
adding:

::

    set(GRAPHENE_EGENESIS_JSON "${CMAKE_CURRENT_SOURCE_DIR}/genesis.json" )

to the ``CMakeLists.txt`` file. This way, you don't need to provide this
parameter all the time.


3.Initializing Blockchain
---------------------------------

Initializing the genesis block
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

We initialize the blockchain an generate our first blocks.

The ``--enable-stale-production`` flag tells the ``witness_node`` to
produce on a chain with zero blocks or very old blocks.  We specify the
``--enable-stale-production`` parameter on the command line as we will
not normally need it (although it can also be specified in the config
file).

.. code-block:: sh

   programs/witness_node/witness_node --genesis-json genesis.json \
                                      --enable-stale-production \
                                      --data-dir data/testnet

We will already see our chain id:::

    Started witness node on a chain with 0 blocks.
    Chain ID is cf307110d029cb882d126bf0488dc4864772f68d9888d86b458d16e6c36aa74b

.. note:: If other witness produces blocks and witness participation is
          high enough, subsequent runs which connect to an existing
          witness node over the p2p network, or which get blockchain
          state from an existing data directory, need not have the
          ``--enable-stale-production`` flag.

Setting up block production
^^^^^^^^^^^^^^^^^^^^^^^^^^^

Let's create a very basic configuration file in `data/testnet/config.ini`:

::

    $ mkdir -p data/testnet
    $ vim data/testnet/config.ini

All we put into the configuration file is the ids and the keys for the
witnesses so that we can start producing blocks 

::

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
   # For each witness, add pubkey and private key:
   private-key = ["GPH6MRyAjQq8ud7hVNYcfnVPJqcVpscN5So8BhtHuGYqET5GDW5CV","5KQwrPbwdL6PhXujxW37FSSQZ1JiwsST4cqQzDeyXtP79zkvFD3"]
   private-key = [<pubkey>,<privkey>]
   private-key = [<pubkey>,<privkey>]
   private-key = [<pubkey>,<privkey>]
   private-key = [<pubkey>,<privkey>]
   private-key = [<pubkey>,<privkey>]
   private-key = [<pubkey>,<privkey>]
   private-key = [<pubkey>,<privkey>]
   private-key = [<pubkey>,<privkey>]
   private-key = [<pubkey>,<privkey>]
   private-key = [<pubkey>,<privkey>]

This authorizes the ``witness_node`` to produce blocks on behalf of the
listed ``witness-id``'s, and specifies the private key needed to sign
those blocks.  Normally each witness would be on a different node, but
for the purposes of this testnet, we will start out with all witnesses
signing blocks on a single node.

.. note:: The setting ``rpc-endpoint = 0.0.0.0:11011`` will open up
          the RPC-port `11011` to connect a cli-wallet or web wallet to
          it. With the ``p2p-endpoint = 0.0.0.0:11010`` being accessible
          from the internet, this node can be used as seed node.

Embedding the Genesis block (optional)
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

Now that we have the blockchain established and the used correct genesis
block, we can have it embedded into the binaries directly. For that
reasons we have moved it into the root directory and called it
``genesis.json`` for the default compile toolchain to catch it
automatically. We recompile to include the genesis block with:

.. code-block:: sh

   make clean
   find . -name "CMakeCache.txt" | xargs rm -f
   find . -name "CMakeFiles" | xargs rm -Rf
   cmake -DCMAKE_BUILD_TYPE=Release .

Deleting caches will reset all ``cmake`` variables, so if you have used
instructions like :doc:`../installation/Build` which tells you to
set other ``cmake`` variables, you will have to add those variables
to the ``cmake`` line above.

Embedding the genesis copies the entire content of genesis block
into the ``witness_node`` binary, and additionally copies the chain ID
into the ``cli_wallet`` binary.  Embedded genesis allows the following
simplifications to the subsequent instructions:

* You need **not** specify the genesis file on the witness node command
  line, or in the witness node configuration file.
* You need **not** specify the chain ID on the ``cli_wallet`` command line
  when starting a new wallet.
  
  
4.Connecting a CLI wallet
--------------------------------

We will now show how to connect a cli-wallet to the new blockchain and
generate our first transaction on the new blockchain.

Creating a wallet
^^^^^^^^^^^^^^^^^^^^^^^

In order to create a wallet, you must specify the previously setup
server. With the witness node's default access control settings, a blank
username and password will suffice:

.. code-block:: sh

    programs/cli_wallet/cli_wallet --wallet-file my-wallet.json -s ws://127.0.0.1:11011 -H 127.0.0.1:8090 -r 127.0.0.1:8099

.. note:: The parameter `-H` is required so that we can interface with
          the cli wallet via RPC-HTTP-JSON, later while `-r` will open a
          port for the Ruby-based faucet.

Before continuing, we should set a password.  This password is used
to encrypt the private keys in the wallet.  We will use the word
``supersecret`` in this example.::

    >>> set_password supersecret

Gaining access to stake
^^^^^^^^^^^^^^^^^^^^^^^^^^

In Graphene, balances are contained in accounts.  To claim an account
that exists in the Graphene genesis, use the ``import_key`` command:::

    >>> unlock supersecret
    >>> import_key <name> "<wifkey>"

Funds are stored in genesis balance objects.  These funds can be
claimed, with no fee, using the ``import_balance`` command.::

    >>> import_balance <name> ["*"] true

Creating accounts
^^^^^^^^^^^^^^^^^^^^

Creating an account requires lifetime member (LTM) status.  To upgrade
to LTM, use the ``upgrade_account`` command:::

    >>> upgrade_account faucet true

We can now register an account.  The ``register_account`` command
allows you to register an account using only a public key:::

    >>> register_account alpha GPH4zSJHx7D84T1j6HQ7keXWdtabBBWJxvfJw72XmEyqmgdoo1njF GPH4zSJHx7D84T1j6HQ7keXWdtabBBWJxvfJw72XmEyqmgdoo1njF faucet faucet 0 true
    >>> transfer faucet alpha 100000 CORE "here is the cash" true

We can now open a new wallet for ``alpha`` user:::

    >>> import_key alpha 5HuCDiMeESd86xrRvTbexLjkVg2BEoKrb7BAA5RLgXizkgV3shs
    >>> upgrade_account alpha true
    >>> create_witness alpha "http://www.alpha" true

The ``get_private_key`` command allows us to obtain the private key corresponding
to the block signing key:::

    >>> get_private_key GPH6viEhYCQr8xKP3Vj8wfHh6WfZeJK7H9uhLPDYWLGCRSj5kHQZM



5.Establishing a Committee
-----------------------------

Our network, of course, needs a committee. We need to initialy create
new accounts and let them apply as committee member.

Creating members
^^^^^^^^^^^^^^^^^^

::

    create_account_with_brain_key com0 com0 faucet faucet true
    create_account_with_brain_key com1 com1 faucet faucet true
    create_account_with_brain_key com2 com2 faucet faucet true
    create_account_with_brain_key com3 com3 faucet faucet true
    create_account_with_brain_key com4 com4 faucet faucet true
    create_account_with_brain_key com5 com5 faucet faucet true
    create_account_with_brain_key com6 com6 faucet faucet true

Upgrading members
^^^^^^^^^^^^^^^^^^^^

Since only lifetime members can be committee members, we need to fund
these accounts and upgrade them accordingly:

::

    transfer faucet com0 100000 CORE "some cash" true
    transfer faucet com1 100000 CORE "some cash" true
    transfer faucet com2 100000 CORE "some cash" true
    transfer faucet com3 100000 CORE "some cash" true
    transfer faucet com4 100000 CORE "some cash" true
    transfer faucet com5 100000 CORE "some cash" true
    transfer faucet com6 100000 CORE "some cash" true
    upgrade_account com0 true
    upgrade_account com1 true
    upgrade_account com2 true
    upgrade_account com3 true
    upgrade_account com4 true
    upgrade_account com5 true
    upgrade_account com6 true

Registering as committee member
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

We can apply for committee with `create_committee_member`:

::

    create_committee_member com0 "" true
    create_committee_member com1 "" true
    create_committee_member com2 "" true
    create_committee_member com3 "" true
    create_committee_member com4 "" true
    create_committee_member com5 "" true
    create_committee_member com6 "" true

Voting with faucet account
^^^^^^^^^^^^^^^^^^^^^^^^^^^

All we need to do know is vote for our own committee members:

::

    vote_for_committee_member faucet com0 true true
    vote_for_committee_member faucet com1 true true
    vote_for_committee_member faucet com2 true true
    vote_for_committee_member faucet com3 true true
    vote_for_committee_member faucet com4 true true
    vote_for_committee_member faucet com5 true true
    vote_for_committee_member faucet com6 true true

	

6.Web Wallet
---------------

Since we need to provide a way for people to enter the
network/blockchain, we need to install the web wallet into nginx.

Dependencies
^^^^^^^^^^^^^^^^^

We first install everything we need to compile the web wallet:

.. code-block:: sh

    sudo apt-get install git nodejs-legacy npm
    sudo npm install -g webpack coffee-script

Fetching the web wallet
^^^^^^^^^^^^^^^^^^^^^^^^

Afterwards, we download the `graphene-ui` repository from Cryptonomex
and install the Node dependencies.

.. code-block:: sh

    git clone https://github.com/cryptonomex/graphene-ui
    cd graphene-ui/

    for I in dl web; do cd $I; npm install; cd ..; done

Configuration
^^^^^^^^^^^^^^^^^

What we need now is the `chain_id` of the chain we have running. We can
get it by executing:::

    $ curl --data '{"jsonrpc": "2.0", "method": "get_chain_properties", "params": [], "id": 1}' http://127.0.0.1:11011/rpc && echo

The chain id is used to let the web wallet know to which network it
connects and how to deal with it. For this we modify the file
`dl/src/chain/config.coffee` and add our blockchain:::

        Test:
            core_asset: "TEST"
            address_prefix: "TEST"
            chain_id: "<chain-id>"

Furthermore, we need to tell our web wallet to which witness node to
connect to. This can be done in the file
`dl/src/stores/SettingsStore.js`. ::

    connection: "ws://<host>/ws",
    faucet_address: "https://<host>",

    # also edit the "default" settings

Compilation
^^^^^^^^^^^^^^

We compile the web wallet with

.. code-block:: sh

    cd web
    npm run build

which will generate the static files in the `dist/` folder.


7.Setting up the Faucet
------------------------

In order to allow people that do not have funds yet to enter the system,
we need to setup a faucet. Here, we will also use `mina` as our
**deployment tool** for a productive installation.

Installation of Dependencies
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

we first install every other dependency that is needed and not yet
installed

.. code-block:: sh

    sudo apt-get install mysql-server libmysqlclient-dev
    # put a master password for mysql

We also need to install a decently recent version of Ruby:

.. code-block:: sh

    cd
    git clone git://github.com/sstephenson/rbenv.git .rbenv
    echo 'export PATH="$HOME/.rbenv/bin:$PATH"' >> ~/.bashrc
    echo 'eval "$(rbenv init -)"' >> ~/.bashrc
    exec $SHELL

    git clone git://github.com/sstephenson/ruby-build.git ~/.rbenv/plugins/ruby-build
    echo 'export PATH="$HOME/.rbenv/plugins/ruby-build/bin:$PATH"' >> ~/.bashrc
    exec $SHELL

    git clone https://github.com/sstephenson/rbenv-gem-rehash.git ~/.rbenv/plugins/rbenv-gem-rehash

    sudo rbenv install 2.2.3
    sudo rbenv global 2.2.3
    sudo gem install bundler

Get the Source
^^^^^^^^^^^^^^^

One implementation of a faucet can be downloaded from Cryptonomex:

.. code-block:: sh

    git clone https://github.com/cryptonomex/faucet
    cd faucet
    sudo bundle   # ignore warnings


Configuration
^^^^^^^^^^^^^^

We need to configure

* the faucet itself,
* rails API secrets,
* database access,
* and the deployment tool, mina.

Faucet
~~~~~~~~~~~~

All required settings are in `config/faucet.yml` which has an example
file:

.. code-block:: sh

    cp config/faucet-example.yml config/faucet.yml 
    vim config/faucet.yml 

Rails API
~~~~~~~~~~~

Rails needs to know a secret for their internals, we can get a new one
with `rake secret`. Put it into the corresponding lines in the config
file.

.. code-block:: sh

    cp config/secrets-example.yml config/secrets.yml
    rake secret
    vim config/secrets.yml 

Database Access
~~~~~~~~~~~~~~~~~~

The default configuration for the database access expects an empty root
password for MySQL. Hence, if you have set a different password, you
need to provide it here.

.. code-block:: sh

    vim config/database.yml 

We generate our databases with:

.. code-block:: sh

    rake db:create; rake db:migrate; rake db:seed
    RAILS_ENV=production bundle exec rake db:create db:schema:load

Database Settings
~~~~~~~~~~~~~~~~~~~~

We also need to add an entry to the database so that page loads and
referrals work properly:

1. Go to ``/www/current``
2. execute: ``rails db``, a mysql console will open
3. Execute: ``insert into widgets set allowed_domains='testnet.bitshares.eu';``  (replace the domain with your domain)

Mina deployment
~~~~~~~~~~~~~~~~~~~~~

Mina is used to deploy the faucet properly and move all the required
files over to the production machine (may be the same machine).

.. code-block:: sh

   cp config/deploy-example.yml config/deploy.yml
   vim config/deploy.yml

Make sure to add your ssh-pub key to your authorized file so that mina
can deploy to the corrseponding machine.

Now, all we need to do is create the public directly that is served and
copy/link the configuration file to the deployment's shared files:

.. code-block:: sh
   
   sudo mkdir /www
   sudo chown -R gph:gph /www

   mina setup
   ln -s $HOME/faucet/config/faucet.yml /www/shared/config/
   ln -s $HOME/faucet/config/secrets.yml /www/shared/config/
   ln -s $HOME/faucet/config/database.yml /www/shared/config/

We deploy mina and the wallet with:

.. code-block:: sh

   ln -s $HOME/graphene-ui/web/dist $HOME/faucet/public/wallet
   mina deploy
   mina wallet

Update wallet
~~~~~~~~~~~~~~~~~~~~~~

If you have modified something in the wallet, you need to recompile the
Javascript/HTML files and re-deploy the wallet with:

.. code-block:: sh

   # re-compile
   cd ~/graphene-ui/web
   git pull # fetch changes from origin
   npm run build

   # deploy
   cd ~/faucet
   mina wallet



8.Nginx Webserver
---------------------

We here use Nginx to deal with Ruby and provide access to the wallet
files and the faucet. We use `passenger` to do so which unfortunatelly
requires to compile nginx "manually". Passenger fortunatelly provides a
script that handles all the installation.

Setup
^^^^^^^^^^^^^^

.. code-block:: sh

   sudo apt-get install curl libcurl4-gnutls-dev 
   cd ~/faucet
   gem install passenger
   sudo passenger-install-nginx-module

After using the recommended settings and waiting for the script to
complete, we integrate nginx into our distribution via

.. code-block:: sh

   sudo ln -s /opt/nginx/ /usr/local/nginx
   sudo ln -s /opt/nginx/conf/ /etc/nginx

Configuration
^^^^^^^^^^^^^^^^^^

Nginx is configured using the freshly installed `nginx.conf` file:

.. code-block:: sh

   vi /opt/nginx/conf/nginx.conf 

We have set it to be:

::

    user gph;
    worker_processes  4;

    events {
        worker_connections  1024;
    }

    http {
        passenger_root /home/gph/.rbenv/versions/2.2.3/lib/ruby/gems/2.2.0/gems/passenger-5.0.23;
        passenger_ruby /home/gph/.rbenv/versions/2.2.3/bin/ruby;
        passenger_max_request_queue_size 1000;

        include       mime.types;
        default_type  application/octet-stream;

        access_log  /www/logs/access.log;
        error_log  /www/logs/error.log;
        log_not_found off;

        sendfile        on;
        #tcp_nopush     on;

        keepalive_timeout  65;

        gzip  on;

        upstream websockets {
          ## Put the witness node's websocket rpc port here:
          server localhost:11011;
        }

        server {
            listen 80;
            server_name localhost;

            location ~ ^/[\w\d\.-]+\.(js|css|dat|png|json)$ {
                root /www/current/public/;
                try_files $uri /wallet$uri =404;
            }

            location ~ /ws/? {
                access_log off;
                proxy_pass http://websockets;
                proxy_set_header X-Real-IP $remote_addr;
                proxy_set_header Host $host;
                proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
                proxy_http_version 1.1;
                proxy_set_header Upgrade $http_upgrade;
                proxy_set_header Connection "upgrade";
            }

            location / {
                passenger_enabled on;
                root /www/current/public/;
            }

        }

    }

.. note:: The parameters `passenger_root` and `passenger_ruby` may be
          different in your setup. Please compare with the default
          `nginx.conf` file to identify the proper directories.

We create an *upstream* called ``websockets`` which is uses to proxy the
queries to ``http://host/ws`` directly to the websocket server. This
allows to have a websocket address be available from the same port as
the web wallet.

Running nginx as serice
^^^^^^^^^^^^^^^^^^^^^^^^

We use a nginx serice script and can install it according to: 

.. code-block:: sh

   sudo wget https://raw.github.com/JasonGiedymin/nginx-init-ubuntu/master/nginx -O /etc/init.d/nginx
   sudo chmod +x /etc/init.d/nginx
   sudo update-rc.d -f nginx defaults

After that, nginx can be launched with

.. code-block:: sh

   sudo service nginx start


9.Installation of Python Library
----------------------------------

Requirements
^^^^^^^^^^^^^^^^^

First, we need to install `pip3` which will deal with the packages
dependencies:

.. code-block:: sh

   sudo apt-get install python3-pip

Installation
^^^^^^^^^^^^^^

Afterwards, we can fetch the python libraries from github and install
it:

.. code-block:: sh

   git clone https://github.com/xeroc/python-graphenelib
   cd python-graphenelib/

   pip3 install --user -r requirements.txt
   python3 setup.py install --user

Usage
^^^^^^^^^^

The usage of this library is well documented on

* https://readthedocs.org/projects/python-graphenelib/



10.Create MPAs/UIAs
------------------------

We now create some Market Pegged assets and construct the price feed.

* Create Market Pegged Assets:
  https://github.com/BitSharesEurope/testnet-pythonscripts/blob/master/create_mpa.py
* Fund `init*` witnessses:
  https://github.com/BitSharesEurope/testnet-pythonscripts/blob/master/fund-inits.py
* Price feed scripts:

  * https://github.com/BitSharesEurope/testnet-pythonscripts/blob/master/feed.last.py
  * https://github.com/BitSharesEurope/testnet-pythonscripts/blob/master/feed.parity.py
  * https://github.com/BitSharesEurope/testnet-pythonscripts/blob/master/feed.random.py

We can use `cron` to runt he price feed script on a regular basis:

::

    */15 * * * * /usr/bin/python3 /home/gph/testnet-pythonscripts/feed.last.py
    */15 * * * * /usr/bin/python3 /home/gph/testnet-pythonscripts/feed.random.py
    0    * * * * /usr/bin/python3 /home/gph/testnet-pythonscripts/feed.parity.py

	
|


