## Wallet Full Nodes & Witness Nodes

*This page shows example command lines a client to connect to the network and open up a node.*


***

First, we need a client to connect to a witness (we run in monitor mode) or a public trusted witness. For exchanges, it is recommended to run a full node, though. In this example, we connect to the network via a seed node:

      programs/witness_node/witness_node -s 104.200.28.117:61705 --rpc-endpoint 127.0.0.1:8090  # FIXME?

This opens up a node that we can connect to via the included wallet

      programs/cli_wallet/cli_wallet -s ws://127.0.0.1:8090 -H 127.0.0.1:8091

which will 
- open port `8091` for HTTP-RPC requests and 
- has the capabilities to handle accounts while the `witness_node` can only answer queries to the blockchain.


***

(ref)
- https://github.com/bitshares/bitshares-core/wiki/Wallet_Full-Nodes_Witness_Nodes

