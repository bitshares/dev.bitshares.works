## Frequently Asked Questions - List All

### Contents:

[**General**](../tutorials/FAQ.md#general)
- [What is the standard Bitshares address structure and format?](../tutorials/FAQ.md#q-what-is-the-standard-bitshares-address-structure-and-format
)
- [What is the format of the block header?](../tutorials/FAQ.md#q-what-is-the-format-of-the-block-header)
- [What is the maximum bitshares block size?](../tutorials/FAQ.md#q-what-is-the-maximum-bitshares-block-size)
- [Are there any sharding mechanics currently deployed?](../tutorials/FAQ.md#q-are-there-any-sharding-mechanics-currently-deployed)
- [How are SPV clients handled?](../tutorials/FAQ.md#q-how-are-spv-clients-handled)
- [How is time addressed in the blockchain? Is NTP used or some other protocol?](../tutorials/FAQ.md#q-how-is-time-addressed-in-the-blockchain-is-ntp-used-or-some-other-protocol)
- [How do new clients bootstrap into the network?](../tutorials/FAQ.md#q-how-do-new-clients-bootstrap-into-the-network)
- [What is the average block time?](../tutorials/FAQ.md#q-what-is-the-average-block-time)
- [How is accounting addressed in Bitshares? Is it a Nxt style accounting model or like Bitcoin’s UTXO](../tutorials/FAQ.md#q-how-is-accounting-addressed-in-bitshares-is-it-a-nxt-style-accounting-model-or-like-bitcoins-utxo)

[**Protocol**](../tutorials/FAQ.md#protocol)
- [Are there any special affordances made for privacy?](../tutorials/FAQ.md#q-are-there-any-special-affordances-made-for-privacy)
- [Does the protocol provide mechanisms for overlay protocols to interact such as OR_RETURN?](../tutorials/FAQ.md#q-does-the-protocol-provide-mechanisms-for-overlay-protocols-to-interact-such-as-or_return)
- [Is this done via a gossip protocol or through a federate relay?](../tutorials/FAQ.md#q-is-this-done-via-a-gossip-protocol-or-through-a-federate-relay)

[**Data Structures**](../tutorials/FAQ.md#data-structures)
- [What data structures are used in the blockchain?](../tutorials/FAQ.md#q-what-data-structures-are-used-in-the-blockchain)

[**Public Key System**](../tutorials/FAQ.md#public-key-system)
- [What public key system is used? If elliptic curve, then what is the curv?](../tutorials/FAQ.md#q-what-public-key-system-is-used-if-elliptic-curve-then-what-is-the-curve)

[**Scripting Language**](../tutorials/FAQ.md#scripting-language)
- [Is there a specification for Bitshares scripting language? (assuming there is one)](../tutorials/FAQ.md#q-is-there-a-specification-for-bitshares-scripting-language-assuming-there-is-one)
- [Is the scripting language turing complete?](../tutorials/FAQ.md#q-is-the-scripting-language-turing-complete)

***
### [Accounts](../tutorials/FAQ.md#accounts-1)
- [How to change a password if using a Cloud Wallet](https://github.com/bitshares/bitshares-ui/wiki/Cloud-Wallet-Login-and-changing-password) : from BitShares UI wiki
- [How can I create, register and upgrade an account to Lifetime Membership?](../tutorials/FAQ.md#q-how-can-i-create-register-and-upgrade-an-account-to-lifetime-membership)
- [How to process Account Registration](../accounts/account_registration.md#account-registration)


***
### [APIs](#apis-1)
- [How an Exchange can integrate their services to the BitShares UI Wallet - Gateway Integration Requirements](https://github.com/bitshares/bitshares-ui/wiki/Gateway-Integration-Requirements) : from BitShares UI wiki
- [How do I get the network_add_nodes command to work? Why is it so complicated?](../tutorials/FAQ.md#q-how-do-i-get-the-network_add_nodes-command-to-work-why-is-it-so-complicated)
- [Is there a way to access methods which require login over HTTP?](../tutorials/FAQ.md#q-is-there-a-way-to-access-methods-which-require-login-over-http)
- [Is there a way to allow external program to drive cli_wallet via websocket, JSONRPC, or HTTP?](../tutorials/FAQ.md#q-is-there-a-way-to-allow-external-program-to-drive-cli_wallet-via-websocket-jsonrpc-or-http)
- [Is there a way to generate help with parameter names and method descriptions?](../tutorials/FAQ.md#q-is-there-a-way-to-generate-help-with-parameter-names-and-method-descriptions)
- [What is the meaning of a.b.c numbers?](../tutorials/FAQ.md#q-what-is-the-meaning-of-abc-numbers)
- [The answer to the previous question was really confusing. Can you make it clearer?](../tutorials/FAQ.md#q-the-answer-to-the-previous-question-was-really-confusing-can-you-make-it-clearer)
- [How to process Account Registration](../accounts/account_registration.md#account-registration)


***

### [Assets](../tutorials/FAQ.md#assets-1)
- [What happens to the asset creation fee?](../tutorials/FAQ.md#q-what-happens-to-the-asset-creation-fee)
- [Can I change x after creation of the asset?](../tutorials/FAQ.md#qcan-i-change-x-after-creation-of-the-asset)
- [What about Parent and Child assets?](../tutorials/FAQ.md#q-what-about-parent-and-child-assets)
- [Can I change the issuer?](../tutorials/FAQ.md#q-can-i-changing-the-issuer)
- [How to Create a new UIA](../tutorials/assets_uia.md#how-to-create-a-new-uia)
- [How to Update/Change an existing UIA](../tutorials/assets_uia.md#how-to-updatechange-an-existing-uia)
- [How to Create a MPA](../tutorials/assets_mpa.md#how-to-create-a-mpa)
- [How to Create a Prediction Market](../tutorials/assets_pm.md#how-to-create-a-prediction-market)
- [How to Closing/Setting a Prediction Market](../tutorials/assets_pm.md#how-to-closingsetting-a-prediction-market
)
- [How to test the publishing of price feeds](../tutorials/assets_publish_feed.md#how-to-test-the-publishing-of-price-feeds)
   - Python Script Example


**Fee Pool**
- [What is the fee pool all about?](../tutorials/FAQ.md#q-what-is-the-fee-pool-all-about)
- [What to do if the fee pool is empty?](../tutorials/FAQ.md#q-what-to-do-if-the-fee-pool-is-empty)
- [What is Fee Pool Draining?](../tutorials/FAQ.md#q-what-is-fee-pool-draining)

**Market Fee**
- [What happens if I enable Market fees?](../tutorials/FAQ.md#q-what-happens-if-i-enable-market-fees)
- [How to claim accumulated fees?](../tutorials/FAQ.md#q-how-to-claim-accumulated-fees)
- [What if two different market fees are involved in a trade?](../tutorials/FAQ.md#q-what-if-two-different-market-fees-are-involved-in-a-trade)
- [What are Asset Flags and Permissions?](../tutorials/FAQ.md#q-what-are-asset-flags-and-permissions)
- [What are the Permissions?](../tutorials/FAQ.md#q-what-are-the-permissions)
- [What are the Flags?](../tutorials/FAQ.md#q-what-are-the-flags)

**Market Pegged Assets**
- [Can I use the same flags/permissions as for UIAs?](../tutorials/FAQ.md#q-can-i-use-the-same-flagspermissions-as-for-uias)
- [What are market-pegged-asset-specific parameters?](../tutorials/FAQ.md#q-what-are-market-pegged-asset-specific-parameters)

***
### BitShares Decentralised Exchange (DEX)
- [How to prepare BitShares Exchange Guide (Single Node Edition)](../knowledge_base/translated/exchange_single_node.md#how-to-prepare-bitshares-exchange-guide-single-node-edition)
- [How to prepare Distributed Access to the BitShares Decentralised Exchange (DEX)](../tutorials/dex_distributed_access.md#distributed-access-to-the-bitshares-decentralised-exchange)

***

### Committee
- [How to Create a new Committee Member](../tutorials/committee_howto.md#how-to-creating-a-new-committee-member)
- [How to Propose Committee Actions](../tutorials/committee_howto.md#how-to-propose-committee-actions)
- [How to Approve/Disapprove a Committee Proposal](../tutorials/committee_howto.md#how-to-approvedisapprove-a-committee-proposal)
- [How Committee Proposes a Change in Fee](../tutorials/committee_howto.md#how-committee-proposes-a-change-in-fee)

***

### [Testnet](../tutorials/FAQ.md#testnet-1)
- [Public & private testnet differences What is the difference between public and private testnet?](../tutorials/FAQ.md#q-public--private-testnet-differences-what-is-the-difference-between-public-and-private-testnet)

***

### Transfer / Transactions
- [How to use the CLI wallet to perform confidential transfers in BitShares](../tutorials/trn_confidential_transfer.md#how-to-use-the-cli-wallet-to-perform-confidential-transfers-in-bitshares)
- [Hot to Construct Any Transaction - Manually](../tutorials/trn_construct_transaction.md#how-to-construct-any-transaction---manually)
- [How to Propose a Transaction](../tutorials/trn_propose_transaction.md#how-to-propose-a-transaction)

***

### Vesting Balance 
- [How to find Vesting Balance](../tutorials/vesting-balance-dev.md#how-to-find-vesting-balance)

***

### [Wallet / CLI Wallet](../tutorials/FAQ.md#wallet--cli-wallet-1)
- [Running a Local Test Network (- wiki)](../wallet/cli_wallet-wiki.md#graphene-cli-wallet-wiki)
- [Wallet Full Nodes & Witness Nodes(- wiki)](../wallet/wallet_full_nodes_witness_nodes.md#wallet-full-nodes--witness-nodes)
- [Connect to a Public API Node service](../wallet/node_wallet_witness.md#use-the-public-api-node)
- [How can I close the CLI client in a clean way?](../tutorials/FAQ.md#q-how-can-i-close-the-cli-client-in-a-clean-way)
- [How to import a GUI-wallet account into CLI-wallet](../wallet/import_account.md#how-to-import-a-gui-wallet-account-into-cli-wallet)
- [Why does the CLI client crash immediately when I try to run it for the first time?](../tutorials/FAQ.md#q-why-does-the-cli-client-crash-immediately-when-i-try-to-run-it-for-the-first-time)
- [How to setup Network and Wallet Configuration](../wallet/wallet_network.md#network-and-wallet-configuration)
   - [General](../wallet/wallet_network.md#general-network-and-wallet-configuration)
   - [Secure](../wallet/wallet_network.md#secure-network-and-wallet-configuration)
- [Wallet Full Nodes & Witness Nodes (-wiki)](../wallet/wallet_full_nodes_witness_nodes.md#wallet-full-nodes--witness-nodes)
   - Examples - connect to the network and open up a node   
   
***
### [Witness](../tutorials/FAQ.md#witness)
- [How can I close the witness node in a clean way?](../tutorials/FAQ.md#q-how-can-i-close-the-witness-node-in-a-clean-way)
- [How do I check whether the witness node is already synced?](../tutorials/FAQ.md#q-how-do-i-check-whether-the-witness-node-is-already-synced)
- [How to Change The Signing Key of a Witness](../tutorials/wt_change_signing_key.md#how-to-change-the-signing-key-of-a-witness)
- [If it seems to be unable to sync beyond a certain date?](../tutorials/FAQ.md#q-if-it-seems-to-be-unable-to-sync-beyond-a-certain-date)
- [Is it safe to delete logs stored in witness_node_data_dirlogsp2p?](../tutorials/FAQ.md#q-is-it-safe-to-delete-logs-stored-in-witness_node_data_dirlogsp2p)
- [What is the best way to interact with the witness node?](../tutorials/FAQ.md#q-what-is-the-best-way-to-interact-with-the-witness-node)
- [What is the meaning of all those different text colors in the witness node console?](../tutorials/FAQ.md#q-what-is-the-meaning-of-all-those-different-text-colors-in-the-witness-node-console)
- [Whose private key is [“BTS6MRyAjQ..”,”5KQwrPbwdL..”]? Why is it predefined ion the config.ini?](../tutorials/FAQ.md#q-whose-private-key-is-bts6mryajq5kqwrpbwdl-why-is-it-predefined-ion-the-configini)


***
### Worker
- [How to Create a Worker](../tutorials/worker_create.md#how-to-create-a-worker)
- [How to see Proposals on the Chain](../tutorials/worker_create.md#how-to-see-proposals-on-the-chain)
- [How to Vote for a Worker](../tutorials/worker_create.md#how-to-vote-for-a-worker)
- [How Workers Get Paid](../tutorials/worker_create.md#how-workers-get-paid)
- [How to find how much per hour allocated for all worker](../tutorials/worker_pay_claim.md#how-to-find-how-much-per-hour-allocated-for-all-worker)

***
***

## Questions and Answers

## General

#### Q. What is the standard Bitshares address structure and format?

address = ‘BTS’+base58(ripemd(sha512(compressed_pub))) (checksum obviated) But addresses are not used directly, instead you have an account (that can be controlled by one or more address, pubkey or another account). https://bitshares.org/technology/dynamic-account-permissions/


#### Q. What is the format of the block header?

    struct block_header
       {
          digest_type                   digest()const;
          block_id_type                 previous;
          uint32_t                      block_num()const { return num_from_id(previous) + 1; }
          fc::time_point_sec            timestamp;
          witness_id_type               witness;
          checksum_type                 transaction_merkle_root;
          extensions_type               extensions;

          static uint32_t num_from_id(const block_id_type& id);
       };

#### Q. What is the maximum bitshares block size?

Configurable by chain parameters.

    struct chain_parameters
    {
       /** using a smart ref breaks the circular dependency created between operations and the fee schedule */
       smart_ref<fee_schedule> current_fees;                       ///< current schedule of fees
       uint8_t                 block_interval                      = GRAPHENE_DEFAULT_BLOCK_INTERVAL; ///< interval in seconds between blocks
       uint32_t                maintenance_interval                = GRAPHENE_DEFAULT_MAINTENANCE_INTERVAL; ///< interval in sections between blockchain maintenance events
       uint8_t                 maintenance_skip_slots              = GRAPHENE_DEFAULT_MAINTENANCE_SKIP_SLOTS; ///< number of block_intervals to skip at maintenance time
       uint32_t                committee_proposal_review_period    = GRAPHENE_DEFAULT_COMMITTEE_PROPOSAL_REVIEW_PERIOD_SEC; ///< minimum time in seconds that a proposed transaction requiring committee authority may not be signed, prior to expiration
       uint32_t                maximum_transaction_size            = GRAPHENE_DEFAULT_MAX_TRANSACTION_SIZE; ///< maximum allowable size in bytes for a transaction
       uint32_t                maximum_block_size                  = GRAPHENE_DEFAULT_MAX_BLOCK_SIZE; ///< maximum allowable size in bytes for a block
       uint32_t                maximum_time_until_expiration       = GRAPHENE_DEFAULT_MAX_TIME_UNTIL_EXPIRATION; ///< maximum lifetime in seconds for transactions to be valid, before expiring
       uint32_t                maximum_proposal_lifetime           = GRAPHENE_DEFAULT_MAX_PROPOSAL_LIFETIME_SEC; ///< maximum lifetime in seconds for proposed transactions to be kept, before expiring
       uint8_t                 maximum_asset_whitelist_authorities = GRAPHENE_DEFAULT_MAX_ASSET_WHITELIST_AUTHORITIES; ///< maximum number of accounts which an asset may list as authorities for its whitelist OR blacklist
       uint8_t                 maximum_asset_feed_publishers       = GRAPHENE_DEFAULT_MAX_ASSET_FEED_PUBLISHERS; ///< the maximum number of feed publishers for a given asset
       uint16_t                maximum_witness_count               = GRAPHENE_DEFAULT_MAX_WITNESSES; ///< maximum number of active witnesses
       uint16_t                maximum_committee_count             = GRAPHENE_DEFAULT_MAX_COMMITTEE; ///< maximum number of active committee_members
       uint16_t                maximum_authority_membership        = GRAPHENE_DEFAULT_MAX_AUTHORITY_MEMBERSHIP; ///< largest number of keys/accounts an authority can have
       uint16_t                reserve_percent_of_fee              = GRAPHENE_DEFAULT_BURN_PERCENT_OF_FEE; ///< the percentage of the network's allocation of a fee that is taken out of circulation
       uint16_t                network_percent_of_fee              = GRAPHENE_DEFAULT_NETWORK_PERCENT_OF_FEE; ///< percent of transaction fees paid to network
       uint16_t                lifetime_referrer_percent_of_fee    = GRAPHENE_DEFAULT_LIFETIME_REFERRER_PERCENT_OF_FEE; ///< percent of transaction fees paid to network
       uint32_t                cashback_vesting_period_seconds     = GRAPHENE_DEFAULT_CASHBACK_VESTING_PERIOD_SEC; ///< time after cashback rewards are accrued before they become liquid
       share_type              cashback_vesting_threshold          = GRAPHENE_DEFAULT_CASHBACK_VESTING_THRESHOLD; ///< the maximum cashback that can be received without vesting
       bool                    count_non_member_votes              = true; ///< set to false to restrict voting privlegages to member accounts
       bool                    allow_non_member_whitelists         = false; ///< true if non-member accounts may set whitelists and blacklists; false otherwise
       share_type              witness_pay_per_block               = GRAPHENE_DEFAULT_WITNESS_PAY_PER_BLOCK; ///< CORE to be allocated to witnesses (per block)
       uint32_t                witness_pay_vesting_seconds         = GRAPHENE_DEFAULT_WITNESS_PAY_VESTING_SECONDS; ///< vesting_seconds parameter for witness VBO's
       share_type              worker_budget_per_day               = GRAPHENE_DEFAULT_WORKER_BUDGET_PER_DAY; ///< CORE to be allocated to workers (per day)
       uint16_t                max_predicate_opcode                = GRAPHENE_DEFAULT_MAX_ASSERT_OPCODE; ///< predicate_opcode must be less than this number
       share_type              fee_liquidation_threshold           = GRAPHENE_DEFAULT_FEE_LIQUIDATION_THRESHOLD; ///< value in CORE at which accumulated fees in blockchain-issued market assets should be liquidated
       uint16_t                accounts_per_fee_scale              = GRAPHENE_DEFAULT_ACCOUNTS_PER_FEE_SCALE; ///< number of accounts between fee scalings
       uint8_t                 account_fee_scale_bitshifts         = GRAPHENE_DEFAULT_ACCOUNT_FEE_SCALE_BITSHIFTS; ///< number of times to left bitshift account registration fee at each scaling
       uint8_t                 max_authority_depth                 = GRAPHENE_MAX_SIG_CHECK_DEPTH;
       extensions_type         extensions;
    
       /** defined in fee_schedule.cpp */
       void validate()const;
    };


#### Q. Are there any sharding mechanics currently deployed?

No

#### Q. How are SPV clients handled?

No SPV clients at the moment, each full node can expose a public websocket/http api.

#### Q. How is time addressed in the blockchain? Is NTP used or some other protocol?

NTP

#### Q. How do new clients bootstrap into the network?

Trusted seed nodes. Knowledge of initial witness keys.

#### Q. What is the average block time?

Current 3 seconds, configurable by chain parameters.

#### Q. How is accounting addressed in Bitshares? Is it a Nxt style accounting model or like Bitcoin’s UTXO

Each account has a finite set of balances, one for each asset type.

### Protocol

#### Q. Are there any special affordances made for privacy?

…such as using CoinJoin or a ZK-SNARK based privacy scheme like Zerocash? If mixing is integrated at the protocl level are you using the standards set forth by the BNMCKF Mixcoin proposal

Confidential values (same as blockstream elements using the same secp256k1-zkp lib) + stealth addresses. https://github.com/Ele

#### Q. Does the protocol provide mechanisms for overlay protocols to interact such as OR_RETURN?

Yes, using a custom_operation.

    struct custom_operation : public base_operation
       {
          struct fee_parameters_type {
             uint64_t fee = GRAPHENE_BLOCKCHAIN_PRECISION;
             uint32_t price_per_kbyte = 10;
          };

          asset                     fee;
          account_id_type           payer;
          flat_set<account_id_type> required_auths;
          uint16_t                  id = 0;
          vector<char>              data;

          account_id_type   fee_payer()const { return payer; }
          void              validate()const;
          share_type        calculate_fee(const fee_parameters_type& k)const;
       };

#### Q. Is this done via a gossip protocol or through a federate relay?

Each node immediately broadcast the data it receives to its peers after validating it https://github.com/cryptonomex/graphene/blob/master/libraries/p2p/design.md


### Data Structures

#### Q. What data structures are used in the blockchain?

::
    Blocks => transactions => operations => objects.

The blockchain state is contained in an object database that is affected by the operations. Example objects::

    account_object
    asset_object
    account_balance_object
    ...


    class account_balance_object : public abstract_object<account_balance_object>
       {
          public:
             static const uint8_t space_id = implementation_ids;
             static const uint8_t type_id  = impl_account_balance_object_type;

             account_id_type   owner;
             asset_id_type     asset_type;
             share_type        balance;

             asset get_balance()const { return asset(balance, asset_type); }
             void  adjust_balance(const asset& delta);
       };

### Public key system

#### Q. What public key system is used? If elliptic curve, then what is the curve?

Same as Bitcoin, secp256k1.

### Scripting Language 

#### Q. Is there a specification for Bitshares scripting language? (assuming there is one)

No scripting

#### Q. Is the scripting language turing complete?

No scripting

***

### Accounts

#### Q. How can I create, register and upgrade an account to Lifetime Membership?

Without already having an account, or knowing someone that has an account, it is not possible. You can’t create accounts out of nowhere.

But you can work around it by importing an active key of a **Lifetime Member** account that has funds:

1. In the gui, go to the permissions tab of an account that is funded and has a LTM status.
1. Click on the BTS public key on the ACTIVE tab and copy the private key.
1. In the cli-wallet run: import_key <account_name> <private_key>
1. Then run: suggest_brain_key and copy the brain key. (You might want to make a backup of your brain key somewhere.)
1. Create a new account with this command: create_account_with_brain_key <brainkey> <new_account_name> <imported_name> <imported_name> true

This will create a new account called <new_account_name> and set the registrar and referrer to <imported_name>. The brainkey can be used to regenerate the account (even in the GUI wallet). You can manually delete the other active key from the wallet.json file.

***

### APIs
#### Q. How do I get the network_add_nodes command to work? Why is it so complicated?

You need to follow the instructions in the "Accessing restricted API's" section to allow a username/password access to the network_node API. Then you need to pass the username/password to the cli_wallet on the command line or in a config file.

It's set up this way so that the default configuration is secure even if the RPC port is publicly accessible. It's fine if your witness_node allows the general public to query the database or broadcast transactions (in fact, this is how the hosted web UI works). It's less fine if your witness_node allows the general public to control which p2p nodes it's connecting to. Therefore the API to add p2p connections needs to be set up with proper access contro

#### Q. Is there a way to access methods which require login over HTTP?
No. Login is inherently a stateful process (logging in changes what the server will do for certain requests, that's kind of the point of having it). If you need to track state across HTTP RPC calls, you must maintain a session across multiple connections. This is a famous source of security vulnerabilities for HTTP applications. Additionally, HTTP is not really designed for "server push" notifications, and we would have to figure out a way to queue notifications for a polling client.

Websockets solves all these problems. If you need to access Graphene's stateful methods, you need to use Websockets.

#### Q. Is there a way to allow external program to drive cli_wallet via websocket, JSONRPC, or HTTP?
Yes. External programs may connect to the CLI wallet and make its calls over a websockets API. To do this, run the wallet in server mode, i.e. cli_wallet -s "127.0.0.1:9999" and then have the external program connect to it over the specified port (in this example, port 9999).

#### Q. Is there a way to generate help with parameter names and method descriptions?
Yes. Documentation of the code base, including APIs, can be generated using Doxygen. Simply run `doxygen` in this directory.

If both Doxygen and perl are available in your build environment, the CLI wallet's `help` and `gethelp` commands will display help generated from the doxygen documentation.

If your CLI wallet's help command displays descriptions without parameter names like `signed_transaction transfer(string, string, string, string, string, bool)` it means CMake was unable to find Doxygen or perl during configuration. If found, the output should look like this: `signed_transaction transfer(string from, string to, string amount, string asset_symbol, string memo, bool broadcast)` 

#### Q. What is the meaning of a.b.c numbers?
The first number specifies the space. Space 1 is for protocol objects, 2 is for implementation objects. Protocol space objects can appear on the wire, for example in the binary form of transactions. Implementation space objects cannot appear on the wire and solely exist for implementation purposes, such as optimization or internal bookkeeping.

The second number specifies the type. The type of the object determines what fields it has. For a complete list of type ID's, see enum object_type and enum impl_object_type in types.hpp.

The third number specifies the instance. The instance of the object is different for each individual object.

#### Q. The answer to the previous question was really confusing. Can you make it clearer?

All account ID's are of the form 1.2.x. If you were the 9735th account to be registered, your account's ID will be 1.2.9735. Account 0 is special (it's the "committee account," which is controlled by the committee members and has a few abilities and restrictions other accounts do not).

All asset ID's are of the form 1.3.x. If you were the 29th asset to be registered, your asset's ID will be 1.3.29. Asset 0 is special (it's BTS, which is considered the "core asset").

The first and second number together identify the kind of thing you're talking about (1.2 for accounts, 1.3 for assets). The third number identifies the particular thing.

***
### Assets

#### Q. What happens to the asset creation fee?

50% of the asset creation fee are used to pre-fill the assets fee pool. From the other 50%, 20% go to the network and 80% go to the referral program. This means, that if you are a life-time member, you get back 40% of the asset creation fee after the vesting period (currently 90 days).

#### Q.Can I change x after creation of the asset?

The following parameters can be changed after creation:

- Issuer
- UIA-Options:
   - Max Supply
   - Market Fee
   - Permissions (disable only/nor re-enable)
   - Flags (if permissions allow it)
   - Core exchange rate
   - White/Black Listing
   - Description
- MPG-Options:
   - Feed Life Time
   - Minimum Feeds
   - Force Settlement Offset/Delay/Volume

Things that cannot be changes:
- Symbol
- Precision

#### Q. What about Parent and Child assets?

A **parent/child** relation ship for assets can be represented by the name of the symbol, e.g.:

    PARENT.child

can only be created by the issuer of `PARENT` and no one else.

#### Q. Can I changing the issuer?

The current issue of an asset may transfer ownership of the asset to someone else by changing the issuer in the asset’s settings.

#### Q. What is the fee pool all about?

The fee pool allows participants in the network to deal with assets and pay for the transaction fees without the need to hold BTS. Any transaction fee can be paid by paying any asset that has a core exchange rate (i.e. a price) at which the asset can be exchange implicitly into BTS to cover the network fee. If the asset’s fee pool is funded, the fees can be payed in the native UIA instead of BTS.

**Note :** The core exchange rate at which a fee can be exchanged into BTS may differ from the actual market valuation of the asset. A user, thus, may pay a premium or spare funds by paying in BTS.

**Warning :** Make sure your core exchange rate is higher than the lowest ask, otherwise, people will buy your token from the market and drain your fee pool via implicit abitrage.

It is the task of the issuer to keep the fee pool funded and the core exchange rate updated unless he wants the owner of his asset to be required to hold BTS for the fee.

#### Q. What to do if the fee pool is empty?

Open up the issuer’s account, click the assets tab and open up the dialog to change the asset. There will be a fee pool tab that allows you to fund the fee pool and claim the accumulated fees!

#### Q. What is Fee Pool Draining?

If an order is created and paid in a non-BTS asset, the fee is implicitly exchange into BTS to pay the network fee. However, if the order is canceled, 90% of the fee will be returned as BTS. The result is, that if the core exchange rate is lower than the highest bid, people can simply buy your token from the market, and exchange them implicitly with the fee pool by creating and canceling an order. This will deplete the fee pool and leave the issuer with his tokens at a slight loss (depending on the offset of the core exchange rate). For this reason, we recommend to use a core exchange that is slightly higher than the market price of your asset. As a consequence, paying fees in BTS should always be cheaper.

#### Q. What happens if I enable Market fees?

If Market Fees of a UIA are turned on, fees have to be payed for each **market transaction**. This means, that market fees only apply to **filled orders**!

The percentage of market fees that are applied can be defined and changed by the issuer and any fee generated that way will be accumulated for each asset only to be claimed by the issuer.

If the Market Fee is set to 1%, the issuer will earn 1% of market volume as profit. These profits are accumulated for each UIA and can be withdrawn by the issuer.

#### Q. How to claim accumulated fees?

Open up the issuer’s account, click the assets tab and open up the dialog to change the asset. There will be a fee pool tab that allows you to fund the fee pool and claim the accumulated fees!

#### Q. What if two different market fees are involved in a trade?

Suppose, I set the market fee for MyUIA market at 0.1%. and the market fee for YourUIA market at 0.3%.

In BitShares, You pay the fee upon receiving an asset. Hence, one side will pay 0.3% the other will pay 0.1%.

#### Q. What are Asset Flags and Permissions?

When an asset is creatd, the issuer can set any combination of flags/permissions. Flags are set in stone unless there is permission to edit. Once a permission to edit is revoked, flags are permanent, and can never be modified again.

#### Q. What are the Permissions?

- Enable market fee
- Require holders to be white-listed
- Issuer may transfer asset back to himself
- Issuer must approve all transfers
- Disable confidential transactions

#### Q. What are the Flags?

| flag | |
|---|---|
| charge_market_fee | an issuer-specified percentage of all market trades in this asset is paid to the issuer|
| white_list | accounts must be white-listed in order to hold this asset |
| override_authority | issuer may transfer asset back to himself |
| transfer_restricted | require the issuer to be one party to every transfer |
| disable_force_settle | disable force settling |
| global_settle | (only for bitassets) allows bitasset issuer to force a global settling - this may be set in permissions, but should not be set as flag unless, for instance, a prediction market has to be resolved. If this flag has been enabled, no further shares can be borrowed! |
| disable_confidential | allow the asset to be used with confidential transactions |
| witness_fed_asset | allow the asset to be fed by witnesses |
| committee_fed_asset | allow the asset to be fed by the committee |

#### Q. Can I use the same flags/permissions as for UIAs?

Yes!

#### Q. What are market-pegged-asset-specific parameters?

| parameter| |
|---|---|
| feed_lifetime_sec | The lifetime of a feed. After this time (in seconds) a feed is no longer considered valid. |
| minimum_feeds | The number of feeds required for a market to become (and stay) active. |
| force_settlement_delay_sec | The delay between requesting a settlement and actual execution of settlement (in seconds) |
| force_settlement_offset_percent | A percentage offset from the price feed for settlement (100% = 10000) |
| maximum_force_settlement_volume | Maximum percentage of the supply that can be settled per day (100% = 10000) |
| short_backing_asset | The asset that has to be used to back this asset (when borrowing) |


***
### Testnet

#### Q. Public & private testnet differences What is the difference between public and private testnet?**

Not much. The biggest difference is that public testnet are intended for wider audience and has fixed (not easy to change parameters), while private testnets can be setup with arbitrary settings

***
### Wallet / CLI Wallet

#### Q. How can I close the CLI client in a clean way?

In Windows closing the whole window produces a nasty exception. In Windows you can try ctrl-d which stops the process but stil produces a nasty exception.

#### Q. Why does the CLI client crash immediately when I try to run it for the first time?

The CLI client is unable to run on its own, i.e. without being connected to the witness node (via a web socket connection). So to successfully run the CLI client you need to do this:

- make sure you have this entry uncommented in the witness_node_data_dir/config.ini file rpc-endpoint = 127.0.0.1:8090
- before you start the CLI client, you need to start the witness node (and wait a while till it’s up and running)

***
### Witness

#### Q. How can I close the witness node in a clean way?
In windows use ctrl-c.

#### Q. How do I check whether the witness node is already synced?

Run the info command in the CLI client and check the head_block_age value.

#### Q. If it seems to be unable to sync beyond a certain date?

You should always make sure you use the newest build available here_ as earlier releases will get stuck due to hard-forks.

#### Q. Is it safe to delete logs stored in witness_node_data_dirlogsp2p?
Yes, but

- they’re rotated automatically after 24 hours anyway
- if you don’t use them you should probably modify config.ini so they aren’t written to disk in the first place.

#### Q. What is the best way to interact with the witness node?
The only way you can interact with the witness node is through the CLI client by using its API. You can also use the GUI (i.e. the light client). 

In the GUI, open the Side Menu and go to [Settings] - [Nodes]. There are TABs (AVAILABLE, HIDDEN, and PERSONAL). Click **PERSONAL** and click [ADD NODE]. A form opens and type a Node name and an Address and Click [CONFIRM].

#### Q. What is the meaning of all those different text colors in the witness node console?

| color | |
|---|---|
| green | debug |
| white| info/default |
| yellow/brown| warning |
| red | error |
| blue | some kind of info, I don’t know |

Related source files are in libraries/fc/include/fc/log/ and libraries/fc/src/log/.

#### Q. Whose private key is [“BTS6MRyAjQ..”,”5KQwrPbwdL..”]? Why is it predefined ion the config.ini?

It’s a shared key for some special purpose. But I don’t remember what it is. If I remember BM or someone else has ever explained it in the forum, but I can’t find the post right now. Just let it be there. I think if you comment it out, it will appear again automatically, it’s generated by the code of witness_node.

***
### Worker

***

    

















