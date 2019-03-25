
.. _w-haproxy-and-bitshares:

HaProxy and BitShares
========================


.. contents:: Table of Contents
   :local:
   
-------	
		

Using BitShares-core behind HaProxy
-----------------------------------

This page explains how to use HaProxy as loadbalancer for bitshares-core.

Global and defaults
^^^^^^^^^^^^^^^^^^^^^^^^^

The haproxy config ``/etc/haproxy/haproxy.cfg`` comes with a global section and defaults. The following appear to be sound defaults with respect to security standards of 2018:


.. code-block::

	 global
		log /dev/log local0
		log /dev/log local1 notice
		chroot /var/lib/haproxy
		# management/stats socket
		stats socket /run/haproxy/admin.sock mode 660 level admin
		stats timeout 30s
		user haproxy
		group haproxy
		daemon

		ca-base /etc/ssl/certs
		crt-base /etc/ssl/private

		# Generated via "openssl dhparam -out dhparam.pem 1024"
		ssl-dh-param-file /etc/haproxy/dhparams.pem

		# Recent Cipher suites
		ssl-default-bind-ciphers ECDH+AESGCM:DH+AESGCM:ECDH+AES256:DH+AES256:ECDH+AES128:DH+AES:RSA+AESGCM:RSA+AES:!aNULL:!MD5:!DSS
		ssl-default-bind-options no-sslv3    no-tls-tickets
		ssl-default-server-ciphers ECDH+AESGCM:DH+AESGCM:ECDH+AES256:DH+AES256:ECDH+AES128:DH+AES:RSA+AESGCM:RSA+AES:!aNULL:!MD5:!DSS
		ssl-default-server-options no-sslv3   no-tls-tickets

		# Track stats
		server-state-file /etc/haproxy/haproxy.state

	 defaults
		 log     global
		 mode    http
		 
	 
Frontends
^^^^^^^^^^^^^^

A frontend is used as entrypoint. Here, we open a frontend for HTTPS (443) and HTTP (80)::

	 frontend https

		# file must contain certificate, chain and key
		bind *:443 ssl strict-sni crt /etc/ssl/keys/domain.pem
		mode http

		# this sets the 'is_node' ACL in case the domain name matches
		acl is_node              hdr(Host)    -i node.bitshares.org

		# SSL
		http-response set-header Strict-Transport-Security max-age=15768000

		# Let's redirect everything for node to the node backend
		use_backend node if is_node

		# in case some other domain points to us, let's still redirect
		# to node backend
		default_backend node

	 frontend http
		bind *:80
		mode http
		# No like HTTP .. Let's do HTTPS
		redirect scheme https code 301 if !{ ssl_fc }

		
Backend
^^^^^^^^^^

The backend is quite simple and merely points to a set of backend nodes each running a bitshares-core API on port ``8090``.

In our case, we also run a `healthchecker <https://github.com/blockchainbv/graphene-healthchecker>`_  that tests the API and causes HAproxy to remove the backend automatically, if unhealthy. The healthchecker listens on port ``8095``.::	
	
	 backend node
			option httpchk
			balance roundrobin
			server node-01 192.168.0.10:8090 check port 8095
			server node-01 192.168.0.11:8090 check port 8095
			server node-01 192.168.0.12:8090 check port 8095
			server node-01 192.168.0.13:8090 check port 8095
			server node-01 192.168.0.14:8090 check port 8095
			server node-01 192.168.0.15:8090 check port 8095
			
	

|