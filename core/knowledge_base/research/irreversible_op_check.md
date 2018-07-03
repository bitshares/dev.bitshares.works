## How to Determine an Operation is Irreversible.


In the BitShars, if you want to know your operation is irreversible (i.e., not able to be undone or altered), you can check the **unreturnable block** block number to determine whether the transaction can be roll back. "Unable to roll back" transactions in blocks and earlier blocks can be guaranteed not to roll back.

***

The following steps show how to find each block number and compare the values.

<br/>

### Step 1: Check `get_relative_account_history`
 
Use the `get_relative_account_history` command to query the history of the relative account to get the most recent operation number. 

The command returns operations relevant to the specified account referenced by an event numbering specific to the account. The current number of operations for the account can be found in the account statistics (or use 0 for start).  
 
    unlocked >>> get_relative_account_history account-name01 0 10 10

>Note: The current implementation has a maximum history size of 100 transactions. If you have missed more than 100 transaction with the current implementation, manual fixing is required.


**`get_relative_account_history`(string name, uint32_t stop, int limit, uint32_t start) const**  

|   |   |
|---|---|
| Return | A list of operations performed by account, ordered from most recent to oldest. <br/> a list of `operation_history_objects` |
| Parameters |- The account whose history should be queried   <br/> - stop: Sequence number of earliest operation. 0 is default and will query 'limit' number of operations.  <br/> - limit: Maximum number of operations to retrieve (must not exceed 100)  <br/> - start: 	Sequence number of the most recent operation to retrieve. 0 is default, which will start querying from the most recent operation. |

<br/>

### Step 2: Find `last_irreversible_block_num`
 
Use the command `get_dynamic_global_properties` in cli_wallet to get the block number that cannot be rolled back. 

The command returns the block chain’s rapidly-changing properties. The returned object contains information that changes every block interval such as the head block number, the next witness, etc. 

    get_dynamic_global_properties
    {
      "id": "2.1.0",
      "head_block_number": 3405374,
      ...
      "last_irreversible_block_num": 3392247
    }

- `head_block_number` is the latest block number
- `last_irreversible_block_num` is the block number that cannot be retired (roll back).

<br/>

### Step 3: Compare the block numbers.

If the most recent `block_num` is smaller than the `last_irreversible_block_num`, the operation is irreversible.



<br/>

***


(ref)
- [graphene::chain::operation_history_object Class Reference](https://bitshares.org/doxygen/classgraphene_1_1chain_1_1operation__history__object.html)

- [graphene::chain::dynamic_global_property_object Class Reference](https://bitshares.org/doxygen/classgraphene_1_1chain_1_1dynamic__global__property__object.html)

- [global_property_object.hpp](https://bitshares.org/doxygen/global__property__object_8hpp_source.html)

      FC_REFLECT_DERIVED( graphene::chain::`dynamic_global_property_object`, (graphene::db::object),
      (head_block_number)
      (head_block_id)
      (time)
      (current_witness)
      (next_maintenance_time)
      (last_budget_time)
      (witness_budget)
      (accounts_registered_this_interval)
      (recently_missed_count)
      (current_aslot)
      (recent_slots_filled)
       (dynamic_flags)
      (last_irreversible_block_num)
      )

  ***
  
