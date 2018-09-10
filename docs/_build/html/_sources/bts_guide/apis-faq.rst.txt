
.. _api-core-faq:

API - FAQ
===============

.. _api-core-faq-1:
 
How do I get the network_add_nodes command to work? Why is it so complicated?
---------------------------------------------------------------------------------------

You need to follow the instructions in the "Accessing restricted API's" section to allow a username/password access to the network_node API. Then you need to pass the username/password to the cli_wallet on the command line or in a config file.

It's set up this way so that the default configuration is secure even if the RPC port is publicly accessible. It's fine if your witness_node allows the general public to query the database or broadcast transactions (in fact, this is how the hosted web UI works). It's less fine if your witness_node allows the general public to control which p2p nodes it's connecting to. Therefore the API to add p2p connections needs to be set up with proper access control.

.. _api-core-faq-2:

Is there a way to access methods which require login over HTTP?
------------------------------------------------------------------------------------
No. Login is inherently a stateful process (logging in changes what the server will do for certain requests, that's kind of the point of having it). If you need to track state across HTTP RPC calls, you must maintain a session across multiple connections. This is a famous source of security vulnerabilities for HTTP applications. Additionally, HTTP is not really designed for "server push" notifications, and we would have to figure out a way to queue notifications for a polling client.

Websockets solves all these problems. If you need to access Graphene's stateful methods, you need to use Websockets.

.. _api-core-faq-3:

Is there a way to allow external program to drive cli_wallet via websocket, JSONRPC, or HTTP?
-------------------------------------------------------------------------------------------------

Yes. External programs may connect to the CLI wallet and make its calls over a websockets API. To do this, run the wallet in server mode, i.e. cli_wallet -s "127.0.0.1:9999" and then have the external program connect to it over the specified port (in this example, port 9999).

.. _api-core-faq-4:

Is there a way to generate help with parameter names and method descriptions?
-------------------------------------------------------------------------------------------

Yes. Documentation of the code base, including APIs, can be generated using Doxygen. Simply run doxygen in this directory.

If both Doxygen and perl are available in your build environment, the CLI wallet's help and gethelp commands will display help generated from the doxygen documentation.

If your CLI wallet's help command displays descriptions without parameter names like signed_transaction transfer(string, string, string, string, string, bool) it means CMake was unable to find Doxygen or perl during configuration. If found, the output should look like this: signed_transaction transfer(string from, string to, string amount, string asset_symbol, string memo, bool broadcast)


.. _api-core-faq-5:

What is the meaning of a.b.c numbers?
------------------------------------------------------

The first number specifies the space. Space 1 is for protocol objects, 2 is for implementation objects. Protocol space objects can appear on the wire, for example in the binary form of transactions. Implementation space objects cannot appear on the wire and solely exist for implementation purposes, such as optimization or internal bookkeeping.

The second number specifies the type. The type of the object determines what fields it has. For a complete list of type ID's, see enum object_type and enum impl_object_type in types.hpp.

The third number specifies the instance. The instance of the object is different for each individual object.

.. _api-core-faq-6:

The answer to the previous question was really confusing. Can you make it clearer?
-------------------------------------------------------------------------------------

All account ID's are of the form 1.2.x. If you were the 9735th account to be registered, your account's ID will be 1.2.9735. Account 0 is special (it's the "committee account," which is controlled by the committee members and has a few abilities and restrictions other accounts do not).

All asset ID's are of the form 1.3.x. If you were the 29th asset to be registered, your asset's ID will be 1.3.29. Asset 0 is special (it's BTS, which is considered the "core asset").

The first and second number together identify the kind of thing you're talking about (1.2 for accounts, 1.3 for assets). The third number identifies the particular thing.

|



