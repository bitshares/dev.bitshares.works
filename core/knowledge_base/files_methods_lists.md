
## BitShares Architecture Maps
- [GitHub Repositories and Entities (pdf)](../knowledge_base/shared_files/BitShares_Architecture-V3.pdf)
- [CLI Wallet Clients and Nodes (pdf)](../knowledge_base/shared_files/BitShares_Architecture-Cli-v2.pdf)


## BitShares-Core Libraries All Folders and Files Lists

- [BitShares-Core Libraries All Folders and Files Lists](../knowledge_base/shared_files/BitShares-Core-Libraries-List20180725.pdf)

| dir | folders |  |  |
|---|---|---|---|
| bitshares-core/libraries/  | app, chain, db, deterministic_openssl_rand, egenesis, net, plugin, utilities, and wallet  |  |


## Code Libraries Files and Methods Lists

|  | file name | release |  |
|---|---|---|---|
| \libraries\app\  | application.cpp | [2.0.180612](../knowledge_base/shared_files/files_methods/Core_Release_2-0-180612-app-cpp.pdf)  |  |
| \libraries\app\ | api.cpp | [2.0.180612](../knowledge_base/shared_files/files_methods/Core_Release_2-0-180612-api-cpp.pdf) |  |
| \libraries\app\ | application_impl.hxx | [2.0.180612](../knowledge_base/shared_files/files_methods/Core_Release_2-0-180612-app-impl-hxx.pdf) |  |
|  |  |  |  |


***

## Coding Flowcharts

|  | | release | |
|---|---|---|---|
|\libraries\app\application.cpp | [namespaces structures ](../knowledge_base/shared_files/application-cpp-namespaces.pdf) | 2.0.180612 |  
| \libraries\app\application.cpp | [startup()](../knowledge_base/shared_files/application-cpp-startup.pdf) | 2.0.180612 |
|\libraries\app\application.cpp | namespace detail {} - [create_example_genesis()](../knowledge_base/shared_files/application-cpp-ns-detail.pdf) | 2.0.180612 |
| \libraries\app\application.cpp | [get_api_access_info()](../knowledge_base/shared_files/files_methods/application-cpp-20180721.pdf)  (***All below in this file***) | 2.0.180612 |
| \libraries\app\application.cpp | has_item()  | 2.0.180612 |
| \libraries\app\application.cpp | handle_block()  | 2.0.180612 |
| \libraries\app\application.cpp | handle_transaction() | 2.0.180612 |
| \libraries\app\application.cpp |  is_included_block() | 2.0.180612 |
| \libraries\app\application.cpp | get_block_ids()  | 2.0.180612 |
| \libraries\app\application.cpp | get_blockchain_synopsis() | 2.0.180612 |
|  |  |  |  |
| \libraries\app\api.cpp |[login()](../knowledge_base/shared_files/files_methods/api-cpp-1-20180721.pdf) (***ALl below in this file***)  | 2.0.180612 |
| \libraries\app\api.cpp | enable_api()  | 2.0.180612 |
| \libraries\app\api.cpp | network_broadcast_api()  | 2.0.180612 |
| \libraries\app\api.cpp |on_applied_block()    | 2.0.180612 |
| \libraries\app\api.cpp | broadcast_transaction()  | 2.0.180612 |
| \libraries\app\api.cpp | broadcast_transaction_synchronous() | 2.0.180612 |
| \libraries\app\api.cpp | broadcast_block()  | 2.0.180612 |
| \libraries\app\api.cpp | broadcast_transaction_with_callback()  | 2.0.180612 |
| \libraries\app\api.cpp | get_info()  | 2.0.180612 |
| \libraries\app\api.cpp | add_node()  | 2.0.180612 |
| \libraries\app\api.cpp | get_fill_order_history()   | 2.0.180612 |
| \libraries\app\api.cpp | get_account_history()  | 2.0.180612 |
| \libraries\app\api.cpp | get_account_history_operations( ) | 2.0.180612 |
| \libraries\app\api.cpp | get_relative_account_history()  | 2.0.180612 |
| \libraries\app\api.cpp | get_account_history_by_operations() | 2.0.180612 |
| \libraries\app\api.cpp | get_market_history()  | 2.0.180612 |
|  |  |  |  |
| /programs/witness_node/main.cpp | [witness_node:startup](../knowledge_base/shared_files/witness_node_ex-codeflows.pdf) | 2.0.180612 |  |
| /programs/cli_wallet/main.cpp | [cli_wallet: startup](../knowledge_base/shared_files/cli_wallet_exe-codeflow.pdf) |2.0.180612  |  |
|  |  |  |  |

