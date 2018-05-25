## Document the difference between impacted accounts and relevant accounts


#### Q.

They seem like similar ideas, why do we have both of them? What is different about them? Can we delete one of them?

***

#### Responses

-1-

- Impected sccount are based on the accounts affected by an operation and are used by account history.
- Relevant accounts are accounts related to an object.

-2-

- `get_relevant_accounts` in api.cpp is unused code. Presumably, both `get_relevant_accounts` and `get_impacted_accounts` in the app library were copied from `chain/db_notify.cpp`, which is where they are used.

This needs refactoring

***
