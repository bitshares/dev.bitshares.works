### Update Documentation for `initial_chain_id` field when creating example genesis #1069
(No need to serialize `initial_chain_id` field when creating example genesis json? #1068)

***
In dev.bitshares.works repo doc, it's not mentioning about `creating_example_genesis` directory. So, the below information are about them. 

### During the startup() - application.cpp

In bitshares-core/libraries/app/application.cpp, [`void application_impl::startup()`](https://github.com/bitshares/bitshares-core/blob/35ec65b130f63c594afe2c9ab7f931b42be08cdc/libraries/app/application.cpp#L318) sets a `genesis.initial_chain.id` (i.e., ` genesis.initial_chain_id = fc::sha256::hash( genesis_str );`) value. In the startup(), there is a flag `modified_genesis`, if it gets `true`, use `genesis_str += "BOGUS";` and sets `genesis.initial_chain_id`.


### namespace detail - application.cpp

In the namespace has the function [`create_example_genesis()`](https://github.com/bitshares/bitshares-core/blob/35ec65b130f63c594afe2c9ab7f931b42be08cdc/libraries/app/application.cpp#L79).  
If the aplication gets in there, it will get `initial_state.initial_chain_id = fc::sha256::hash( "BOGUS" );`


***
*Extended research:*
To understand during those processes how `chain_id` has been handled. 

### when start witness_node

In the process, only uses `get_chain_id()` to show the value to a user. 
- [Code flow (pdf: page8)](/core/knowledge_base/shared_files/witness_node_ex-codeflows.pdf)


### when start cli_wallet

In the process, check a wallet_file existence and also check chain_database `chain_id`. If both get `false`, uses `graphene::egenesis::get_egenesis_chain_id()` to set  `wdata.chain_id`.
- [Code flow (pdf: page4)](/core/knowledge_base/shared_files/cli_wallet_exe-codeflow.pdf)

***


### application code flows

#### application.cpp - namespaces, functions, and code flows

- [namespaces structures and function names list (pdf)](/core/knowledge_base/shared_files/application-cpp-namespaces.pdf)
- namespace graphene { namespace app { namespace detail {}} - [startup() Code flow (pdf)](/core/knowledge_base/shared_files/application-cpp-startup.pdf)
- namespace detail {} - [create_example_genesis() Code flow (pdf)](/core/knowledge_base/shared_files/application-cpp-ns-detail.pdf)

#### witness_node and cli_wallet start
- [witness_node: Code flow (pdf) ](/core/knowledge_base/shared_files/witness_node_ex-codeflows.pdf)
- [cli_wallet: Code flow (pdf) ](/core/knowledge_base/shared_files/cli_wallet_exe-codeflow.pdf)

*The above documents are just information. If someone wants to know how the logic has been structured, it might be interested to check the **code** flows.*


***

### Additional information

**doxygen documentation**

*graphene::chain::genesis_state_type graphene::app::detail::[`crete_example_genusis()`](https://bitshares.org/doxygen/namespacegraphene_1_1app_1_1detail.html#a6ffeeab5458989981d9dd2acb364904e)*

[` compute_chain_id()`](https://bitshares.org/doxygen/structgraphene_1_1chain_1_1genesis__state__type.html#a1212f7780e4dd0f749e59bcdf9149a96)

[` initial_chain_id`](https://bitshares.org/doxygen/structgraphene_1_1chain_1_1genesis__state__type.html#acbf6798be37935a7c792b4fd7adba4c8)

**/libraries/egenesis/embed_genesis.cpp**

https://github.com/bitshares/bitshares-core/blob/master/libraries/egenesis/embed_genesis.cpp#L66

    // hack:  import create_example_genesis() even though it's a way, way
    // specific internal detail
    namespace graphene { namespace app { namespace detail {
    genesis_state_type create_example_genesis();
    } } } // graphene::app::detail

****
