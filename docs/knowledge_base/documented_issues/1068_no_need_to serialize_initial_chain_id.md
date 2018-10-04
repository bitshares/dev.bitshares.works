### No need to serialize `initial_chain_id` field when creating example genesis json? #1068



***

#### Issue

**User Story :** I noticed that create_example_genesis() initialize the initial_chain_id to string "BOGUS"'s sha256 hash, while the generated genesis file will be entirely used by egenesis or by --genesis-json option to calculate the real chain id, the initial_chain_id field in the genesis file actually represents a wrong chain id.

It confused me a while, maybe not serialize the initial_chain_id field when creating example genesis? Since new devs&admins would mostly using example genesis as a base, this could confuse them too.

***

**It's a hard fork change since serialization change will lead to different chain id. IMHO does not worth the efforts. To reduce confusion, we need documentation.**

***
*This document purpose:(not yet)*

***
