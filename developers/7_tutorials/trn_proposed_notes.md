## Proposed Transaction - Notes

*This section shares some of Proposed Transaction issues we have known.*

#### Contents:
- Proposal_delete_operation issues #75, #146 (closed)
- Unable to propose a proposal with an `approve_proposal` operation #214, #658
- An exception that is thrown logs an object that has been deleted #383
- [#383] Fix a crash in which we tried to log a proposal object that may have been deleted. #739

***

## proposal_delete_operation issues #75, #146 (closed)

#### Issue

proposal_delete_operation doesn't recursively check the authorities in case when multi-sig authority involved. See https://bitsharestalk.org/index.php/topic,20861.msg271080.html#msg271080, it's a try to delete an incorrect committee proposal, but failed due to this reason:
10 assert_exception: Assert Exception required_approvals->find(o.fee_paying_account) != required_approvals->end(): Provided authority is not authoritative for this proposal. {"provided":"1.2.12376","required":["1.2.0"]}
This operation allows the early veto of a proposed transaction. It may be used by any account which is a required authority on the proposed transaction, when that account's holder feels the proposal is ill-advised and he decides he will never approve of it and wishes to put an end to all discussion of the issue.
Imo it's better to set a threshold equals to (total_weight - approve_threshold) for proposal_delete_operation, so the proposal will only be deleted if enough authorities agree to delete the proposal.

#### Findings/Result

- The operation will delete the proposal from the database. text is here: https://github.com/bitshares/bitshares-core/blob/master/libraries/chain/include/graphene/chain/protocol/proposal.hpp#L144-L154.
- and actual delete is at: https://github.com/bitshares/bitshares-core/blob/master/libraries/chain/proposal_evaluator.cpp#L206.

*** 

## Unable to propose a proposal with an `approve_proposal` operation #214, #658

#### Issue

Got this message while trying to propose a proposal with an `approve_proposal` operation:

    10 assert_exception: Assert Exception
    other.size() == 0:
        {}
        th_a  proposal_evaluator.cpp:54 do_evaluate

The issue is probably caused by lack of size checking on `auth.key_auths` before [this line in proposal.cpp](https://github.com/cryptonomex/graphene/blob/2.0.160328/libraries/chain/protocol/proposal.cpp#L92), imo the code could be

    if( auth.key_auths.size() > 0 )
       o.emplace_back( std::move(auth) );

To fix this issue, a hard fork is needed.

#### Findings/Result

Actually it should be `proposal_update_operation` (and if to add an account authority to "approve" the proposal).


***

## An exception that is thrown logs an object that has been deleted #383

#### Issue

In [this line](https://github.com/bitshares/bitshares-core/blob/master/libraries/chain/db_block.cpp#L295), we throw an exception with (proposal) but we might have deleted proposal in a [previous line](https://github.com/bitshares/bitshares-core/blob/master/libraries/chain/db_block.cpp#L274).

#### Findings/Result


## [#383] Fix a crash in which we tried to log a proposal object that may have been deleted. #739



***
