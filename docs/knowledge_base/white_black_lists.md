## Using White and Black Lists

### Table of Contents:

- [User Whitelists](.../knowledge_base/white_black_lists.md#user-whitelists)
- [Asset User Whitelists](../knowledge_base/white_black_lists.md#asset-user-whitelists)
- [Asset Market Whitelists](../knowledge_base/white_black_lists.md#asset-market-whitelists)

***

Some 3rd party service providers may want to select which customers are allowed to hold their assets (e.g., after verified their identity for KYC/AML). Those services can use so called **whitelists** (or, alternatively, blacklists) of their assets that will prevent unauthorized participants to use this particular asset.

In BitShares 2.0, account names (life-time members only) and also user-issued assets (i.e., UIA) have their individual whitelists. Hence, if you issue an IOU on the blockchain, you can define who can hold and trade your tokens, if you wish.

User whitelists on contrast can be used by independent KYC/AML providers to state proper verification. An asset issuer may then use those providers to oursource identity verification completely.

## User Whitelists

Any live-time member can cast an opinion about other accounts using white- and black-lists. They **do not** prevent anyone from interacting with your account but serve as a basis for *list authorities*.

### Examples

A user `white` can be added to the white-list of account `provider` with::

    >>> whitelist_account provider white white_listed true

In contrast a `black` user can be added to its blacklist with::

    >>> whitelist_account provider black black_listed true

Both can be removed from their lists with::

    >>> whitelist_account provider black no_listing true
    >>> whitelist_account provider white no_listing true

### Definition

White- and Black-listing of accounts works with the following API call:

**signed_transaction `graphene::wallet::wallet_api::whitelist_account`(string authorizing_account, string account_to_list, account_whitelist_operation::account_listing new_listing_status, bool broadcast = false)

Whitelist and blacklist accounts, primarily for transacting in whitelisted assets.

Accounts can freely specify opinions about other accounts, in the form of either whitelisting or blacklisting them. This information is used in chain validation only to determine whether an account is authorized to transact in an asset type which enforces a whitelist, but third parties can use this information for other uses as well, as long as it does not conflict with the use of whitelisted assets.

An asset which enforces a whitelist specifies a list of accounts to maintain its whitelist, and a list of accounts to maintain its blacklist. In order for a given account A to hold and transact in a whitelisted asset S, A must be whitelisted by at least one of S’s whitelist_authorities and blacklisted by none of S’s blacklist_authorities. If A receives a balance of S, and is later removed from the whitelist(s) which allowed it to hold S, or added to any blacklist S specifies as authoritative, A’s balance of S will be frozen until A’s authorization is reinstated.

|  |  |
| ------------ |---------- |
|  Return      |  the signed transaction changing the whitelisting status |
|  ---------   |  |
| Parameters   |  |                           
| `authorizing_account` | the account who is doing the whitelisting  |                            
| `account_to_list`     |  the account being whitelisted   |  
| `new_listing_status`  |   the new whitelisting status   |  
| `broadcast`  |  true to broadcast the transaction on the network  |  


It expects a new_listing_status from

enum `graphene::chain::account_whitelist_operation::account_listing`

**Values:**

|  |  |
| ------------ |---------- |
|  `no_listing` = 0x0      | No opinion is specified about this account.   |
|  `white_listed` = 0x1    | This account is whitelisted, but not blacklisted.   |
|  `black_listed` = 0x2    | This account is blacklisted, but not whitelisted.    |
|  `white_and_black_listed` = white_listed : black_listed | This account is both whitelisted and blacklisted.  |

***

## Asset User Whitelists

Asset User white- and black-lists serve the need for companies to restrict service to a subset of accounts. For instance, a fiat gateway may require to follow KYC/AML regulations and can hence only deal with those customers that have been verified accordingly. If the issuer of an user-issued asset desires, he may set a restriction so that only users on the white-list (and/or **not** on the blacklist) are allowed to hold his token.

Instead of putting all verified accounts into the respective asset’s white-list directly, BitShares 2.0 allows to define one or several white-list *authorities*. In practice, the white- and black-lists of these accounts are combined and serve as white- and black-lists for the asset.

This allows for easy out-sourcing of KYC/AML verification to 3rd-party providers.

>**Note:** By removing a user from the whitelist, funds can effectively be frozen.

### Example

Let’s assume user `alice` wants to own a gateways IOUs called `G.USD` which are restricted by a whitelists. Before being able to own `G.USD`, `alice` needs to be white-listed by one of the authorities of `G.USD`.

#### Defining an asset’s list authorities

We now define the authorities (i.e. accounts) that define the white- and blacklist of the asset `G.USD`. We add `g-issuer` and `kycprovider` to the white- and black-list::

    >>> update_asset G.USD "" "{blacklist_authorities:[g-issuer, kycprovider], whitelist_authorities:[g-issuer, kycprovider], flags:white_list}" true

>**Note:** The third argument for `update_asset` replaces the existing settings. Make sure to have all desired settings present.
Adding alice to


#### Adding `alice` to a whitelist

Let’s assume the only authority is the issuer `g-issuer` himself for simplicity. The issuer now needs to add `alice` to `g-issuer`’s account whitelist::

    >>> whitelist_account g-issuer alice white_listed true

### Definition

White- and Black-listing of assets works with the following API call:

**signed_transaction `graphene::wallet::wallet_api::update_asset`(string symbol, optional<string> new_issuer, asset_options new_options, bool broadcast = false)**

Update the core options on an asset. There are a number of options which all assets in the network use. These options are enumerated in the `asset_object::asset_options` struct. This command is used to update these options for an existing asset.

>**Note:** This operation cannot be used to update BitAsset-specific options. For these options, `update_bitasset()` instead.

|  |  |
| ------------ |---------- |
|  Return      |  the signed transaction updating the asset |
|  ---------   |  |
| Parameters   |  |                           
| `symbol` | the name or id of the asset to update  |                            
| `new_issuer`     | if changing the asset’s issuer, the name or id of the new issuer. null if you wish to remain the issuer of the asset   |  
| `new_options`  |  the new asset_options object, which will entirely replace the existing options.  |  
| `broadcast`  |  true to broadcast the transaction on the network  |  


The asset_options struct contains options available on all assets in the network

struct `graphene::chain::asset_options`

>**Note:** Changes to this struct will break protocol compatibility 

**Public Functions**

void `validate()` const 
- Perform internal consistency checks.
   - **Exceptions**
      - fc::exception: if any check fails

**Public Members**

|  |  |
| ------------ |---------- |
|  share_type `max_supply` = GRAPHENE_MAX_SHARE_SUPPLY      |  The maximum supply of this asset which may exist at any given time. This can be as large as GRAPHENE_MAX_SHARE_SUPPLY  |
|  uint16_t `market_fee_percent` = 0   | When this asset is traded on the markets, this percentage of the total traded will be exacted and paid to the issuer. This is a fixed point value, representing hundredths of a percent, i.e. a value of 100 in this field means a 1% fee is charged on market trades of this asset. |
| share_type `max_market_fee` = GRAPHENE_MAX_SHARE_SUPPLY   | Market fees calculated as market_fee_percent of the traded volume are capped to this value. |                           
| uint16_t `issuer_permissions` = UIA_ASSET_ISSUER_PERMISSION_MASK | The flags which the issuer has permission to update. See asset_issuer_permission_flags.   |                            
| uint16_t `flags` = 0 | The currently active flags on this permission. See asset_issuer_permission_flags.  |  
| price `core_exchange_rate`  |  When a non-core asset is used to pay a fee, the blockchain must convert that asset to core asset in order to accept the fee. If this asset’s fee pool is funded, the chain will automatically deposite fees in this asset to its accumulated fees, and withdraw from the fee pool the same amount as converted at the core exchange rate.  |  
| flat_set<account_id_type> `whitelist_authorities`  |  A set of accounts which maintain whitelists to consult for this asset. If whitelist_authorities is non-empty, then only accounts in whitelist_authorities are allowed to hold, use, or transfer the asset. |  
|flat_set<account_id_type> `blacklist_authorities` |  A set of accounts which maintain blacklists to consult for this asset. If flags & white_list is set, an account may only send, receive, trade, etc. in this asset if none of these accounts appears in its account_object::blacklisting_accounts field. If the account is blacklisted, it may not transact in this asset even if it is also whitelisted.  |
| flat_set<asset_id_type> `whitelist_markets` | defines the assets that this asset may be traded against in the market  |
| flat_set<asset_id_type> `blacklist_markets` | defines the assets that this asset may not be traded against in the market, must not overlap whitelist  |
| string `description` | data that describes the meaning/purpose of this asset, fee will be charged proportional to size of description. |

enum `graphene::chain::asset_issuer_permission_flags`

**Values**

|  |  |
|---|---|
| `charge_market_fee` = 0x01 | an issuer-specified percentage of all market trades in this asset is paid to the issuer  |
| `white_list` = 0x02 |accounts must be whitelisted in order to hold this asset  |
| `override_authority` = 0x04 | issuer may transfer asset back to himself  |
| `transfer_restricted` = 0x08 |require the issuer to be one party to every transfer   |
| `disable_force_settle` = 0x10 |disable force settling   |
| `global_settle` = 0x20 |allow the bitasset issuer to force a global settling this may be set in permissions, but not flags   |
| `disable_confidential` = 0x40 |allow the asset to be used with confidential transactions  |
| `witness_fed_asset` = 0x80 | allow the asset to be fed by witnesses  |
| `committee_fed_asse`t = 0x100 | allow the asset to be fed by the committee  |

***

## Asset Market Whitelists

An issuer of an user-issued-asset may want to restrict trading partners for his assets for legal reasons. For instance, a gateway for US dollar may not be allowed to let his customers trade USD against CNY because additional licenses would be required. Hence, in BitShares 2.0 we let issuers chose to restrict trading partners with white- and black-lists.

### Example

A gateway with IOU `G.USD` that wants to prevent his customers from trading `G.USD` against `bitCNY` can do so by adding `bitCNY` to the blacklist of `G.USD` by issuing::

    >>> update_asset G.USD "" "{blacklist_markets:[CNY]}" true

Alternatively, if an issuer may want to only open the market `G.USD : bitUSD` with his asset, he can do so as well with::

    >>> update_asset G.USD "" "{whitelist_markets:[USD]}" true

>**Note:** The third argument for update_asset replaces the existing settings. Make sure to have all desired settings present.

### Defintion

Asset Market white-lists work with the following API call:

**signed_transaction `graphene::wallet::wallet_api::update_asset`(string symbol, optional<string> new_issuer, asset_options new_options, bool broadcast = false)

Update the core options on an asset. There are a number of options which all assets in the network use. These options are enumerated in the asset_object::asset_options struct. This command is used to update these options for an existing asset.

**Note:** This operation cannot be used to update BitAsset-specific options. For these options, `update_bitasset()` instead.

|  |  |
| ------------ |---------- |
|  Return      |  the signed transaction updating the asset  |
|  ---------   |  |
| Parameters   |  |                           
| `symbol` | the name or id of the asset to update  |                            
| `new_issuer` | if changing the asset’s issuer, the name or id of the new issuer. null if you wish to remain the issuer of the asset   |  
| `new_options`  | the new asset_options object, which will entirely replace the existing options.  |  
| `broadcast`  |  true to broadcast the transaction on the network  |  


The *asset_options* struct contains options available on all assets in the network. 

struct `graphene::chain::asset_options`

>**Note:** Changes to this struct will break protocol compatibility 

**Public Functions**

void `validate()` const 
- Perform internal consistency checks.
   - **Exceptions**
      - fc::exception: if any check fails

**Public Members**

|  |  |
| ------------ |---------- |
|  share_type `max_supply` = GRAPHENE_MAX_SHARE_SUPPLY      |  The maximum supply of this asset which may exist at any given time. This can be as large as GRAPHENE_MAX_SHARE_SUPPLY  |
|  uint16_t `market_fee_percent` = 0   | When this asset is traded on the markets, this percentage of the total traded will be exacted and paid to the issuer. This is a fixed point value, representing hundredths of a percent, i.e. a value of 100 in this field means a 1% fee is charged on market trades of this asset. |
| share_type `max_market_fee` = GRAPHENE_MAX_SHARE_SUPPLY   | Market fees calculated as market_fee_percent of the traded volume are capped to this value. |                           
| uint16_t `issuer_permissions` = UIA_ASSET_ISSUER_PERMISSION_MASK | The flags which the issuer has permission to update. See asset_issuer_permission_flags.   |                            
| uint16_t `flags` = 0 | The currently active flags on this permission. See asset_issuer_permission_flags.  |  
| price `core_exchange_rate`  |  When a non-core asset is used to pay a fee, the blockchain must convert that asset to core asset in order to accept the fee. If this asset’s fee pool is funded, the chain will automatically deposite fees in this asset to its accumulated fees, and withdraw from the fee pool the same amount as converted at the core exchange rate.  |  
| flat_set<account_id_type> `whitelist_authorities`  |  A set of accounts which maintain whitelists to consult for this asset. If whitelist_authorities is non-empty, then only accounts in whitelist_authorities are allowed to hold, use, or transfer the asset. |  
|flat_set<account_id_type> `blacklist_authorities` |  A set of accounts which maintain blacklists to consult for this asset. If flags & white_list is set, an account may only send, receive, trade, etc. in this asset if none of these accounts appears in its account_object::blacklisting_accounts field. If the account is blacklisted, it may not transact in this asset even if it is also whitelisted.  |
| flat_set<asset_id_type> `whitelist_markets` | defines the assets that this asset may be traded against in the market  |
| flat_set<asset_id_type> `blacklist_markets` | defines the assets that this asset may not be traded against in the market, must not overlap whitelist  |
| string `description` | data that describes the meaning/purpose of this asset, fee will be charged proportional to size of description. |

enum `graphene::chain::asset_issuer_permission_flags`

**Values**

|  |  |
|---|---|
| `charge_market_fee` = 0x01 | an issuer-specified percentage of all market trades in this asset is paid to the issuer  |
| `white_list` = 0x02 |accounts must be whitelisted in order to hold this asset  |
| `override_authority` = 0x04 | issuer may transfer asset back to himself  |
| `transfer_restricted` = 0x08 |require the issuer to be one party to every transfer   |
| `disable_force_settle` = 0x10 |disable force settling   |
| `global_settle` = 0x20 |allow the bitasset issuer to force a global settling this may be set in permissions, but not flags   |
| `disable_confidential` = 0x40 |allow the asset to be used with confidential transactions  |
| `witness_fed_asset` = 0x80 | allow the asset to be fed by witnesses  |
| `committee_fed_asse`t = 0x100 | allow the asset to be fed by the committee  |



***

(ref)

- http://docs.bitshares.org/integration/supporting-features.html
- http://docs.bitshares.org/integration/user-whitelist.html
- http://docs.bitshares.org/integration/asset-whitelist.html
- 




