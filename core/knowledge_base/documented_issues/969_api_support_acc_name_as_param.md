###  Update all account-related API's to support account name as parameter #969
*Feature Release (201807)*
***

**User Story :**

As an API user I want to use account name directly for all APIs. Some APIs support account names, e.g. `get_accounts`, but others don't support, e.g. `get_relative_account_history`.

***

*This document purpose: To list APIs support **account names** as a parameter after Feature Release (201807). And related issues.*

***

The following API calls will be impacted with the proposed change:

### Database API:
- [x] get_accounts
- [x] get_account_references
- [x] get_vesting_balances
- [x] get_margin_positions
- [x] get_witness_by_account
- [x] get_committee_member_by_account
- [x] get_workers_by_account
- [x] get_full_accounts
- [x] get_account_balances
- [x] verify_account_authority
- [x] get_proposed_transactions
- [x] get_withdraw_permissions_by_giver
- [x] get_withdraw_permissions_by_recipient

### API:
- [x] get_account_history
- [x] get_account_history_by_operations
- [x] get_account_history_operations
- [x] get_relative_account_history

### Wallet:
- [x] resync
- [x] get_account
- [x] import_balance
- [x] create_committee_member
- [x] get_witness
- [x] get_committee_member
- [x] create_witness
- [x] get_vesting_balances
- [x] vote_for_committee_member
- [x] vote_for_witness
- [x] list_account_balances
- [x] get_account_history
- [x] get_account_history_by_operations
- [x] get_relative_account_history

Note: This feature will slightly impact performance but will greatly improve usability.

(e.g.) The change... 
- from    `get_account_balances( id, assets );`
- to(new) `get_account_balances( account_name_or_id, assets );`


#### APIs -already accepting account as a string
- verify_account_authority
- get_full_accounts


***

*related closed issues*

#### support account names or ids in all api calls #989

summary of changeds
- Function `get_account_from_string`(local scope) was added to `database_api` and called in all `database_api` related functions to get the account ID from a string when needed. This is used in all the impacted calls of `database_api` as the `account` params changed to string.
- `account_id_to_string` was added to `wallet` to cast `account_id_type` to `string`. When the wallet need to call a `database_api` function with an account, it converts to string if needed and then makes the call with the string.
- `api` now can connect to `database_api` and call functions(thanks @jmjatlanta), I exposed `get_account_id_from_string` so `api` can call this and get an ID from a string parameter and continue doing the usual stuff with the ID.

#### get_relative_account_history always return empty [] #1052

****

(*created: 7/11/2018*)


