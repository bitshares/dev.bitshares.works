### Documentation for 

## Update Documentation for `initial_chain_id` field when creating example genesis #1069
(No need to serialize `initial_chain_id` field when creating example genesis json? #1068)

***
In dev.bitshares.works repo doc, it's not mentioning about `creating_example_genesis` directory. So, the below information are about them. 

### During the startup() - application.cpp

In bitshares-core/libraries/app/application.cpp, [`void application_impl::startup()`](https://github.com/bitshares/bitshares-core/blob/35ec65b130f63c594afe2c9ab7f931b42be08cdc/libraries/app/application.cpp#L318) sets a `genesis.initial_chain.id` (i.e., ` genesis.initial_chain_id = fc::sha256::hash( genesis_str );`) value. In the startup(), there is a flag `modified_genesis`, if it gets `true`, use `genesis_str += "BOGUS";` and sets `genesis.initial_chain_id`.


### namespace detail - application.cpp

In the namespace has the function [`create_example_genesis()`](https://github.com/bitshares/bitshares-core/blob/35ec65b130f63c594afe2c9ab7f931b42be08cdc/libraries/app/application.cpp#L79).  
If the aplication gets in there, it will get `initial_state.initial_chain_id = fc::sha256::hash( "BOGUS" );`


***
*Extended research*

### when start witness_node

In the process, only uses `get_chain_id()` to show the value to a user. 
- [Code flow (pdf: page8)](/core/knowledge_base/shared_files/witness_node_ex-codeflows.pdf)


### when start cli_wallet

In the process, check a wallet_file existence and also check chain_database `chain_id`. If both get `false`, uses `graphene::egenesis::get_egenesis_chain_id()` to set  `wdata.chain_id`.
- [Code flow (pdf: page3)](/core/knowledge_base/shared_files/cli_wallet_exe-codeflow.pdf)

***


## application code flows

#### application.cpp - namespaces, functions, and code flows

- [namespaces structures and function names list (pdf)](/core/knowledge_base/shared_files/application-cpp-namespaces.pdf)
- namespace graphene { namespace app { namespace detail {}} - [startup() Code flow (pdf)](/core/knowledge_base/shared_files/application-cpp-startup.pdf)
- namespace detail {} - [create_example_genesis() Code flow (pdf)](/core/knowledge_base/shared_files/application-cpp-ns-detail.pdf)

#### witness_node and cli_wallet start
- [witness_node: Code flow (pdf) ](/core/knowledge_base/shared_files/witness_node_ex-codeflows.pdf)
- [cli_wallet: Code flow (pdf) ](/core/knowledge_base/shared_files/cli_wallet_exe-codeflow.pdf)

*The above documents are just information. If someone wants to know how the logic has been structured, it might be interested to check the **code** flows.*


***
