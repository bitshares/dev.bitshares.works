
.. _how-to-setup-nignx:

How to Set up Nignx Webserver
===========================================


.. contents:: Table of Contents
   :local:
   
-------

We here use Nginx to deal with Ruby and provide access to the wallet files and the faucet. We use passenger to do so which unfortunatelly requires to compile nginx “manually”. Passenger fortunatelly provides a script that handles all the installation.

1. Setup
---------------------------------

::

    sudo apt-get install curl libcurl4-gnutls-dev
    cd ~/faucet
    gem install passenger
    sudo passenger-install-nginx-module

After using the recommended settings and waiting for the script to complete, we integrate nginx into our distribution via::

    sudo ln -s /opt/nginx/ /usr/local/nginx
    sudo ln -s /opt/nginx/conf/ /etc/nginx

	
2. Configuration
--------------------------------------

Nginx is configured using the freshly installed nginx.conf file::

    vi /opt/nginx/conf/nginx.conf

We have set it to be::

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

.. Note:: The parameters passenger_root and passenger_ruby may be different in your setup. Please compare with the default nginx.conf file to identify the proper directories.

We create an _upstream _called `websockets` which is uses to proxy the queries to `http://host/ws` directly to the websocket server. This allows to have a websocket address be available from the same port as the web wallet.

3. Running nginx as service
------------------------------------

We use a nginx service script and can install it according to::

    sudo wget https://raw.github.com/JasonGiedymin/nginx-init-ubuntu/master/nginx -O /etc/init.d/nginx
    sudo chmod +x /etc/init.d/nginx
    sudo update-rc.d -f nginx defaults

After that, nginx can be launched with::

    sudo service nginx start

	
.. Note:: To reload nginx.  ``sudo service nginx reload`` 



---------------

**** (ask) ****

6. Web Wallet
----------------------------------------------------


Since we need to provide a way for people to enter the network/blockchain, we need to install the web wallet into nginx.

6.1 Installation of Dependencies::

    sudo apt-get install git nodejs-legacy npm
    sudo npm install -g webpack coffee-script

6.2 Fetching the web wallet

Afterwards, we download the bitshares-ui repository from Cryptonomex and install the Node dependencies::

    git clone https://github.com/bitshares/bitshares-ui
    cd bitshares-ui/

    for I in dl web; do cd $I; npm install; cd ..; done

6.3 Configuration

Obtain the chain_id of the chain we are running.::

    $ curl --data '{"jsonrpc": "2.0", "method": "get_chain_properties", "params": [], "id": 1}' http://127.0.0.1:11011/rpc && echo

The chain id is used to let the web wallet know to which network it connects and how to deal with it. For this we modify the file dl/src/chain/config.coffee and add our blockchain::

    Test:
    core_asset: "TEST"
    address_prefix: "TEST"
    chain_id: "<chain-id>"

Furthermore, we need to tell our web wallet to which witness node to connect to. This can be done in the file dl/src/stores/SettingsStore.js.::

    connection: "ws://<host>/ws",
    faucet_address: "https://<host>",

    # also edit the "default" settings

6.4 Compilation

**Compile the web wallet**  

- This will generate the static files in the dist/ folder.

::

    cd web
    npm run build

|


