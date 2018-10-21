
.. _lib-chain-db:

*******************************************
Chain - db_xxx.cpp
*******************************************


* File directory: (../ibraries/chain/xxx.cpp)

.. contents:: Table of Contents
   :local:
   
-------


db_balance.cpp
===============================

get_balance
-------------------------

.. code-block:: cpp 

	asset database::get_balance(account_id_type owner, asset_id_type asset_id) const
	{
	   auto& index = get_index_type<account_balance_index>().indices().get<by_account_asset>();
	   auto itr = index.find(boost::make_tuple(owner, asset_id));
	   if( itr == index.end() )
		  return asset(0, asset_id);
	   return itr->get_balance();
	}
	
	
get_balance	
-------------------------

.. code-block:: cpp 

	asset database::get_balance(const account_object& owner, const asset_object& asset_obj) const
	{
	   return get_balance(owner.get_id(), asset_obj.get_id());
	}
	
	
to_pretty_string	
-------------------------

.. code-block:: cpp 

	string database::to_pretty_string( const asset& a )const
	{
	   return a.asset_id(*this).amount_to_pretty_string(a.amount);
	}
	
	
adjust_balance	
-------------------------

.. code-block:: cpp 

	void database::adjust_balance(account_id_type account, asset delta )
	{ try {
	   if( delta.amount == 0 )
		  return;

	   auto& index = get_index_type<account_balance_index>().indices().get<by_account_asset>();
	   auto itr = index.find(boost::make_tuple(account, delta.asset_id));
	   if(itr == index.end())
	   {
		  FC_ASSERT( delta.amount > 0, "Insufficient Balance: ${a}'s balance of ${b} is less than required ${r}", 
					 ("a",account(*this).name)
					 ("b",to_pretty_string(asset(0,delta.asset_id)))
					 ("r",to_pretty_string(-delta)));
		  create<account_balance_object>([account,&delta](account_balance_object& b) {
			 b.owner = account;
			 b.asset_type = delta.asset_id;
			 b.balance = delta.amount.value;
			 if( b.asset_type == asset_id_type() ) // CORE asset
				b.maintenance_flag = true;
		  });
	   } else {
		  if( delta.amount < 0 )
			 FC_ASSERT( itr->get_balance() >= -delta, "Insufficient Balance: ${a}'s balance of ${b} is less than required ${r}", ("a",account(*this).name)("b",to_pretty_string(itr->get_balance()))("r",to_pretty_string(-delta)));
		  modify(*itr, [delta](account_balance_object& b) {
			 b.adjust_balance(delta);
		  });
	   }

	} FC_CAPTURE_AND_RETHROW( (account)(delta) ) }
	
	
deposit_lazy_vesting
-------------------------

.. code-block:: cpp 

	optional< vesting_balance_id_type > database::deposit_lazy_vesting(
	   const optional< vesting_balance_id_type >& ovbid,
	   share_type amount, uint32_t req_vesting_seconds,
	   account_id_type req_owner,
	   bool require_vesting )
	{
	   if( amount == 0 )
		  return optional< vesting_balance_id_type >();

	   fc::time_point_sec now = head_block_time();

	   while( true )
	   {
		  if( !ovbid.valid() )
			 break;
		  const vesting_balance_object& vbo = (*ovbid)(*this);
		  if( vbo.owner != req_owner )
			 break;
		  if( vbo.policy.which() != vesting_policy::tag< cdd_vesting_policy >::value )
			 break;
		  if( vbo.policy.get< cdd_vesting_policy >().vesting_seconds != req_vesting_seconds )
			 break;
		  modify( vbo, [&]( vesting_balance_object& _vbo )
		  {
			 if( require_vesting )
				_vbo.deposit(now, amount);
			 else
				_vbo.deposit_vested(now, amount);
		  } );
		  return optional< vesting_balance_id_type >();
	   }

	   const vesting_balance_object& vbo = create< vesting_balance_object >( [&]( vesting_balance_object& _vbo )
	   {
		  _vbo.owner = req_owner;
		  _vbo.balance = amount;

		  cdd_vesting_policy policy;
		  policy.vesting_seconds = req_vesting_seconds;
		  policy.coin_seconds_earned = require_vesting ? 0 : amount.value * policy.vesting_seconds;
		  policy.coin_seconds_earned_last_update = now;

		  _vbo.policy = policy;
	   } );

	   return vbo.id;
	}
	
	
deposit_cashback	
-------------------------

.. code-block:: cpp 

	void database::deposit_cashback(const account_object& acct, share_type amount, bool require_vesting)
	{
	   // If we don't have a VBO, or if it has the wrong maturity
	   // due to a policy change, cut it loose.

	   if( amount == 0 )
		  return;

	   if( acct.get_id() == GRAPHENE_COMMITTEE_ACCOUNT || acct.get_id() == GRAPHENE_WITNESS_ACCOUNT ||
		   acct.get_id() == GRAPHENE_RELAXED_COMMITTEE_ACCOUNT || acct.get_id() == GRAPHENE_NULL_ACCOUNT ||
		   acct.get_id() == GRAPHENE_TEMP_ACCOUNT )
	   {
		  // The blockchain's accounts do not get cashback; it simply goes to the reserve pool.
		  modify( get_core_dynamic_data(), [amount](asset_dynamic_data_object& d) {
			 d.current_supply -= amount;
		  });
		  return;
	   }

	   optional< vesting_balance_id_type > new_vbid = deposit_lazy_vesting(
		  acct.cashback_vb,
		  amount,
		  get_global_properties().parameters.cashback_vesting_period_seconds,
		  acct.id,
		  require_vesting );

	   if( new_vbid.valid() )
	   {
		  modify( acct, [&new_vbid]( account_object& _acct )
		  {
			 _acct.cashback_vb = *new_vbid;
		  } );
		  modify( acct.statistics( *this ), []( account_statistics_object& aso )
		  {
			 aso.has_cashback_vb = true;
		  } );
	   }

	   return;
	}
	
	
deposit_witness_pay	
-------------------------

.. code-block:: cpp 

	void database::deposit_witness_pay(const witness_object& wit, share_type amount)
	{
	   if( amount == 0 )
		  return;

	   optional< vesting_balance_id_type > new_vbid = deposit_lazy_vesting(
		  wit.pay_vb,
		  amount,
		  get_global_properties().parameters.witness_pay_vesting_seconds,
		  wit.witness_account,
		  true );

	   if( new_vbid.valid() )
	   {
		  modify( wit, [&]( witness_object& _wit )
		  {
			 _wit.pay_vb = *new_vbid;
		  } );
	   }

	   return;
	}




-------------------

|

db_block
================================



is_known_block
-------------------------

.. code-block:: cpp 

	bool database::is_known_block( const block_id_type& id )const
	{
	   return _fork_db.is_known_block(id) || _block_id_to_block.contains(id);
	}
	


is_known_transaction
-------------------------
* Only return true *if* the transaction has not expired or been invalidated. If this method is called with a VERY old transaction we will return false, they should query things by blocks if they are that old.

.. code-block:: cpp 
	
	bool database::is_known_transaction( const transaction_id_type& id )const
	{
	   const auto& trx_idx = get_index_type<transaction_index>().indices().get<by_trx_id>();
	   return trx_idx.find( id ) != trx_idx.end();
	}



get_block_id_for_num
-------------------------

.. code-block:: cpp 
	
	block_id_type  database::get_block_id_for_num( uint32_t block_num )const
	{ try {
	   return _block_id_to_block.fetch_block_id( block_num );
	} FC_CAPTURE_AND_RETHROW( (block_num) ) }


fetch_block_by_id
-------------------------

.. code-block:: cpp 
	
	optional<signed_block> database::fetch_block_by_id( const block_id_type& id )const
	{
	   auto b = _fork_db.fetch_block( id );
	   if( !b )
		  return _block_id_to_block.fetch_optional(id);
	   return b->data;
	}


fetch_block_by_number
-------------------------

.. code-block:: cpp 

	optional<signed_block> database::fetch_block_by_number( uint32_t num )const
	{
	   auto results = _fork_db.fetch_block_by_number(num);
	   if( results.size() == 1 )
		  return results[0]->data;
	   else
		  return _block_id_to_block.fetch_by_number(num);
	   return optional<signed_block>();
	}


get_recent_transaction
-------------------------

.. code-block:: cpp 

	const signed_transaction& database::get_recent_transaction(const transaction_id_type& trx_id) const
	{
	   auto& index = get_index_type<transaction_index>().indices().get<by_trx_id>();
	   auto itr = index.find(trx_id);
	   FC_ASSERT(itr != index.end());
	   return itr->trx;
	}


get_block_ids_on_fork
-------------------------

.. code-block:: cpp 

	std::vector<block_id_type> database::get_block_ids_on_fork(block_id_type head_of_fork) const
	{
	  pair<fork_database::branch_type, fork_database::branch_type> branches = _fork_db.fetch_branch_from(head_block_id(), head_of_fork);
	  if( !((branches.first.back()->previous_id() == branches.second.back()->previous_id())) )
	  {
		 edump( (head_of_fork)
				(head_block_id())
				(branches.first.size())
				(branches.second.size()) );
		 assert(branches.first.back()->previous_id() == branches.second.back()->previous_id());
	  }
	  std::vector<block_id_type> result;
	  for (const item_ptr& fork_block : branches.second)
		result.emplace_back(fork_block->id);
	  result.emplace_back(branches.first.back()->previous_id());
	  return result;
	}


push_block
-------------------------
* Push block "may fail" in which case every partial change is unwound.  After push block is successful the block is appended to the chain database on disk. 
*  @return true if we switched forks as a result of this push.
	 
.. code-block:: cpp 

	bool database::push_block(const signed_block& new_block, uint32_t skip)
	{
	//   idump((new_block.block_num())(new_block.id())(new_block.timestamp)(new_block.previous));
	   bool result;
	   detail::with_skip_flags( *this, skip, [&]()
	   {
		  detail::without_pending_transactions( *this, std::move(_pending_tx),
		  [&]()
		  {
			 result = _push_block(new_block);
		  });
	   });
	   return result;
	}


	bool database::_push_block(const signed_block& new_block)
	{ try {
	   uint32_t skip = get_node_properties().skip_flags;
	   if( !(skip&skip_fork_db) )
	   {
		  /// TODO: if the block is greater than the head block and before the next maitenance interval
		  // verify that the block signer is in the current set of active witnesses.

		  shared_ptr<fork_item> new_head = _fork_db.push_block(new_block);
		  //If the head block from the longest chain does not build off of the current head, we need to switch forks.
		  if( new_head->data.previous != head_block_id() )
		  {
			 //If the newly pushed block is the same height as head, we get head back in new_head
			 //Only switch forks if new_head is actually higher than head
			 if( new_head->data.block_num() > head_block_num() )
			 {
				wlog( "Switching to fork: ${id}", ("id",new_head->data.id()) );
				auto branches = _fork_db.fetch_branch_from(new_head->data.id(), head_block_id());

				// pop blocks until we hit the forked block
				while( head_block_id() != branches.second.back()->data.previous )
				{
				   ilog( "popping block #${n} ${id}", ("n",head_block_num())("id",head_block_id()) );
				   pop_block();
				}

				// push all blocks on the new fork
				for( auto ritr = branches.first.rbegin(); ritr != branches.first.rend(); ++ritr )
				{
					ilog( "pushing block from fork #${n} ${id}", ("n",(*ritr)->data.block_num())("id",(*ritr)->id) );
					optional<fc::exception> except;
					try {
					   undo_database::session session = _undo_db.start_undo_session();
					   apply_block( (*ritr)->data, skip );
					   _block_id_to_block.store( (*ritr)->id, (*ritr)->data );
					   session.commit();
					}
					catch ( const fc::exception& e ) { except = e; }
					if( except )
					{
					   wlog( "exception thrown while switching forks ${e}", ("e",except->to_detail_string() ) );
					   // remove the rest of branches.first from the fork_db, those blocks are invalid
					   while( ritr != branches.first.rend() )
					   {
						  ilog( "removing block from fork_db #${n} ${id}", ("n",(*ritr)->data.block_num())("id",(*ritr)->id) );
						  _fork_db.remove( (*ritr)->id );
						  ++ritr;
					   }
					   _fork_db.set_head( branches.second.front() );

					   // pop all blocks from the bad fork
					   while( head_block_id() != branches.second.back()->data.previous )
					   {
						  ilog( "popping block #${n} ${id}", ("n",head_block_num())("id",head_block_id()) );
						  pop_block();
					   }

					   ilog( "Switching back to fork: ${id}", ("id",branches.second.front()->data.id()) );
					   // restore all blocks from the good fork
					   for( auto ritr2 = branches.second.rbegin(); ritr2 != branches.second.rend(); ++ritr2 )
					   {
						  ilog( "pushing block #${n} ${id}", ("n",(*ritr2)->data.block_num())("id",(*ritr2)->id) );
						  auto session = _undo_db.start_undo_session();
						  apply_block( (*ritr2)->data, skip );
						  _block_id_to_block.store( (*ritr2)->id, (*ritr2)->data );
						  session.commit();
					   }
					   throw *except;
					}
				}
				return true;
			 }
			 else return false;
		  }
	   }

	   try {
		  auto session = _undo_db.start_undo_session();
		  apply_block(new_block, skip);
		  _block_id_to_block.store(new_block.id(), new_block);
		  session.commit();
	   } catch ( const fc::exception& e ) {
		  elog("Failed to push new block:\n${e}", ("e", e.to_detail_string()));
		  _fork_db.remove(new_block.id());
		  throw;
	   }

	   return false;
	} FC_CAPTURE_AND_RETHROW( (new_block) ) }


push_transaction
-------------------------
* Attempts to push the transaction into the pending queue When called to push a locally generated transaction, set the skip_block_size_check bit on the skip argument. This will allow the transaction to be pushed even if it causes the pending block size to exceed the maximum block size.
* Although the transaction will probably not propagate further now, as the peers are likely to have their pending queues full as well, it will be kept in the queue to be propagated later when a new block flushes out the pending  queues.

.. code-block:: cpp 

	processed_transaction database::push_transaction( const signed_transaction& trx, uint32_t skip )
	{ try {
	   processed_transaction result;
	   detail::with_skip_flags( *this, skip, [&]()
	   {
		  result = _push_transaction( trx );
	   } );
	   return result;
	} FC_CAPTURE_AND_RETHROW( (trx) ) }


	processed_transaction database::_push_transaction( const signed_transaction& trx )
	{
	   // If this is the first transaction pushed after applying a block, start a new undo session.
	   // This allows us to quickly rewind to the clean state of the head block, in case a new block arrives.
	   if( !_pending_tx_session.valid() )
		  _pending_tx_session = _undo_db.start_undo_session();

	   // Create a temporary undo session as a child of _pending_tx_session.
	   // The temporary session will be discarded by the destructor if
	   // _apply_transaction fails.  If we make it to merge(), we
	   // apply the changes.

	   auto temp_session = _undo_db.start_undo_session();
	   auto processed_trx = _apply_transaction( trx );
	   _pending_tx.push_back(processed_trx);

	   // notify_changed_objects();
	   // The transaction applied successfully. Merge its changes into the pending block session.
	   temp_session.merge();

	   // notify anyone listening to pending transactions
	   notify_on_pending_transaction( trx );
	   return processed_trx;
	}


validate_transaction
-------------------------

.. code-block:: cpp 

	processed_transaction database::validate_transaction( const signed_transaction& trx )
	{
	   auto session = _undo_db.start_undo_session();
	   return _apply_transaction( trx );
	}


push_proposal_nesting_guard
-------------------------

.. code-block:: cpp 

	class push_proposal_nesting_guard {
	public:
	   push_proposal_nesting_guard( uint32_t& nesting_counter, const database& db )
		  : orig_value(nesting_counter), counter(nesting_counter)
	   {
		  FC_ASSERT( counter < db.get_global_properties().active_witnesses.size() * 2, "Max proposal nesting depth exceeded!" );
		  counter++;
	   }
	   ~push_proposal_nesting_guard()
	   {
		  if( --counter != orig_value )
			 elog( "Unexpected proposal nesting count value: ${n} != ${o}", ("n",counter)("o",orig_value) );
	   }
	private:
		const uint32_t  orig_value;
		uint32_t& counter;
	};


push_proposal
-------------------------

.. code-block:: cpp 

	processed_transaction database::push_proposal(const proposal_object& proposal)
	{ try {
	   transaction_evaluation_state eval_state(this);
	   eval_state._is_proposed_trx = true;

	   eval_state.operation_results.reserve(proposal.proposed_transaction.operations.size());
	   processed_transaction ptrx(proposal.proposed_transaction);
	   eval_state._trx = &ptrx;
	   size_t old_applied_ops_size = _applied_ops.size();

	   try {
		  push_proposal_nesting_guard guard( _push_proposal_nesting_depth, *this );
		  if( _undo_db.size() >= _undo_db.max_size() )
			 _undo_db.set_max_size( _undo_db.size() + 1 );
		  auto session = _undo_db.start_undo_session(true);
		  for( auto& op : proposal.proposed_transaction.operations )
			 eval_state.operation_results.emplace_back(apply_operation(eval_state, op));
		  remove(proposal);
		  session.merge();
	   } catch ( const fc::exception& e ) {
		  if( head_block_time() <= HARDFORK_483_TIME )
		  {
			 for( size_t i=old_applied_ops_size,n=_applied_ops.size(); i<n; i++ )
			 {
				ilog( "removing failed operation from applied_ops: ${op}", ("op", *(_applied_ops[i])) );
				_applied_ops[i].reset();
			 }
		  }
		  else
		  {
			 _applied_ops.resize( old_applied_ops_size );
		  }
		  wlog( "${e}", ("e",e.to_detail_string() ) );
		  throw;
	   }

	   ptrx.operation_results = std::move(eval_state.operation_results);
	   return ptrx;
	} FC_CAPTURE_AND_RETHROW( (proposal) ) }


generate_block
-------------------------

.. code-block:: cpp 

	signed_block database::generate_block(
	   fc::time_point_sec when,
	   witness_id_type witness_id,
	   const fc::ecc::private_key& block_signing_private_key,
	   uint32_t skip /* = 0 */
	   )
	{ try {
	   signed_block result;
	   detail::with_skip_flags( *this, skip, [&]()
	   {
		  result = _generate_block( when, witness_id, block_signing_private_key );
	   } );
	   return result;
	} FC_CAPTURE_AND_RETHROW() }


	signed_block database::_generate_block(
	   fc::time_point_sec when,
	   witness_id_type witness_id,
	   const fc::ecc::private_key& block_signing_private_key
	   )
	{
	   try {
	   uint32_t skip = get_node_properties().skip_flags;
	   uint32_t slot_num = get_slot_at_time( when );
	   FC_ASSERT( slot_num > 0 );
	   witness_id_type scheduled_witness = get_scheduled_witness( slot_num );
	   FC_ASSERT( scheduled_witness == witness_id );

	   const auto& witness_obj = witness_id(*this);

	   if( !(skip & skip_witness_signature) )
		  FC_ASSERT( witness_obj.signing_key == block_signing_private_key.get_public_key() );

	   static const size_t max_block_header_size = fc::raw::pack_size( signed_block_header() ) + 4;
	   auto maximum_block_size = get_global_properties().parameters.maximum_block_size;
	   size_t total_block_size = max_block_header_size;

	   signed_block pending_block;

	   //
	   // The following code throws away existing pending_tx_session and
	   // rebuilds it by re-applying pending transactions.
	   //
	   // This rebuild is necessary because pending transactions' validity
	   // and semantics may have changed since they were received, because
	   // time-based semantics are evaluated based on the current block
	   // time.  These changes can only be reflected in the database when
	   // the value of the "when" variable is known, which means we need to
	   // re-apply pending transactions in this method.
	   //
	   _pending_tx_session.reset();
	   _pending_tx_session = _undo_db.start_undo_session();

	   uint64_t postponed_tx_count = 0;
	   // pop pending state (reset to head block state)
	   for( const processed_transaction& tx : _pending_tx )
	   {
		  size_t new_total_size = total_block_size + fc::raw::pack_size( tx );

		  // postpone transaction if it would make block too big
		  if( new_total_size >= maximum_block_size )
		  {
			 postponed_tx_count++;
			 continue;
		  }

		  try
		  {
			 auto temp_session = _undo_db.start_undo_session();
			 processed_transaction ptx = _apply_transaction( tx );
			 temp_session.merge();

			 // We have to recompute pack_size(ptx) because it may be different
			 // than pack_size(tx) (i.e. if one or more results increased
			 // their size)
			 total_block_size += fc::raw::pack_size( ptx );
			 pending_block.transactions.push_back( ptx );
		  }
		  catch ( const fc::exception& e )
		  {
			 // Do nothing, transaction will not be re-applied
			 wlog( "Transaction was not processed while generating block due to ${e}", ("e", e) );
			 wlog( "The transaction was ${t}", ("t", tx) );
		  }
	   }
	   if( postponed_tx_count > 0 )
	   {
		  wlog( "Postponed ${n} transactions due to block size limit", ("n", postponed_tx_count) );
	   }

	   _pending_tx_session.reset();

	   // We have temporarily broken the invariant that
	   // _pending_tx_session is the result of applying _pending_tx, as
	   // _pending_tx now consists of the set of postponed transactions.
	   // However, the push_block() call below will re-create the
	   // _pending_tx_session.

	   pending_block.previous = head_block_id();
	   pending_block.timestamp = when;
	   pending_block.transaction_merkle_root = pending_block.calculate_merkle_root();
	   pending_block.witness = witness_id;

	   if( !(skip & skip_witness_signature) )
		  pending_block.sign( block_signing_private_key );

	   // TODO:  Move this to _push_block() so session is restored.
	   if( !(skip & skip_block_size_check) )
	   {
		  FC_ASSERT( fc::raw::pack_size(pending_block) <= get_global_properties().parameters.maximum_block_size );
	   }

	   push_block( pending_block, skip );

	   return pending_block;
	} FC_CAPTURE_AND_RETHROW( (witness_id) ) }


pop_block
-------------------------
* Removes the most recent block from the database and undoes any changes it made.
	 
.. code-block:: cpp 

	void database::pop_block()
	{ try {
	   _pending_tx_session.reset();
	   auto head_id = head_block_id();
	   optional<signed_block> head_block = fetch_block_by_id( head_id );
	   GRAPHENE_ASSERT( head_block.valid(), pop_empty_chain, "there are no blocks to pop" );

	   _fork_db.pop_block();
	   pop_undo();

	   _popped_tx.insert( _popped_tx.begin(), head_block->transactions.begin(), head_block->transactions.end() );

	} FC_CAPTURE_AND_RETHROW() }


clear_pending
-------------------------

.. code-block:: cpp 

	void database::clear_pending()
	{ try {
	   assert( (_pending_tx.size() == 0) || _pending_tx_session.valid() );
	   _pending_tx.clear();
	   _pending_tx_session.reset();
	} FC_CAPTURE_AND_RETHROW() }


push_applied_operation
-------------------------

.. code-block:: cpp 

	uint32_t database::push_applied_operation( const operation& op )
	{
	   _applied_ops.emplace_back(op);
	   operation_history_object& oh = *(_applied_ops.back());
	   oh.block_num    = _current_block_num;
	   oh.trx_in_block = _current_trx_in_block;
	   oh.op_in_trx    = _current_op_in_trx;
	   oh.virtual_op   = _current_virtual_op++;
	   return _applied_ops.size() - 1;
	}


set_applied_operation_result
------------------------------

.. code-block:: cpp 
	
	void database::set_applied_operation_result( uint32_t op_id, const operation_result& result )
	{
	   assert( op_id < _applied_ops.size() );
	   if( _applied_ops[op_id] )
		  _applied_ops[op_id]->result = result;
	   else
	   {
		  elog( "Could not set operation result (head_block_num=${b})", ("b", head_block_num()) );
	   }
	}


get_applied_operations
-------------------------

.. code-block:: cpp 

	const vector<optional< operation_history_object > >& database::get_applied_operations() const
	{
	   return _applied_ops;
	}

	
	
apply_block	
------------
	
//////////////////// private methods ////////////////////

.. code-block:: cpp 

	void database::apply_block( const signed_block& next_block, uint32_t skip )
	{
	   auto block_num = next_block.block_num();
	   if( _checkpoints.size() && _checkpoints.rbegin()->second != block_id_type() )
	   {
		  auto itr = _checkpoints.find( block_num );
		  if( itr != _checkpoints.end() )
			 FC_ASSERT( next_block.id() == itr->second, "Block did not match checkpoint", ("checkpoint",*itr)("block_id",next_block.id()) );

		  if( _checkpoints.rbegin()->first >= block_num )
			 skip = ~0;// WE CAN SKIP ALMOST EVERYTHING
	   }

	   detail::with_skip_flags( *this, skip, [&]()
	   {
		  _apply_block( next_block );
	   } );
	   return;
	}
	
	void database::_apply_block( const signed_block& next_block )
	{ try {
	   uint32_t next_block_num = next_block.block_num();
	   uint32_t skip = get_node_properties().skip_flags;
	   _applied_ops.clear();

	   FC_ASSERT( (skip & skip_merkle_check) || next_block.transaction_merkle_root == next_block.calculate_merkle_root(), "", ("next_block.transaction_merkle_root",next_block.transaction_merkle_root)("calc",next_block.calculate_merkle_root())("next_block",next_block)("id",next_block.id()) );

	   const witness_object& signing_witness = validate_block_header(skip, next_block);
	   const auto& global_props = get_global_properties();
	   const auto& dynamic_global_props = get_dynamic_global_properties();
	   bool maint_needed = (dynamic_global_props.next_maintenance_time <= next_block.timestamp);

	   _current_block_num    = next_block_num;
	   _current_trx_in_block = 0;

	   _issue_453_affected_assets.clear();

	   for( const auto& trx : next_block.transactions )
	   {
		  /* We do not need to push the undo state for each transaction
		   * because they either all apply and are valid or the
		   * entire block fails to apply.  We only need an "undo" state
		   * for transactions when validating broadcast transactions or
		   * when building a block.
		   */
		  apply_transaction( trx, skip );
		  ++_current_trx_in_block;
	   }

	   const uint32_t missed = update_witness_missed_blocks( next_block );
	   update_global_dynamic_data( next_block, missed );
	   update_signing_witness(signing_witness, next_block);
	   update_last_irreversible_block();

	   // Are we at the maintenance interval?
	   if( maint_needed )
		  perform_chain_maintenance(next_block, global_props);

	   create_block_summary(next_block);
	   clear_expired_transactions();
	   clear_expired_proposals();
	   clear_expired_orders();
	   update_expired_feeds();       // this will update expired feeds and some core exchange rates
	   update_core_exchange_rates(); // this will update remaining core exchange rates
	   update_withdraw_permissions();

	   // n.b., update_maintenance_flag() happens this late
	   // because get_slot_time() / get_slot_at_time() is needed above
	   // TODO:  figure out if we could collapse this function into
	   // update_global_dynamic_data() as perhaps these methods only need
	   // to be called for header validation?
	   update_maintenance_flag( maint_needed );
	   update_witness_schedule();
	   if( !_node_property_object.debug_updates.empty() )
		  apply_debug_updates();

	   // notify observers that the block has been applied
	   notify_applied_block( next_block ); //emit
	   _applied_ops.clear();

	   notify_changed_objects();
	} FC_CAPTURE_AND_RETHROW( (next_block.block_num()) )  }



apply_transaction
-------------------------

.. code-block:: cpp 

	processed_transaction database::apply_transaction(const signed_transaction& trx, uint32_t skip)
	{
	   processed_transaction result;
	   detail::with_skip_flags( *this, skip, [&]()
	   {
		  result = _apply_transaction(trx);
	   });
	   return result;
	}

.. code-block:: cpp 

	processed_transaction database::_apply_transaction(const signed_transaction& trx)
	{ try {
	   uint32_t skip = get_node_properties().skip_flags;

	   if( true || !(skip&skip_validate) )   /* issue #505 explains why this skip_flag is disabled */
		  trx.validate();

	   auto& trx_idx = get_mutable_index_type<transaction_index>();
	   const chain_id_type& chain_id = get_chain_id();
	   transaction_id_type trx_id;
	   if( !(skip & skip_transaction_dupe_check) )
	   {
		  trx_id = trx.id();
		  FC_ASSERT( trx_idx.indices().get<by_trx_id>().find(trx_id) == trx_idx.indices().get<by_trx_id>().end() );
	   }
	   transaction_evaluation_state eval_state(this);
	   const chain_parameters& chain_parameters = get_global_properties().parameters;
	   eval_state._trx = &trx;

	   if( !(skip & (skip_transaction_signatures | skip_authority_check) ) )
	   {
		  auto get_active = [&]( account_id_type id ) { return &id(*this).active; };
		  auto get_owner  = [&]( account_id_type id ) { return &id(*this).owner;  };
		  trx.verify_authority( chain_id, get_active, get_owner, get_global_properties().parameters.max_authority_depth );
	   }

	   //Skip all manner of expiration and TaPoS checking if we're on block 1; It's impossible that the transaction is
	   //expired, and TaPoS makes no sense as no blocks exist.
	   if( BOOST_LIKELY(head_block_num() > 0) )
	   {
		  if( !(skip & skip_tapos_check) )
		  {
			 const auto& tapos_block_summary = block_summary_id_type( trx.ref_block_num )(*this);

			 //Verify TaPoS block summary has correct ID prefix, and that this block's time is not past the expiration
			 FC_ASSERT( trx.ref_block_prefix == tapos_block_summary.block_id._hash[1] );
		  }

		  fc::time_point_sec now = head_block_time();

		  FC_ASSERT( trx.expiration <= now + chain_parameters.maximum_time_until_expiration, "",
					 ("trx.expiration",trx.expiration)("now",now)("max_til_exp",chain_parameters.maximum_time_until_expiration));
		  FC_ASSERT( now <= trx.expiration, "", ("now",now)("trx.exp",trx.expiration) );
	   }

	   //Insert transaction into unique transactions database.
	   if( !(skip & skip_transaction_dupe_check) )
	   {
		  create<transaction_object>([&trx_id,&trx](transaction_object& transaction) {
			 transaction.trx_id = trx_id;
			 transaction.trx = trx;
		  });
	   }

	   eval_state.operation_results.reserve(trx.operations.size());

	   //Finally process the operations
	   processed_transaction ptrx(trx);
	   _current_op_in_trx = 0;
	   for( const auto& op : ptrx.operations )
	   {
		  eval_state.operation_results.emplace_back(apply_operation(eval_state, op));
		  ++_current_op_in_trx;
	   }
	   ptrx.operation_results = std::move(eval_state.operation_results);

	   if( head_block_time() < HARDFORK_CORE_1040_TIME ) // TODO totally remove this code block after hard fork
	   {
		  //Make sure the temp account has no non-zero balances
		  const auto& index = get_index_type<account_balance_index>().indices().get<by_account_asset>();
		  auto range = index.equal_range( boost::make_tuple( GRAPHENE_TEMP_ACCOUNT ) );
		  std::for_each(range.first, range.second, [](const account_balance_object& b) { FC_ASSERT(b.balance == 0); });
	   }

	   return ptrx;
	} FC_CAPTURE_AND_RETHROW( (trx) ) }


apply_operation
-------------------------

.. code-block:: cpp 

	operation_result database::apply_operation(transaction_evaluation_state& eval_state, const operation& op)
	{ try {
	   int i_which = op.which();
	   uint64_t u_which = uint64_t( i_which );
	   FC_ASSERT( i_which >= 0, "Negative operation tag in operation ${op}", ("op",op) );
	   FC_ASSERT( u_which < _operation_evaluators.size(), "No registered evaluator for operation ${op}", ("op",op) );
	   unique_ptr<op_evaluator>& eval = _operation_evaluators[ u_which ];
	   FC_ASSERT( eval, "No registered evaluator for operation ${op}", ("op",op) );
	   auto op_id = push_applied_operation( op );
	   auto result = eval->evaluate( eval_state, op, true );
	   set_applied_operation_result( op_id, result );
	   return result;
	} FC_CAPTURE_AND_RETHROW( (op) ) }


validate_block_header
-------------------------

.. code-block:: cpp 

	const witness_object& database::validate_block_header( uint32_t skip, const signed_block& next_block )const
	{
	   FC_ASSERT( head_block_id() == next_block.previous, "", ("head_block_id",head_block_id())("next.prev",next_block.previous) );
	   FC_ASSERT( head_block_time() < next_block.timestamp, "", ("head_block_time",head_block_time())("next",next_block.timestamp)("blocknum",next_block.block_num()) );
	   const witness_object& witness = next_block.witness(*this);

	   if( !(skip&skip_witness_signature) ) 
		  FC_ASSERT( next_block.validate_signee( witness.signing_key ) );

	   if( !(skip&skip_witness_schedule_check) )
	   {
		  uint32_t slot_num = get_slot_at_time( next_block.timestamp );
		  FC_ASSERT( slot_num > 0 );

		  witness_id_type scheduled_witness = get_scheduled_witness( slot_num );

		  FC_ASSERT( next_block.witness == scheduled_witness, "Witness produced block at wrong time",
					 ("block witness",next_block.witness)("scheduled",scheduled_witness)("slot_num",slot_num) );
	   }

	   return witness;
	}



create_block_summary
-------------------------

.. code-block:: cpp 

	void database::create_block_summary(const signed_block& next_block)
	{
	   block_summary_id_type sid(next_block.block_num() & 0xffff );
	   modify( sid(*this), [&](block_summary_object& p) {
			 p.block_id = next_block.id();
	   });
	}


add_checkpoints
-------------------------

.. code-block:: cpp 

	void database::add_checkpoints( const flat_map<uint32_t,block_id_type>& checkpts )
	{
	   for( const auto& i : checkpts )
		  _checkpoints[i.first] = i.second;
	}


before_last_checkpoint
-------------------------

.. code-block:: cpp 

	bool database::before_last_checkpoint()const
	{
	   return (_checkpoints.size() > 0) && (_checkpoints.rbegin()->first >= head_block_num());
	}




-------------------

|

db_debug.cpp
================================
* This method dumps the state of the blockchain in a semi-human readable form for the purpose of tracking down funds and mismatches in currency allocation 


debug_dump
-------------------------

.. code-block:: cpp 

	void database::debug_dump()
	{
	   const auto& db = *this;
	   const asset_dynamic_data_object& core_asset_data = db.get_core_asset().dynamic_asset_data_id(db);

	   const auto& balance_index = db.get_index_type<account_balance_index>().indices();
	   const auto& statistics_index = db.get_index_type<account_stats_index>().indices();
	   const auto& bids = db.get_index_type<collateral_bid_index>().indices();
	   const auto& settle_index = db.get_index_type<force_settlement_index>().indices();
	   map<asset_id_type,share_type> total_balances;
	   map<asset_id_type,share_type> total_debts;
	   share_type core_in_orders;
	   share_type reported_core_in_orders;

	   for( const account_balance_object& a : balance_index )
	   {
		//  idump(("balance")(a));
		  total_balances[a.asset_type] += a.balance;
	   }
	   for( const force_settlement_object& s : settle_index )
	   {
		  total_balances[s.balance.asset_id] += s.balance.amount;
	   }
	   for( const vesting_balance_object& vbo : db.get_index_type< vesting_balance_index >().indices() )
		  total_balances[ vbo.balance.asset_id ] += vbo.balance.amount;
	   for( const fba_accumulator_object& fba : db.get_index_type< simple_index< fba_accumulator_object > >() )
		  total_balances[ asset_id_type() ] += fba.accumulated_fba_fees;
	   for( const account_statistics_object& s : statistics_index )
	   {
		//  idump(("statistics")(s));
		  reported_core_in_orders += s.total_core_in_orders;
	   }
	   for( const collateral_bid_object& b : bids )
		  total_balances[b.inv_swan_price.base.asset_id] += b.inv_swan_price.base.amount;
	   for( const limit_order_object& o : db.get_index_type<limit_order_index>().indices() )
	   {
	 //     idump(("limit_order")(o));
		  auto for_sale = o.amount_for_sale();
		  if( for_sale.asset_id == asset_id_type() ) core_in_orders += for_sale.amount;
		  total_balances[for_sale.asset_id] += for_sale.amount;
	   }
	   for( const call_order_object& o : db.get_index_type<call_order_index>().indices() )
	   {
	//      idump(("call_order")(o));
		  auto col = o.get_collateral();
		  if( col.asset_id == asset_id_type() ) core_in_orders += col.amount;
		  total_balances[col.asset_id] += col.amount;
		  total_debts[o.get_debt().asset_id] += o.get_debt().amount;
	   }
	   for( const asset_object& asset_obj : db.get_index_type<asset_index>().indices() )
	   {
		  total_balances[asset_obj.id] += asset_obj.dynamic_asset_data_id(db).accumulated_fees;
		  total_balances[asset_id_type()] += asset_obj.dynamic_asset_data_id(db).fee_pool;
	//      edump((total_balances[asset_obj.id])(asset_obj.dynamic_asset_data_id(db).current_supply ) );
	   }

	   if( total_balances[asset_id_type()].value != core_asset_data.current_supply.value )
	   {
		  FC_THROW( "computed balance of CORE mismatch",
					("computed value",total_balances[asset_id_type()].value)
					("current supply",core_asset_data.current_supply.value) );
	   }


	   /*
	   const auto& vbidx = db.get_index_type<simple_index<vesting_balance_object>>();
	   for( const auto& s : vbidx )
	   {
	//      idump(("vesting_balance")(s));
	   }
	   */
	}


debug_apply_update	
-------------------------

.. code-block:: cpp 

	void debug_apply_update( database& db, const fc::variant_object& vo )
	{
	   static const uint8_t
		  db_action_nil = 0,
		  db_action_create = 1,
		  db_action_write = 2,
		  db_action_update = 3,
		  db_action_delete = 4;

	   // "_action" : "create"   object must not exist, unspecified fields take defaults
	   // "_action" : "write"    object may exist, is replaced entirely, unspecified fields take defaults
	   // "_action" : "update"   object must exist, unspecified fields don't change
	   // "_action" : "delete"   object must exist, will be deleted

	   // if _action is unspecified:
	   // - delete if object contains only ID field
	   // - otherwise, write

	   object_id_type oid;
	   uint8_t action = db_action_nil;
	   auto it_id = vo.find("id");
	   FC_ASSERT( it_id != vo.end() );

	   from_variant( it_id->value(), oid );
	   action = ( vo.size() == 1 ) ? db_action_delete : db_action_write;

	   from_variant( vo["id"], oid );
	   if( vo.size() == 1 )
		  action = db_action_delete;
	   auto it_action = vo.find("_action" );
	   if( it_action != vo.end() )
	   {
		  const std::string& str_action = it_action->value().get_string();
		  if( str_action == "create" )
			 action = db_action_create;
		  else if( str_action == "write" )
			 action = db_action_write;
		  else if( str_action == "update" )
			 action = db_action_update;
		  else if( str_action == "delete" )
			 action = db_action_delete;
	   }

	   auto& idx = db.get_index( oid );

	   switch( action )
	   {
		  case db_action_create:
			 FC_ASSERT( false );
			 break;
		  case db_action_write:
			 db.modify( db.get_object( oid ), [&]( object& obj )
			 {
				idx.object_default( obj );
				idx.object_from_variant( vo, obj, GRAPHENE_MAX_NESTED_OBJECTS );
			 } );
			 break;
		  case db_action_update:
			 db.modify( db.get_object( oid ), [&]( object& obj )
			 {
				idx.object_from_variant( vo, obj, GRAPHENE_MAX_NESTED_OBJECTS );
			 } );
			 break;
		  case db_action_delete:
			 db.remove( db.get_object( oid ) );
			 break;
		  default:
			 FC_ASSERT( false );
	   }
	}


apply_debug_updates
-------------------------

.. code-block:: cpp 

	void database::apply_debug_updates()
	{
	   block_id_type head_id = head_block_id();
	   auto it = _node_property_object.debug_updates.find( head_id );
	   if( it == _node_property_object.debug_updates.end() )
		  return;
	   for( const fc::variant_object& update : it->second )
		  debug_apply_update( *this, update );
	}

	
debug_update
-------------------------

.. code-block:: cpp 

	void database::debug_update( const fc::variant_object& update )
	{
	   block_id_type head_id = head_block_id();
	   auto it = _node_property_object.debug_updates.find( head_id );
	   if( it == _node_property_object.debug_updates.end() )
		  it = _node_property_object.debug_updates.emplace( head_id, std::vector< fc::variant_object >() ).first;
	   it->second.emplace_back( update );

	   optional<signed_block> head_block = fetch_block_by_id( head_id );
	   FC_ASSERT( head_block.valid() );

	   // What the last block does has been changed by adding to node_property_object, so we have to re-apply it
	   pop_block();
	   push_block( *head_block );
	}



-------------------

|

db_getter.cpp
================================



----------------------------------
	
.. code-block:: cpp 
	
	const asset_object& database::get_core_asset() const
	{
	   return *_p_core_asset_obj;
	}

	const asset_dynamic_data_object& database::get_core_dynamic_data() const
	{
	   return *_p_core_dynamic_data_obj;
	}

	const global_property_object& database::get_global_properties()const
	{
	   return *_p_global_prop_obj;
	}

	const chain_property_object& database::get_chain_properties()const
	{
	   return *_p_chain_property_obj;
	}

	const dynamic_global_property_object& database::get_dynamic_global_properties() const
	{
	   return *_p_dyn_global_prop_obj;
	}

	const fee_schedule&  database::current_fee_schedule()const
	{
	   return get_global_properties().parameters.current_fees;
	}

	time_point_sec database::head_block_time()const
	{
	   return get_dynamic_global_properties().time;
	}

	uint32_t database::head_block_num()const
	{
	   return get_dynamic_global_properties().head_block_number;
	}

	block_id_type database::head_block_id()const
	{
	   return get_dynamic_global_properties().head_block_id;
	}

	decltype( chain_parameters::block_interval ) database::block_interval( )const
	{
	   return get_global_properties().parameters.block_interval;
	}

	const chain_id_type& database::get_chain_id( )const
	{
	   return get_chain_properties().chain_id;
	}

	const node_property_object& database::get_node_properties()const
	{
	   return _node_property_object;
	}

	node_property_object& database::node_properties()
	{
	   return _node_property_object;
	}

	uint32_t database::last_non_undoable_block_num() const
	{
	   return head_block_num() - _undo_db.size();
	}

	const account_statistics_object& database::get_account_stats_by_owner( account_id_type owner )const
	{
	   auto& idx = get_index_type<account_stats_index>().indices().get<by_owner>();
	   auto itr = idx.find( owner );
	   FC_ASSERT( itr != idx.end(), "Can not find account statistics object for owner ${a}", ("a",owner) );
	   return *itr;
	}

	const witness_schedule_object& database::get_witness_schedule_object()const
	{
	   return *_p_witness_schedule_obj;
	}




-------------------

|

db_init.cpp
================================

* C++ requires that static class variables declared and initialized in headers must also have a definition in a single source file, else linker errors will occur [1].
* The purpose of this source file is to collect such definitions in a single place.
* [1] http://stackoverflow.com/questions/8016780/undefined-reference-to-static-constexpr-char


.. code-block:: cpp 

	const uint8_t account_object::space_id;
	const uint8_t account_object::type_id;

	const uint8_t asset_object::space_id;
	const uint8_t asset_object::type_id;

	const uint8_t block_summary_object::space_id;
	const uint8_t block_summary_object::type_id;

	const uint8_t call_order_object::space_id;
	const uint8_t call_order_object::type_id;

	const uint8_t committee_member_object::space_id;
	const uint8_t committee_member_object::type_id;

	const uint8_t force_settlement_object::space_id;
	const uint8_t force_settlement_object::type_id;

	const uint8_t global_property_object::space_id;
	const uint8_t global_property_object::type_id;

	const uint8_t limit_order_object::space_id;
	const uint8_t limit_order_object::type_id;

	const uint8_t operation_history_object::space_id;
	const uint8_t operation_history_object::type_id;

	const uint8_t proposal_object::space_id;
	const uint8_t proposal_object::type_id;

	const uint8_t transaction_object::space_id;
	const uint8_t transaction_object::type_id;

	const uint8_t vesting_balance_object::space_id;
	const uint8_t vesting_balance_object::type_id;

	const uint8_t withdraw_permission_object::space_id;
	const uint8_t withdraw_permission_object::type_id;

	const uint8_t witness_object::space_id;
	const uint8_t witness_object::type_id;

	const uint8_t worker_object::space_id;
	const uint8_t worker_object::type_id;


initialize_evaluators	
----------------------------------
	
.. code-block:: cpp 
	
	void database::initialize_evaluators()
	{
	   _operation_evaluators.resize(255);
	   register_evaluator<account_create_evaluator>();
	   register_evaluator<account_update_evaluator>();
	   register_evaluator<account_upgrade_evaluator>();
	   register_evaluator<account_whitelist_evaluator>();
	   register_evaluator<committee_member_create_evaluator>();
	   register_evaluator<committee_member_update_evaluator>();
	   register_evaluator<committee_member_update_global_parameters_evaluator>();
	   register_evaluator<custom_evaluator>();
	   register_evaluator<asset_create_evaluator>();
	   register_evaluator<asset_issue_evaluator>();
	   register_evaluator<asset_reserve_evaluator>();
	   register_evaluator<asset_update_evaluator>();
	   register_evaluator<asset_update_bitasset_evaluator>();
	   register_evaluator<asset_update_feed_producers_evaluator>();
	   register_evaluator<asset_settle_evaluator>();
	   register_evaluator<asset_global_settle_evaluator>();
	   register_evaluator<assert_evaluator>();
	   register_evaluator<limit_order_create_evaluator>();
	   register_evaluator<limit_order_cancel_evaluator>();
	   register_evaluator<call_order_update_evaluator>();
	   register_evaluator<bid_collateral_evaluator>();
	   register_evaluator<transfer_evaluator>();
	   register_evaluator<override_transfer_evaluator>();
	   register_evaluator<asset_fund_fee_pool_evaluator>();
	   register_evaluator<asset_publish_feeds_evaluator>();
	   register_evaluator<proposal_create_evaluator>();
	   register_evaluator<proposal_update_evaluator>();
	   register_evaluator<proposal_delete_evaluator>();
	   register_evaluator<vesting_balance_create_evaluator>();
	   register_evaluator<vesting_balance_withdraw_evaluator>();
	   register_evaluator<witness_create_evaluator>();
	   register_evaluator<witness_update_evaluator>();
	   register_evaluator<withdraw_permission_create_evaluator>();
	   register_evaluator<withdraw_permission_claim_evaluator>();
	   register_evaluator<withdraw_permission_update_evaluator>();
	   register_evaluator<withdraw_permission_delete_evaluator>();
	   register_evaluator<worker_create_evaluator>();
	   register_evaluator<balance_claim_evaluator>();
	   register_evaluator<transfer_to_blind_evaluator>();
	   register_evaluator<transfer_from_blind_evaluator>();
	   register_evaluator<blind_transfer_evaluator>();
	   register_evaluator<asset_claim_fees_evaluator>();
	   register_evaluator<asset_update_issuer_evaluator>();
	   register_evaluator<asset_claim_pool_evaluator>();
	}



initialize_indexes
----------------------------------
	
.. code-block:: cpp 
	
	void database::initialize_indexes()
	{
	   reset_indexes();
	   _undo_db.set_max_size( GRAPHENE_MIN_UNDO_HISTORY );

	   //Protocol object indexes
	   add_index< primary_index<asset_index> >();
	   add_index< primary_index<force_settlement_index> >();

	   auto acnt_index = add_index< primary_index<account_index> >();
	   acnt_index->add_secondary_index<account_member_index>();
	   acnt_index->add_secondary_index<account_referrer_index>();

	   add_index< primary_index<committee_member_index> >();
	   add_index< primary_index<witness_index> >();
	   add_index< primary_index<limit_order_index > >();
	   add_index< primary_index<call_order_index > >();

	   auto prop_index = add_index< primary_index<proposal_index > >();
	   prop_index->add_secondary_index<required_approval_index>();

	   add_index< primary_index<withdraw_permission_index > >();
	   add_index< primary_index<vesting_balance_index> >();
	   add_index< primary_index<worker_index> >();
	   add_index< primary_index<balance_index> >();
	   add_index< primary_index<blinded_balance_index> >();

	   //Implementation object indexes
	   add_index< primary_index<transaction_index                             > >();
	   add_index< primary_index<account_balance_index                         > >();
	   add_index< primary_index<asset_bitasset_data_index                     > >();
	   add_index< primary_index<simple_index<global_property_object          >> >();
	   add_index< primary_index<simple_index<dynamic_global_property_object  >> >();
	   add_index< primary_index<account_stats_index                           > >();
	   add_index< primary_index<simple_index<asset_dynamic_data_object       >> >();
	   add_index< primary_index<simple_index<block_summary_object            >> >();
	   add_index< primary_index<simple_index<chain_property_object          > > >();
	   add_index< primary_index<simple_index<witness_schedule_object        > > >();
	   add_index< primary_index<simple_index<budget_record_object           > > >();
	   add_index< primary_index< special_authority_index                      > >();
	   add_index< primary_index< buyback_index                                > >();
	   add_index< primary_index<collateral_bid_index                          > >();

	   add_index< primary_index< simple_index< fba_accumulator_object       > > >();
	}

	
init_genesis	
----------------------------------
	
.. code-block:: cpp 
	
	void database::init_genesis(const genesis_state_type& genesis_state)
	{ try {
	   FC_ASSERT( genesis_state.initial_timestamp != time_point_sec(), "Must initialize genesis timestamp." );
	   FC_ASSERT( genesis_state.initial_timestamp.sec_since_epoch() % GRAPHENE_DEFAULT_BLOCK_INTERVAL == 0,
				  "Genesis timestamp must be divisible by GRAPHENE_DEFAULT_BLOCK_INTERVAL." );
	   FC_ASSERT(genesis_state.initial_witness_candidates.size() > 0,
				 "Cannot start a chain with zero witnesses.");
	   FC_ASSERT(genesis_state.initial_active_witnesses <= genesis_state.initial_witness_candidates.size(),
				 "initial_active_witnesses is larger than the number of candidate witnesses.");

	   _undo_db.disable();
	   struct auth_inhibitor {
		  auth_inhibitor(database& db) : db(db), old_flags(db.node_properties().skip_flags)
		  { db.node_properties().skip_flags |= skip_authority_check; }
		  ~auth_inhibitor()
		  { db.node_properties().skip_flags = old_flags; }
	   private:
		  database& db;
		  uint32_t old_flags;
	   } inhibitor(*this);

	   transaction_evaluation_state genesis_eval_state(this);

	   // Create blockchain accounts
	   fc::ecc::private_key null_private_key = fc::ecc::private_key::regenerate(fc::sha256::hash(string("null_key")));
	   create<account_balance_object>([](account_balance_object& b) {
		  b.balance = GRAPHENE_MAX_SHARE_SUPPLY;
	   });
	   const account_object& committee_account =
		  create<account_object>( [&](account_object& n) {
			 n.membership_expiration_date = time_point_sec::maximum();
			 n.network_fee_percentage = GRAPHENE_DEFAULT_NETWORK_PERCENT_OF_FEE;
			 n.lifetime_referrer_fee_percentage = GRAPHENE_100_PERCENT - GRAPHENE_DEFAULT_NETWORK_PERCENT_OF_FEE;
			 n.owner.weight_threshold = 1;
			 n.active.weight_threshold = 1;
			 n.name = "committee-account";
			 n.statistics = create<account_statistics_object>( [&n](account_statistics_object& s){
							   s.owner = n.id;
							   s.name = n.name;
							   s.core_in_balance = GRAPHENE_MAX_SHARE_SUPPLY;
							}).id;
		  });
	   FC_ASSERT(committee_account.get_id() == GRAPHENE_COMMITTEE_ACCOUNT);
	   FC_ASSERT(create<account_object>([this](account_object& a) {
		   a.name = "witness-account";
		   a.statistics = create<account_statistics_object>([&a](account_statistics_object& s){
							 s.owner = a.id;
							 s.name = a.name;
						  }).id;
		   a.owner.weight_threshold = 1;
		   a.active.weight_threshold = 1;
		   a.registrar = a.lifetime_referrer = a.referrer = GRAPHENE_WITNESS_ACCOUNT;
		   a.membership_expiration_date = time_point_sec::maximum();
		   a.network_fee_percentage = GRAPHENE_DEFAULT_NETWORK_PERCENT_OF_FEE;
		   a.lifetime_referrer_fee_percentage = GRAPHENE_100_PERCENT - GRAPHENE_DEFAULT_NETWORK_PERCENT_OF_FEE;
	   }).get_id() == GRAPHENE_WITNESS_ACCOUNT);
	   FC_ASSERT(create<account_object>([this](account_object& a) {
		   a.name = "relaxed-committee-account";
		   a.statistics = create<account_statistics_object>([&a](account_statistics_object& s){
							 s.owner = a.id;
							 s.name = a.name;
						  }).id;
		   a.owner.weight_threshold = 1;
		   a.active.weight_threshold = 1;
		   a.registrar = a.lifetime_referrer = a.referrer = GRAPHENE_RELAXED_COMMITTEE_ACCOUNT;
		   a.membership_expiration_date = time_point_sec::maximum();
		   a.network_fee_percentage = GRAPHENE_DEFAULT_NETWORK_PERCENT_OF_FEE;
		   a.lifetime_referrer_fee_percentage = GRAPHENE_100_PERCENT - GRAPHENE_DEFAULT_NETWORK_PERCENT_OF_FEE;
	   }).get_id() == GRAPHENE_RELAXED_COMMITTEE_ACCOUNT);
	   FC_ASSERT(create<account_object>([this](account_object& a) {
		   a.name = "null-account";
		   a.statistics = create<account_statistics_object>([&a](account_statistics_object& s){
							 s.owner = a.id;
							 s.name = a.name;
						  }).id;
		   a.owner.weight_threshold = 1;
		   a.active.weight_threshold = 1;
		   a.registrar = a.lifetime_referrer = a.referrer = GRAPHENE_NULL_ACCOUNT;
		   a.membership_expiration_date = time_point_sec::maximum();
		   a.network_fee_percentage = 0;
		   a.lifetime_referrer_fee_percentage = GRAPHENE_100_PERCENT;
	   }).get_id() == GRAPHENE_NULL_ACCOUNT);
	   FC_ASSERT(create<account_object>([this](account_object& a) {
		   a.name = "temp-account";
		   a.statistics = create<account_statistics_object>([&a](account_statistics_object& s){
							 s.owner = a.id;
							 s.name = a.name;
						  }).id;
		   a.owner.weight_threshold = 0;
		   a.active.weight_threshold = 0;
		   a.registrar = a.lifetime_referrer = a.referrer = GRAPHENE_TEMP_ACCOUNT;
		   a.membership_expiration_date = time_point_sec::maximum();
		   a.network_fee_percentage = GRAPHENE_DEFAULT_NETWORK_PERCENT_OF_FEE;
		   a.lifetime_referrer_fee_percentage = GRAPHENE_100_PERCENT - GRAPHENE_DEFAULT_NETWORK_PERCENT_OF_FEE;
	   }).get_id() == GRAPHENE_TEMP_ACCOUNT);
	   FC_ASSERT(create<account_object>([this](account_object& a) {
		   a.name = "proxy-to-self";
		   a.statistics = create<account_statistics_object>([&a](account_statistics_object& s){
							 s.owner = a.id;
							 s.name = a.name;
						  }).id;
		   a.owner.weight_threshold = 1;
		   a.active.weight_threshold = 1;
		   a.registrar = a.lifetime_referrer = a.referrer = GRAPHENE_NULL_ACCOUNT;
		   a.membership_expiration_date = time_point_sec::maximum();
		   a.network_fee_percentage = 0;
		   a.lifetime_referrer_fee_percentage = GRAPHENE_100_PERCENT;
	   }).get_id() == GRAPHENE_PROXY_TO_SELF_ACCOUNT);

	   // Create more special accounts
	   while( true )
	   {
		  uint64_t id = get_index<account_object>().get_next_id().instance();
		  if( id >= genesis_state.immutable_parameters.num_special_accounts )
			 break;
		  const account_object& acct = create<account_object>([this,id](account_object& a) {
			  a.name = "special-account-" + std::to_string(id);
			  a.statistics = create<account_statistics_object>([&a](account_statistics_object& s){
								s.owner = a.id;
								s.name = a.name;
							 }).id;
			  a.owner.weight_threshold = 1;
			  a.active.weight_threshold = 1;
			  a.registrar = a.lifetime_referrer = a.referrer = account_id_type(id);
			  a.membership_expiration_date = time_point_sec::maximum();
			  a.network_fee_percentage = GRAPHENE_DEFAULT_NETWORK_PERCENT_OF_FEE;
			  a.lifetime_referrer_fee_percentage = GRAPHENE_100_PERCENT - GRAPHENE_DEFAULT_NETWORK_PERCENT_OF_FEE;
		  });
		  FC_ASSERT( acct.get_id() == account_id_type(id) );
		  remove( acct );
	   }

	   // Create core asset
	   const asset_dynamic_data_object& dyn_asset =
		  create<asset_dynamic_data_object>([](asset_dynamic_data_object& a) {
			 a.current_supply = GRAPHENE_MAX_SHARE_SUPPLY;
		  });
	   const asset_object& core_asset =
		 create<asset_object>( [&genesis_state,&dyn_asset]( asset_object& a ) {
			 a.symbol = GRAPHENE_SYMBOL;
			 a.options.max_supply = genesis_state.max_core_supply;
			 a.precision = GRAPHENE_BLOCKCHAIN_PRECISION_DIGITS;
			 a.options.flags = 0;
			 a.options.issuer_permissions = 0;
			 a.issuer = GRAPHENE_NULL_ACCOUNT;
			 a.options.core_exchange_rate.base.amount = 1;
			 a.options.core_exchange_rate.base.asset_id = asset_id_type(0);
			 a.options.core_exchange_rate.quote.amount = 1;
			 a.options.core_exchange_rate.quote.asset_id = asset_id_type(0);
			 a.dynamic_asset_data_id = dyn_asset.id;
		  });
	   FC_ASSERT( dyn_asset.id == asset_dynamic_data_id_type() );
	   FC_ASSERT( asset_id_type(core_asset.id) == asset().asset_id );
	   FC_ASSERT( get_balance(account_id_type(), asset_id_type()) == asset(dyn_asset.current_supply) );
	   _p_core_asset_obj = &core_asset;
	   _p_core_dynamic_data_obj = &dyn_asset;
	   // Create more special assets
	   while( true )
	   {
		  uint64_t id = get_index<asset_object>().get_next_id().instance();
		  if( id >= genesis_state.immutable_parameters.num_special_assets )
			 break;
		  const asset_dynamic_data_object& dyn_asset =
			 create<asset_dynamic_data_object>([](asset_dynamic_data_object& a) {
				a.current_supply = 0;
			 });
		  const asset_object& asset_obj = create<asset_object>( [id,&dyn_asset]( asset_object& a ) {
			 a.symbol = "SPECIAL" + std::to_string( id );
			 a.options.max_supply = 0;
			 a.precision = GRAPHENE_BLOCKCHAIN_PRECISION_DIGITS;
			 a.options.flags = 0;
			 a.options.issuer_permissions = 0;
			 a.issuer = GRAPHENE_NULL_ACCOUNT;
			 a.options.core_exchange_rate.base.amount = 1;
			 a.options.core_exchange_rate.base.asset_id = asset_id_type(0);
			 a.options.core_exchange_rate.quote.amount = 1;
			 a.options.core_exchange_rate.quote.asset_id = asset_id_type(0);
			 a.dynamic_asset_data_id = dyn_asset.id;
		  });
		  FC_ASSERT( asset_obj.get_id() == asset_id_type(id) );
		  remove( asset_obj );
	   }

	   chain_id_type chain_id = genesis_state.compute_chain_id();

	   // Create global properties
	   _p_global_prop_obj = & create<global_property_object>([&genesis_state](global_property_object& p) {
		   p.parameters = genesis_state.initial_parameters;
		   // Set fees to zero initially, so that genesis initialization needs not pay them
		   // We'll fix it at the end of the function
		   p.parameters.current_fees->zero_all_fees();

	   });
	   _p_dyn_global_prop_obj = & create<dynamic_global_property_object>([&genesis_state](dynamic_global_property_object& p) {
		  p.time = genesis_state.initial_timestamp;
		  p.dynamic_flags = 0;
		  p.witness_budget = 0;
		  p.recent_slots_filled = fc::uint128::max_value();
	   });

	   FC_ASSERT( (genesis_state.immutable_parameters.min_witness_count & 1) == 1, "min_witness_count must be odd" );
	   FC_ASSERT( (genesis_state.immutable_parameters.min_committee_member_count & 1) == 1, "min_committee_member_count must be odd" );

	   _p_chain_property_obj = & create<chain_property_object>([chain_id,&genesis_state](chain_property_object& p)
	   {
		  p.chain_id = chain_id;
		  p.immutable_parameters = genesis_state.immutable_parameters;
	   } );
	   for (uint32_t i = 0; i <= 0x10000; i++)
		  create<block_summary_object>( [&]( block_summary_object&) {});

	   // Create initial accounts
	   for( const auto& account : genesis_state.initial_accounts )
	   {
		  account_create_operation cop;
		  cop.name = account.name;
		  cop.registrar = GRAPHENE_TEMP_ACCOUNT;
		  cop.owner = authority(1, account.owner_key, 1);
		  if( account.active_key == public_key_type() )
		  {
			 cop.active = cop.owner;
			 cop.options.memo_key = account.owner_key;
		  }
		  else
		  {
			 cop.active = authority(1, account.active_key, 1);
			 cop.options.memo_key = account.active_key;
		  }
		  account_id_type account_id(apply_operation(genesis_eval_state, cop).get<object_id_type>());

		  if( account.is_lifetime_member )
		  {
			  account_upgrade_operation op;
			  op.account_to_upgrade = account_id;
			  op.upgrade_to_lifetime_member = true;
			  apply_operation(genesis_eval_state, op);
		  }
	   }

	   // Helper function to get account ID by name
	   const auto& accounts_by_name = get_index_type<account_index>().indices().get<by_name>();
	   auto get_account_id = [&accounts_by_name](const string& name) {
		  auto itr = accounts_by_name.find(name);
		  FC_ASSERT(itr != accounts_by_name.end(),
					"Unable to find account '${acct}'. Did you forget to add a record for it to initial_accounts?",
					("acct", name));
		  return itr->get_id();
	   };

	   // Helper function to get asset ID by symbol
	   const auto& assets_by_symbol = get_index_type<asset_index>().indices().get<by_symbol>();
	   const auto get_asset_id = [&assets_by_symbol](const string& symbol) {
		  auto itr = assets_by_symbol.find(symbol);
		  FC_ASSERT(itr != assets_by_symbol.end(),
					"Unable to find asset '${sym}'. Did you forget to add a record for it to initial_assets?",
					("sym", symbol));
		  return itr->get_id();
	   };

	   map<asset_id_type, share_type> total_supplies;
	   map<asset_id_type, share_type> total_debts;

	   // Create initial assets
	   for( const genesis_state_type::initial_asset_type& asset : genesis_state.initial_assets )
	   {
		  asset_id_type new_asset_id = get_index_type<asset_index>().get_next_id();
		  total_supplies[ new_asset_id ] = 0;

		  asset_dynamic_data_id_type dynamic_data_id;
		  optional<asset_bitasset_data_id_type> bitasset_data_id;
		  if( asset.is_bitasset )
		  {
			 int collateral_holder_number = 0;
			 total_debts[ new_asset_id ] = 0;
			 for( const auto& collateral_rec : asset.collateral_records )
			 {
				account_create_operation cop;
				cop.name = asset.symbol + "-collateral-holder-" + std::to_string(collateral_holder_number);
				boost::algorithm::to_lower(cop.name);
				cop.registrar = GRAPHENE_TEMP_ACCOUNT;
				cop.owner = authority(1, collateral_rec.owner, 1);
				cop.active = cop.owner;
				account_id_type owner_account_id = apply_operation(genesis_eval_state, cop).get<object_id_type>();

				modify( owner_account_id(*this).statistics(*this), [&collateral_rec]( account_statistics_object& o ) {
				   o.total_core_in_orders = collateral_rec.collateral;
				});

				create<call_order_object>([&](call_order_object& c) {
				   c.borrower = owner_account_id;
				   c.collateral = collateral_rec.collateral;
				   c.debt = collateral_rec.debt;
				   c.call_price = price::call_price(chain::asset(c.debt, new_asset_id),
													chain::asset(c.collateral, core_asset.id),
													GRAPHENE_DEFAULT_MAINTENANCE_COLLATERAL_RATIO);
				});

				total_supplies[ asset_id_type(0) ] += collateral_rec.collateral;
				total_debts[ new_asset_id ] += collateral_rec.debt;
				++collateral_holder_number;
			 }

			 bitasset_data_id = create<asset_bitasset_data_object>([&core_asset,new_asset_id](asset_bitasset_data_object& b) {
				b.options.short_backing_asset = core_asset.id;
				b.options.minimum_feeds = GRAPHENE_DEFAULT_MINIMUM_FEEDS;
				b.asset_id = new_asset_id;
			 }).id;
		  }

		  dynamic_data_id = create<asset_dynamic_data_object>([&asset](asset_dynamic_data_object& d) {
			 d.accumulated_fees = asset.accumulated_fees;
		  }).id;

		  total_supplies[ new_asset_id ] += asset.accumulated_fees;

		  create<asset_object>([&](asset_object& a) {
			 a.symbol = asset.symbol;
			 a.options.description = asset.description;
			 a.precision = asset.precision;
			 string issuer_name = asset.issuer_name;
			 a.issuer = get_account_id(issuer_name);
			 a.options.max_supply = asset.max_supply;
			 a.options.flags = witness_fed_asset;
			 a.options.issuer_permissions = charge_market_fee | override_authority | white_list | transfer_restricted | disable_confidential |
										   ( asset.is_bitasset ? disable_force_settle | global_settle | witness_fed_asset | committee_fed_asset : 0 );
			 a.dynamic_asset_data_id = dynamic_data_id;
			 a.bitasset_data_id = bitasset_data_id;
		  });
	   }

	   // Create initial balances
	   share_type total_allocation;
	   for( const auto& handout : genesis_state.initial_balances )
	   {
		  const auto asset_id = get_asset_id(handout.asset_symbol);
		  create<balance_object>([&handout,total_allocation,asset_id](balance_object& b) {
			 b.balance = asset(handout.amount, asset_id);
			 b.owner = handout.owner;
		  });

		  total_supplies[ asset_id ] += handout.amount;
	   }

	   // Create initial vesting balances
	   for( const genesis_state_type::initial_vesting_balance_type& vest : genesis_state.initial_vesting_balances )
	   {
		  const auto asset_id = get_asset_id(vest.asset_symbol);
		  create<balance_object>([&](balance_object& b) {
			 b.owner = vest.owner;
			 b.balance = asset(vest.amount, asset_id);

			 linear_vesting_policy policy;
			 policy.begin_timestamp = vest.begin_timestamp;
			 policy.vesting_cliff_seconds = 0;
			 policy.vesting_duration_seconds = vest.vesting_duration_seconds;
			 policy.begin_balance = vest.begin_balance;

			 b.vesting_policy = std::move(policy);
		  });

		  total_supplies[ asset_id ] += vest.amount;
	   }

	   if( total_supplies[ asset_id_type(0) ] > 0 )
	   {
		   adjust_balance(GRAPHENE_COMMITTEE_ACCOUNT, -get_balance(GRAPHENE_COMMITTEE_ACCOUNT,{}));
	   }
	   else
	   {
		   total_supplies[ asset_id_type(0) ] = GRAPHENE_MAX_SHARE_SUPPLY;
	   }

	   const auto& idx = get_index_type<asset_index>().indices().get<by_symbol>();
	   auto it = idx.begin();
	   bool has_imbalanced_assets = false;

	   while( it != idx.end() )
	   {
		  if( it->bitasset_data_id.valid() )
		  {
			 auto supply_itr = total_supplies.find( it->id );
			 auto debt_itr = total_debts.find( it->id );
			 FC_ASSERT( supply_itr != total_supplies.end() );
			 FC_ASSERT( debt_itr != total_debts.end() );
			 if( supply_itr->second != debt_itr->second )
			 {
				has_imbalanced_assets = true;
				elog( "Genesis for asset ${aname} is not balanced\n"
					  "   Debt is ${debt}\n"
					  "   Supply is ${supply}\n",
					  ("aname", it->symbol)
					  ("debt", debt_itr->second)
					  ("supply", supply_itr->second)
					);
			 }
		  }
		  ++it;
	   }
	   FC_ASSERT( !has_imbalanced_assets );

	   // Save tallied supplies
	   for( const auto& item : total_supplies )
	   {
		   const auto asset_id = item.first;
		   const auto total_supply = item.second;

		   modify( get( asset_id ), [ & ]( asset_object& asset ) {
			   modify( get( asset.dynamic_asset_data_id ), [ & ]( asset_dynamic_data_object& asset_data ) {
				   asset_data.current_supply = total_supply;
			   } );
		   } );
	   }

	   // Create special witness account
	   const witness_object& wit = create<witness_object>([&](witness_object& w) {});
	   FC_ASSERT( wit.id == GRAPHENE_NULL_WITNESS );
	   remove(wit);

	   // Create initial witnesses
	   std::for_each(genesis_state.initial_witness_candidates.begin(), genesis_state.initial_witness_candidates.end(),
					 [&](const genesis_state_type::initial_witness_type& witness) {
		  witness_create_operation op;
		  op.witness_account = get_account_id(witness.owner_name);
		  op.block_signing_key = witness.block_signing_key;
		  apply_operation(genesis_eval_state, op);
	   });

	   // Create initial committee members
	   std::for_each(genesis_state.initial_committee_candidates.begin(), genesis_state.initial_committee_candidates.end(),
					 [&](const genesis_state_type::initial_committee_member_type& member) {
		  committee_member_create_operation op;
		  op.committee_member_account = get_account_id(member.owner_name);
		  apply_operation(genesis_eval_state, op);
	   });

	   // Create initial workers
	   std::for_each(genesis_state.initial_worker_candidates.begin(), genesis_state.initial_worker_candidates.end(),
					  [&](const genesis_state_type::initial_worker_type& worker)
	   {
		   worker_create_operation op;
		   op.owner = get_account_id(worker.owner_name);
		   op.work_begin_date = genesis_state.initial_timestamp;
		   op.work_end_date = time_point_sec::maximum();
		   op.daily_pay = worker.daily_pay;
		   op.name = "Genesis-Worker-" + worker.owner_name;
		   op.initializer = vesting_balance_worker_initializer{uint16_t(0)};

		   apply_operation(genesis_eval_state, std::move(op));
	   });

	   // Set active witnesses
		   modify(get_global_properties(), [&genesis_state](global_property_object& p) {
			  for( uint32_t i = 1; i <= genesis_state.initial_active_witnesses; ++i )
			  {
				 p.active_witnesses.insert(witness_id_type(i));
			  }
		   });

		   // Enable fees
		   modify(get_global_properties(), [&genesis_state](global_property_object& p) {
			  p.parameters.current_fees = genesis_state.initial_parameters.current_fees;
		   });

		   // Create witness scheduler
		   _p_witness_schedule_obj = & create<witness_schedule_object>([this]( witness_schedule_object& wso )
		   {
			  for( const witness_id_type& wid : get_global_properties().active_witnesses )
				 wso.current_shuffled_witnesses.push_back( wid );
		   });

		   // Create FBA counters
		   create<fba_accumulator_object>([&]( fba_accumulator_object& acc )
		   {
			  FC_ASSERT( acc.id == fba_accumulator_id_type( fba_accumulator_id_transfer_to_blind ) );
			  acc.accumulated_fba_fees = 0;
			#ifdef GRAPHENE_FBA_STEALTH_DESIGNATED_ASSET
			  acc.designated_asset = GRAPHENE_FBA_STEALTH_DESIGNATED_ASSET;
			#endif
		   });

		   create<fba_accumulator_object>([&]( fba_accumulator_object& acc )
		   {
			  FC_ASSERT( acc.id == fba_accumulator_id_type( fba_accumulator_id_blind_transfer ) );
			  acc.accumulated_fba_fees = 0;
			#ifdef GRAPHENE_FBA_STEALTH_DESIGNATED_ASSET
			  acc.designated_asset = GRAPHENE_FBA_STEALTH_DESIGNATED_ASSET;
			#endif
		   });

		   create<fba_accumulator_object>([&]( fba_accumulator_object& acc )
		   {
			  FC_ASSERT( acc.id == fba_accumulator_id_type( fba_accumulator_id_transfer_from_blind ) );
			  acc.accumulated_fba_fees = 0;
			#ifdef GRAPHENE_FBA_STEALTH_DESIGNATED_ASSET
			  acc.designated_asset = GRAPHENE_FBA_STEALTH_DESIGNATED_ASSET;
		   #endif
		   });

		   FC_ASSERT( get_index<fba_accumulator_object>().get_next_id() == fba_accumulator_id_type( fba_accumulator_id_count ) );

		   //debug_dump();

		   _undo_db.enable();
		} }
      

-------------------

|

db_maint.cpp
================================

sort_votable_objects
-------------------------------

.. code-block:: cpp 

	template<class Index>
	vector<std::reference_wrapper<const typename Index::object_type>> database::sort_votable_objects(size_t count) const
	{
	   using ObjectType = typename Index::object_type;
	   const auto& all_objects = get_index_type<Index>().indices();
	   count = std::min(count, all_objects.size());
	   vector<std::reference_wrapper<const ObjectType>> refs;
	   refs.reserve(all_objects.size());
	   std::transform(all_objects.begin(), all_objects.end(),
					  std::back_inserter(refs),
					  [](const ObjectType& o) { return std::cref(o); });
	   std::partial_sort(refs.begin(), refs.begin() + count, refs.end(),
					   [this](const ObjectType& a, const ObjectType& b)->bool {
		  share_type oa_vote = _vote_tally_buffer[a.vote_id];
		  share_type ob_vote = _vote_tally_buffer[b.vote_id];
		  if( oa_vote != ob_vote )
			 return oa_vote > ob_vote;
		  return a.vote_id < b.vote_id;
	   });

	   refs.resize(count, refs.front());
	   return refs;
	}
	
	
perform_account_maintenance	
-------------------------------

.. code-block:: cpp 

	template<class Type>
	void database::perform_account_maintenance(Type tally_helper)
	{
	   const auto& bal_idx = get_index_type< account_balance_index >().indices().get< by_maintenance_flag >();
	   if( bal_idx.begin() != bal_idx.end() )
	   {
		  auto bal_itr = bal_idx.rbegin();
		  while( bal_itr->maintenance_flag )
		  {
			 const account_balance_object& bal_obj = *bal_itr;

			 modify( get_account_stats_by_owner( bal_obj.owner ), [&bal_obj](account_statistics_object& aso) {
				aso.core_in_balance = bal_obj.balance;
			 });

			 modify( bal_obj, []( account_balance_object& abo ) {
				abo.maintenance_flag = false;
			 });

			 bal_itr = bal_idx.rbegin();
		  }
	   }

	   const auto& stats_idx = get_index_type< account_stats_index >().indices().get< by_maintenance_seq >();
	   auto stats_itr = stats_idx.lower_bound( true );

	   while( stats_itr != stats_idx.end() )
	   {
		  const account_statistics_object& acc_stat = *stats_itr;
		  const account_object& acc_obj = acc_stat.owner( *this );
		  ++stats_itr;

		  if( acc_stat.has_some_core_voting() )
			 tally_helper( acc_obj, acc_stat );

		  if( acc_stat.has_pending_fees() )
			 acc_stat.process_fees( acc_obj, *this );
	   }

	}
	
worker_pay_visitor	
-------------------------------
* A visitor for @ref worker_type which calls pay_worker on the worker within

.. code-block:: cpp 

	struct worker_pay_visitor
	{
	   private:
		  share_type pay;
		  database& db;

	   public:
		  worker_pay_visitor(share_type pay, database& db)
			 : pay(pay), db(db) {}

		  typedef void result_type;
		  template<typename W>
		  void operator()(W& worker)const
		  {
			 worker.pay_worker(pay, db);
		  }
	};
	
update_worker_votes	
-------------------------------

.. code-block:: cpp 

	void database::update_worker_votes()
	{
	   const auto& idx = get_index_type<worker_index>().indices().get<by_account>();
	   auto itr = idx.begin();
	   auto itr_end = idx.end();
	   bool allow_negative_votes = (head_block_time() < HARDFORK_607_TIME);
	   while( itr != itr_end )
	   {
		  modify( *itr, [this,allow_negative_votes]( worker_object& obj )
		  {
			 obj.total_votes_for = _vote_tally_buffer[obj.vote_for];
			 obj.total_votes_against = allow_negative_votes ? _vote_tally_buffer[obj.vote_against] : 0;
		  });
		  ++itr;
	   }
	}
	

pay_workers
-------------------------------

.. code-block:: cpp 

	void database::pay_workers( share_type& budget )
	{
	   const auto head_time = head_block_time();
	//   ilog("Processing payroll! Available budget is ${b}", ("b", budget));
	   vector<std::reference_wrapper<const worker_object>> active_workers;
	   // TODO optimization: add by_expiration index to avoid iterating through all objects
	   get_index_type<worker_index>().inspect_all_objects([head_time, &active_workers](const object& o) {
		  const worker_object& w = static_cast<const worker_object&>(o);
		  if( w.is_active(head_time) && w.approving_stake() > 0 )
			 active_workers.emplace_back(w);
	   });

	   // worker with more votes is preferred
	   // if two workers exactly tie for votes, worker with lower ID is preferred
	   std::sort(active_workers.begin(), active_workers.end(), [](const worker_object& wa, const worker_object& wb) {
		  share_type wa_vote = wa.approving_stake();
		  share_type wb_vote = wb.approving_stake();
		  if( wa_vote != wb_vote )
			 return wa_vote > wb_vote;
		  return wa.id < wb.id;
	   });

	   const auto last_budget_time = get_dynamic_global_properties().last_budget_time;
	   const auto passed_time_ms = head_time - last_budget_time;
	   const auto passed_time_count = passed_time_ms.count();
	   const auto day_count = fc::days(1).count();
	   for( uint32_t i = 0; i < active_workers.size() && budget > 0; ++i )
	   {
		  const worker_object& active_worker = active_workers[i];
		  share_type requested_pay = active_worker.daily_pay;

		  // Note: if there is a good chance that passed_time_count == day_count,
		  //       for better performance, can avoid the 128 bit calculation by adding a check.
		  //       Since it's not the case on BitShares mainnet, we're not using a check here.
		  fc::uint128 pay(requested_pay.value);
		  pay *= passed_time_count;
		  pay /= day_count;
		  requested_pay = pay.to_uint64();

		  share_type actual_pay = std::min(budget, requested_pay);
		  //ilog(" ==> Paying ${a} to worker ${w}", ("w", active_worker.id)("a", actual_pay));
		  modify(active_worker, [&](worker_object& w) {
			 w.worker.visit(worker_pay_visitor(actual_pay, *this));
		  });

		  budget -= actual_pay;
	   }
	}
	
update_active_witnesses	
-------------------------------

.. code-block:: cpp 

	void database::update_active_witnesses()
	{ try {
	   assert( _witness_count_histogram_buffer.size() > 0 );
	   share_type stake_target = (_total_voting_stake-_witness_count_histogram_buffer[0]) / 2;

	   /// accounts that vote for 0 or 1 witness do not get to express an opinion on
	   /// the number of witnesses to have (they abstain and are non-voting accounts)

	   share_type stake_tally = 0; 

	   size_t witness_count = 0;
	   if( stake_target > 0 )
	   {
		  while( (witness_count < _witness_count_histogram_buffer.size() - 1)
				 && (stake_tally <= stake_target) )
		  {
			 stake_tally += _witness_count_histogram_buffer[++witness_count];
		  }
	   }

	   const chain_property_object& cpo = get_chain_properties();

	   witness_count = std::max( witness_count*2+1, (size_t)cpo.immutable_parameters.min_witness_count );
	   auto wits = sort_votable_objects<witness_index>( witness_count );

	   const global_property_object& gpo = get_global_properties();

	   auto update_witness_total_votes = [this]( const witness_object& wit ) {
		  modify( wit, [this]( witness_object& obj )
		  {
			 obj.total_votes = _vote_tally_buffer[obj.vote_id];
		  });
	   };

	   if( _track_standby_votes )
	   {
		  const auto& all_witnesses = get_index_type<witness_index>().indices();
		  for( const witness_object& wit : all_witnesses )
		  {
			 update_witness_total_votes( wit );
		  }
	   }
	   else
	   {
		  for( const witness_object& wit : wits )
		  {
			 update_witness_total_votes( wit );
		  }
	   }

	   // Update witness authority
	   modify( get(GRAPHENE_WITNESS_ACCOUNT), [this,&wits]( account_object& a )
	   {
		  if( head_block_time() < HARDFORK_533_TIME )
		  {
			 uint64_t total_votes = 0;
			 map<account_id_type, uint64_t> weights;
			 a.active.weight_threshold = 0;
			 a.active.clear();

			 for( const witness_object& wit : wits )
			 {
				weights.emplace(wit.witness_account, _vote_tally_buffer[wit.vote_id]);
				total_votes += _vote_tally_buffer[wit.vote_id];
			 }

			 // total_votes is 64 bits. Subtract the number of leading low bits from 64 to get the number of useful bits,
			 // then I want to keep the most significant 16 bits of what's left.
			 int8_t bits_to_drop = std::max(int(boost::multiprecision::detail::find_msb(total_votes)) - 15, 0);
			 for( const auto& weight : weights )
			 {
				// Ensure that everyone has at least one vote. Zero weights aren't allowed.
				uint16_t votes = std::max((weight.second >> bits_to_drop), uint64_t(1) );
				a.active.account_auths[weight.first] += votes;
				a.active.weight_threshold += votes;
			 }

			 a.active.weight_threshold /= 2;
			 a.active.weight_threshold += 1;
		  }
		  else
		  {
			 vote_counter vc;
			 for( const witness_object& wit : wits )
				vc.add( wit.witness_account, _vote_tally_buffer[wit.vote_id] );
			 vc.finish( a.active );
		  }
	   } );

	   modify( gpo, [&wits]( global_property_object& gp )
	   {
		  gp.active_witnesses.clear();
		  gp.active_witnesses.reserve(wits.size());
		  std::transform(wits.begin(), wits.end(),
						 std::inserter(gp.active_witnesses, gp.active_witnesses.end()),
						 [](const witness_object& w) {
			 return w.id;
		  });
	   });

	} FC_CAPTURE_AND_RETHROW() }
	
	
update_active_committee_members	
-------------------------------

.. code-block:: cpp 

	void database::update_active_committee_members()
	{ try {
	   assert( _committee_count_histogram_buffer.size() > 0 );
	   share_type stake_target = (_total_voting_stake-_committee_count_histogram_buffer[0]) / 2;

	   /// accounts that vote for 0 or 1 witness do not get to express an opinion on
	   /// the number of witnesses to have (they abstain and are non-voting accounts)
	   uint64_t stake_tally = 0; // _committee_count_histogram_buffer[0];
	   size_t committee_member_count = 0;
	   if( stake_target > 0 )
	   {
		  while( (committee_member_count < _committee_count_histogram_buffer.size() - 1)
				 && (stake_tally <= stake_target) )
		  {
			 stake_tally += _committee_count_histogram_buffer[++committee_member_count];
		  }
	   }

	   const chain_property_object& cpo = get_chain_properties();

	   committee_member_count = std::max( committee_member_count*2+1, (size_t)cpo.immutable_parameters.min_committee_member_count );
	   auto committee_members = sort_votable_objects<committee_member_index>( committee_member_count );

	   auto update_committee_member_total_votes = [this]( const committee_member_object& cm ) {
		  modify( cm, [this]( committee_member_object& obj )
		  {
			 obj.total_votes = _vote_tally_buffer[obj.vote_id];
		  });
	   };

	   if( _track_standby_votes )
	   {
		  const auto& all_committee_members = get_index_type<committee_member_index>().indices();
		  for( const committee_member_object& cm : all_committee_members )
		  {
			 update_committee_member_total_votes( cm );
		  }
	   }
	   else
	   {
		  for( const committee_member_object& cm : committee_members )
		  {
			 update_committee_member_total_votes( cm );
		  }
	   }

	   // Update committee authorities
	   if( !committee_members.empty() )
	   {
		  const account_object& committee_account = get(GRAPHENE_COMMITTEE_ACCOUNT);
		  modify( committee_account, [this,&committee_members](account_object& a)
		  {
			 if( head_block_time() < HARDFORK_533_TIME )
			 {
				uint64_t total_votes = 0;
				map<account_id_type, uint64_t> weights;
				a.active.weight_threshold = 0;
				a.active.clear();

				for( const committee_member_object& cm : committee_members )
				{
				   weights.emplace( cm.committee_member_account, _vote_tally_buffer[cm.vote_id] );
				   total_votes += _vote_tally_buffer[cm.vote_id];
				}

				// total_votes is 64 bits. Subtract the number of leading low bits from 64 to get the number of useful bits,
				// then I want to keep the most significant 16 bits of what's left.
				int8_t bits_to_drop = std::max(int(boost::multiprecision::detail::find_msb(total_votes)) - 15, 0);
				for( const auto& weight : weights )
				{
				   // Ensure that everyone has at least one vote. Zero weights aren't allowed.
				   uint16_t votes = std::max((weight.second >> bits_to_drop), uint64_t(1) );
				   a.active.account_auths[weight.first] += votes;
				   a.active.weight_threshold += votes;
				}

				a.active.weight_threshold /= 2;
				a.active.weight_threshold += 1;
			 }
			 else
			 {
				vote_counter vc;
				for( const committee_member_object& cm : committee_members )
				   vc.add( cm.committee_member_account, _vote_tally_buffer[cm.vote_id] );
				vc.finish( a.active );
			 }
		  });
		  modify( get(GRAPHENE_RELAXED_COMMITTEE_ACCOUNT), [&committee_account](account_object& a)
		  {
			 a.active = committee_account.active;
		  });
	   }
	   modify( get_global_properties(), [&committee_members](global_property_object& gp)
	   {
		  gp.active_committee_members.clear();
		  std::transform(committee_members.begin(), committee_members.end(),
						 std::inserter(gp.active_committee_members, gp.active_committee_members.begin()),
						 [](const committee_member_object& d) { return d.id; });
	   });
	} FC_CAPTURE_AND_RETHROW() }
	

initialize_budget_record	
-------------------------------

.. code-block:: cpp 

	void database::initialize_budget_record( fc::time_point_sec now, budget_record& rec )const
	{
	   const dynamic_global_property_object& dpo = get_dynamic_global_properties();
	   const asset_object& core = get_core_asset();
	   const asset_dynamic_data_object& core_dd = get_core_dynamic_data();

	   rec.from_initial_reserve = core.reserved(*this);
	   rec.from_accumulated_fees = core_dd.accumulated_fees;
	   rec.from_unused_witness_budget = dpo.witness_budget;

	   if(    (dpo.last_budget_time == fc::time_point_sec())
		   || (now <= dpo.last_budget_time) )
	   {
		  rec.time_since_last_budget = 0;
		  return;
	   }

	   int64_t dt = (now - dpo.last_budget_time).to_seconds();
	   rec.time_since_last_budget = uint64_t( dt );

	   // We'll consider accumulated_fees to be reserved at the BEGINNING
	   // of the maintenance interval.  However, for speed we only
	   // call modify() on the asset_dynamic_data_object once at the
	   // end of the maintenance interval.  Thus the accumulated_fees
	   // are available for the budget at this point, but not included
	   // in core.reserved().
	   share_type reserve = rec.from_initial_reserve + core_dd.accumulated_fees;
	   // Similarly, we consider leftover witness_budget to be burned
	   // at the BEGINNING of the maintenance interval.
	   reserve += dpo.witness_budget;

	   fc::uint128_t budget_u128 = reserve.value;
	   budget_u128 *= uint64_t(dt);
	   budget_u128 *= GRAPHENE_CORE_ASSET_CYCLE_RATE;
	   //round up to the nearest satoshi -- this is necessary to ensure
	   //   there isn't an "untouchable" reserve, and we will eventually
	   //   be able to use the entire reserve
	   budget_u128 += ((uint64_t(1) << GRAPHENE_CORE_ASSET_CYCLE_RATE_BITS) - 1);
	   budget_u128 >>= GRAPHENE_CORE_ASSET_CYCLE_RATE_BITS;
	   if( budget_u128 < reserve.value )
		  rec.total_budget = share_type(budget_u128.to_uint64());
	   else
		  rec.total_budget = reserve;

	   return;
	}

	
process_budget	
-------------------------------
* Update the budget for witnesses and workers.

.. code-block:: cpp 

	void database::process_budget()
	{
	   try
	   {
		  const global_property_object& gpo = get_global_properties();
		  const dynamic_global_property_object& dpo = get_dynamic_global_properties();
		  const asset_dynamic_data_object& core = get_core_dynamic_data();
		  fc::time_point_sec now = head_block_time();

		  int64_t time_to_maint = (dpo.next_maintenance_time - now).to_seconds();
		  //
		  // The code that generates the next maintenance time should
		  //    only produce a result in the future.  If this assert
		  //    fails, then the next maintenance time algorithm is buggy.
		  //
		  assert( time_to_maint > 0 );
		  //
		  // Code for setting chain parameters should validate
		  //    block_interval > 0 (as well as the humans proposing /
		  //    voting on changes to block interval).
		  //
		  assert( gpo.parameters.block_interval > 0 );
		  uint64_t blocks_to_maint = (uint64_t(time_to_maint) + gpo.parameters.block_interval - 1) / gpo.parameters.block_interval;

		  // blocks_to_maint > 0 because time_to_maint > 0,
		  // which means numerator is at least equal to block_interval

		  budget_record rec;
		  initialize_budget_record( now, rec );
		  share_type available_funds = rec.total_budget;

		  share_type witness_budget = gpo.parameters.witness_pay_per_block.value * blocks_to_maint;
		  rec.requested_witness_budget = witness_budget;
		  witness_budget = std::min(witness_budget, available_funds);
		  rec.witness_budget = witness_budget;
		  available_funds -= witness_budget;

		  fc::uint128_t worker_budget_u128 = gpo.parameters.worker_budget_per_day.value;
		  worker_budget_u128 *= uint64_t(time_to_maint);
		  worker_budget_u128 /= 60*60*24;

		  share_type worker_budget;
		  if( worker_budget_u128 >= available_funds.value )
			 worker_budget = available_funds;
		  else
			 worker_budget = worker_budget_u128.to_uint64();
		  rec.worker_budget = worker_budget;
		  available_funds -= worker_budget;

		  share_type leftover_worker_funds = worker_budget;
		  pay_workers(leftover_worker_funds);
		  rec.leftover_worker_funds = leftover_worker_funds;
		  available_funds += leftover_worker_funds;

		  rec.supply_delta = rec.witness_budget
			 + rec.worker_budget
			 - rec.leftover_worker_funds
			 - rec.from_accumulated_fees
			 - rec.from_unused_witness_budget;

		  modify(core, [&]( asset_dynamic_data_object& _core )
		  {
			 _core.current_supply = (_core.current_supply + rec.supply_delta );

			 assert( rec.supply_delta ==
									   witness_budget
									 + worker_budget
									 - leftover_worker_funds
									 - _core.accumulated_fees
									 - dpo.witness_budget
									);
			 _core.accumulated_fees = 0;
		  });

		  modify(dpo, [&]( dynamic_global_property_object& _dpo )
		  {
			 // Since initial witness_budget was rolled into
			 // available_funds, we replace it with witness_budget
			 // instead of adding it.
			 _dpo.witness_budget = witness_budget;
			 _dpo.last_budget_time = now;
		  });

		  create< budget_record_object >( [&]( budget_record_object& _rec )
		  {
			 _rec.time = head_block_time();
			 _rec.record = rec;
		  });

		  // available_funds is money we could spend, but don't want to.
		  // we simply let it evaporate back into the reserve.
	   }
	   FC_CAPTURE_AND_RETHROW()
	}
	
	
visit_special_authorities	
-------------------------------

.. code-block:: cpp 

	template< typename Visitor >
	void visit_special_authorities( const database& db, Visitor visit )
	{
	   const auto& sa_idx = db.get_index_type< special_authority_index >().indices().get<by_id>();

	   for( const special_authority_object& sao : sa_idx )
	   {
		  const account_object& acct = sao.account(db);
		  if( acct.owner_special_authority.which() != special_authority::tag< no_special_authority >::value )
		  {
			 visit( acct, true, acct.owner_special_authority );
		  }
		  if( acct.active_special_authority.which() != special_authority::tag< no_special_authority >::value )
		  {
			 visit( acct, false, acct.active_special_authority );
		  }
	   }
	}
	
	
update_top_n_authorities	
-------------------------------

.. code-block:: cpp 

	void update_top_n_authorities( database& db )
	{
	   visit_special_authorities( db,
	   [&]( const account_object& acct, bool is_owner, const special_authority& auth )
	   {
		  if( auth.which() == special_authority::tag< top_holders_special_authority >::value )
		  {
			 // use index to grab the top N holders of the asset and vote_counter to obtain the weights

			 const top_holders_special_authority& tha = auth.get< top_holders_special_authority >();
			 vote_counter vc;
			 const auto& bal_idx = db.get_index_type< account_balance_index >().indices().get< by_asset_balance >();
			 uint8_t num_needed = tha.num_top_holders;
			 if( num_needed == 0 )
				return;

			 // find accounts
			 const auto range = bal_idx.equal_range( boost::make_tuple( tha.asset ) );
			 for( const account_balance_object& bal : boost::make_iterator_range( range.first, range.second ) )
			 {
				 assert( bal.asset_type == tha.asset );
				 if( bal.owner == acct.id )
					continue;
				 vc.add( bal.owner, bal.balance.value );
				 --num_needed;
				 if( num_needed == 0 )
					break;
			 }

			 db.modify( acct, [&]( account_object& a )
			 {
				vc.finish( is_owner ? a.owner : a.active );
				if( !vc.is_empty() )
				   a.top_n_control_flags |= (is_owner ? account_object::top_n_control_owner : account_object::top_n_control_active);
			 } );
		  }
	   } );
	}
	
	
split_fba_balance	
-------------------------------

.. code-block:: cpp 

	void split_fba_balance(
	   database& db,
	   uint64_t fba_id,
	   uint16_t network_pct,
	   uint16_t designated_asset_buyback_pct,
	   uint16_t designated_asset_issuer_pct
	)
	{
	   FC_ASSERT( uint32_t(network_pct) + uint32_t(designated_asset_buyback_pct) + uint32_t(designated_asset_issuer_pct) == GRAPHENE_100_PERCENT );
	   const fba_accumulator_object& fba = fba_accumulator_id_type( fba_id )(db);
	   if( fba.accumulated_fba_fees == 0 )
		  return;

	   const asset_dynamic_data_object& core_dd = db.get_core_dynamic_data();

	   if( !fba.is_configured(db) )
	   {
		  ilog( "${n} core given to network at block ${b} due to non-configured FBA", ("n", fba.accumulated_fba_fees)("b", db.head_block_time()) );
		  db.modify( core_dd, [&]( asset_dynamic_data_object& _core_dd )
		  {
			 _core_dd.current_supply -= fba.accumulated_fba_fees;
		  } );
		  db.modify( fba, [&]( fba_accumulator_object& _fba )
		  {
			 _fba.accumulated_fba_fees = 0;
		  } );
		  return;
	   }

	   fc::uint128_t buyback_amount_128 = fba.accumulated_fba_fees.value;
	   buyback_amount_128 *= designated_asset_buyback_pct;
	   buyback_amount_128 /= GRAPHENE_100_PERCENT;
	   share_type buyback_amount = buyback_amount_128.to_uint64();

	   fc::uint128_t issuer_amount_128 = fba.accumulated_fba_fees.value;
	   issuer_amount_128 *= designated_asset_issuer_pct;
	   issuer_amount_128 /= GRAPHENE_100_PERCENT;
	   share_type issuer_amount = issuer_amount_128.to_uint64();

	   // this assert should never fail
	   FC_ASSERT( buyback_amount + issuer_amount <= fba.accumulated_fba_fees );

	   share_type network_amount = fba.accumulated_fba_fees - (buyback_amount + issuer_amount);

	   const asset_object& designated_asset = (*fba.designated_asset)(db);

	   if( network_amount != 0 )
	   {
		  db.modify( core_dd, [&]( asset_dynamic_data_object& _core_dd )
		  {
			 _core_dd.current_supply -= network_amount;
		  } );
	   }

	   fba_distribute_operation vop;
	   vop.account_id = *designated_asset.buyback_account;
	   vop.fba_id = fba.id;
	   vop.amount = buyback_amount;
	   if( vop.amount != 0 )
	   {
		  db.adjust_balance( *designated_asset.buyback_account, asset(buyback_amount) );
		  db.push_applied_operation(vop);
	   }

	   vop.account_id = designated_asset.issuer;
	   vop.fba_id = fba.id;
	   vop.amount = issuer_amount;
	   if( vop.amount != 0 )
	   {
		  db.adjust_balance( designated_asset.issuer, asset(issuer_amount) );
		  db.push_applied_operation(vop);
	   }

	   db.modify( fba, [&]( fba_accumulator_object& _fba )
	   {
		  _fba.accumulated_fba_fees = 0;
	   } );
	}

	
distribute_fba_balances	
-------------------------------

.. code-block:: cpp 

	void distribute_fba_balances( database& db )
	{
	   split_fba_balance( db, fba_accumulator_id_transfer_to_blind  , 20*GRAPHENE_1_PERCENT, 60*GRAPHENE_1_PERCENT, 20*GRAPHENE_1_PERCENT );
	   split_fba_balance( db, fba_accumulator_id_blind_transfer     , 20*GRAPHENE_1_PERCENT, 60*GRAPHENE_1_PERCENT, 20*GRAPHENE_1_PERCENT );
	   split_fba_balance( db, fba_accumulator_id_transfer_from_blind, 20*GRAPHENE_1_PERCENT, 60*GRAPHENE_1_PERCENT, 20*GRAPHENE_1_PERCENT );
	}
	
create_buyback_orders	
-------------------------------

.. code-block:: cpp 

	void create_buyback_orders( database& db )
	{
	   const auto& bbo_idx = db.get_index_type< buyback_index >().indices().get<by_id>();
	   const auto& bal_idx = db.get_index_type< account_balance_index >().indices().get< by_account_asset >();

	   for( const buyback_object& bbo : bbo_idx )
	   {
		  const asset_object& asset_to_buy = bbo.asset_to_buy(db);
		  assert( asset_to_buy.buyback_account.valid() );

		  const account_object& buyback_account = (*(asset_to_buy.buyback_account))(db);
		  asset_id_type next_asset = asset_id_type();

		  if( !buyback_account.allowed_assets.valid() )
		  {
			 wlog( "skipping buyback account ${b} at block ${n} because allowed_assets does not exist", ("b", buyback_account)("n", db.head_block_num()) );
			 continue;
		  }

		  while( true )
		  {
			 auto it = bal_idx.lower_bound( boost::make_tuple( buyback_account.id, next_asset ) );
			 if( it == bal_idx.end() )
				break;
			 if( it->owner != buyback_account.id )
				break;
			 asset_id_type asset_to_sell = it->asset_type;
			 share_type amount_to_sell = it->balance;
			 next_asset = asset_to_sell + 1;
			 if( asset_to_sell == asset_to_buy.id )
				continue;
			 if( amount_to_sell == 0 )
				continue;
			 if( buyback_account.allowed_assets->find( asset_to_sell ) == buyback_account.allowed_assets->end() )
			 {
				wlog( "buyback account ${b} not selling disallowed holdings of asset ${a} at block ${n}", ("b", buyback_account)("a", asset_to_sell)("n", db.head_block_num()) );
				continue;
			 }

			 try
			 {
				transaction_evaluation_state buyback_context(&db);
				buyback_context.skip_fee_schedule_check = true;

				limit_order_create_operation create_vop;
				create_vop.fee = asset( 0, asset_id_type() );
				create_vop.seller = buyback_account.id;
				create_vop.amount_to_sell = asset( amount_to_sell, asset_to_sell );
				create_vop.min_to_receive = asset( 1, asset_to_buy.id );
				create_vop.expiration = time_point_sec::maximum();
				create_vop.fill_or_kill = false;

				limit_order_id_type order_id = db.apply_operation( buyback_context, create_vop ).get< object_id_type >();

				if( db.find( order_id ) != nullptr )
				{
				   limit_order_cancel_operation cancel_vop;
				   cancel_vop.fee = asset( 0, asset_id_type() );
				   cancel_vop.order = order_id;
				   cancel_vop.fee_paying_account = buyback_account.id;

				   db.apply_operation( buyback_context, cancel_vop );
				}
			 }
			 catch( const fc::exception& e )
			 {
				// we can in fact get here, e.g. if asset issuer of buy/sell asset blacklists/whitelists the buyback account
				wlog( "Skipping buyback processing selling ${as} for ${ab} for buyback account ${b} at block ${n}; exception was ${e}",
					  ("as", asset_to_sell)("ab", asset_to_buy)("b", buyback_account)("n", db.head_block_num())("e", e.to_detail_string()) );
				continue;
			 }
		  }
	   }
	   return;
	}
	
deprecate_annual_members	
-------------------------------

.. code-block:: cpp 

	void deprecate_annual_members( database& db )
	{
	   const auto& account_idx = db.get_index_type<account_index>().indices().get<by_id>();
	   fc::time_point_sec now = db.head_block_time();
	   for( const account_object& acct : account_idx )
	   {
		  try
		  {
			 transaction_evaluation_state upgrade_context(&db);
			 upgrade_context.skip_fee_schedule_check = true;

			 if( acct.is_annual_member( now ) )
			 {
				account_upgrade_operation upgrade_vop;
				upgrade_vop.fee = asset( 0, asset_id_type() );
				upgrade_vop.account_to_upgrade = acct.id;
				upgrade_vop.upgrade_to_lifetime_member = true;
				db.apply_operation( upgrade_context, upgrade_vop );
			 }
		  }
		  catch( const fc::exception& e )
		  {
			 // we can in fact get here, e.g. if asset issuer of buy/sell asset blacklists/whitelists the buyback account
			 wlog( "Skipping annual member deprecate processing for account ${a} (${an}) at block ${n}; exception was ${e}",
				   ("a", acct.id)("an", acct.name)("n", db.head_block_num())("e", e.to_detail_string()) );
			 continue;
		  }
	   }
	   return;
	}
	
process_bids	
-------------------------------

.. code-block:: cpp 

	void database::process_bids( const asset_bitasset_data_object& bad )
	{
	   if( bad.is_prediction_market ) return;
	   if( bad.current_feed.settlement_price.is_null() ) return;

	   asset_id_type to_revive_id = (asset( 0, bad.options.short_backing_asset ) * bad.settlement_price).asset_id;
	   const asset_object& to_revive = to_revive_id( *this );
	   const asset_dynamic_data_object& bdd = to_revive.dynamic_data( *this );

	   const auto& bid_idx = get_index_type< collateral_bid_index >().indices().get<by_price>();
	   const auto start = bid_idx.lower_bound( boost::make_tuple( to_revive_id, price::max( bad.options.short_backing_asset, to_revive_id ), collateral_bid_id_type() ) );

	   share_type covered = 0;
	   auto itr = start;
	   while( covered < bdd.current_supply && itr != bid_idx.end() && itr->inv_swan_price.quote.asset_id == to_revive_id )
	   {
		  const collateral_bid_object& bid = *itr;
		  asset total_collateral = bid.inv_swan_price.quote * bad.settlement_price;
		  total_collateral += bid.inv_swan_price.base;
		  price call_price = price::call_price( bid.inv_swan_price.quote, total_collateral, bad.current_feed.maintenance_collateral_ratio );
		  if( ~call_price >= bad.current_feed.settlement_price ) break;
		  covered += bid.inv_swan_price.quote.amount;
		  ++itr;
	   }
	   if( covered < bdd.current_supply ) return;

	   const auto end = itr;
	   share_type to_cover = bdd.current_supply;
	   share_type remaining_fund = bad.settlement_fund;
	   for( itr = start; itr != end; )
	   {
		  const collateral_bid_object& bid = *itr;
		  ++itr;
		  share_type debt = bid.inv_swan_price.quote.amount;
		  share_type collateral = (bid.inv_swan_price.quote * bad.settlement_price).amount;
		  if( bid.inv_swan_price.quote.amount >= to_cover )
		  {
			 debt = to_cover;
			 collateral = remaining_fund;
		  }
		  to_cover -= debt;
		  remaining_fund -= collateral;
		  execute_bid( bid, debt, collateral, bad.current_feed );
	   }
	   FC_ASSERT( remaining_fund == 0 );
	   FC_ASSERT( to_cover == 0 );

	   _cancel_bids_and_revive_mpa( to_revive, bad );
	}
	
update_and_match_call_orders	
-------------------------------

.. code-block:: cpp 

	void update_and_match_call_orders( database& db )
	{
	   // Update call_price
	   wlog( "Updating all call orders for hardfork core-343 at block ${n}", ("n",db.head_block_num()) );
	   asset_id_type current_asset;
	   const asset_bitasset_data_object* abd = nullptr;
	   // by_collateral index won't change after call_price updated, so it's safe to iterate
	   for( const auto& call_obj : db.get_index_type<call_order_index>().indices().get<by_collateral>() )
	   {
		  if( current_asset != call_obj.debt_type() ) // debt type won't be asset_id_type(), abd will always get initialized
		  {
			 current_asset = call_obj.debt_type();
			 abd = &current_asset(db).bitasset_data(db);
		  }
		  if( !abd || abd->is_prediction_market ) // nothing to do with PM's; check !abd just to be safe
			 continue;
		  db.modify( call_obj, [abd]( call_order_object& call ) {
			 call.call_price  =  price::call_price( call.get_debt(), call.get_collateral(),
													abd->current_feed.maintenance_collateral_ratio );
		  });
	   }
	   // Match call orders
	   const auto& asset_idx = db.get_index_type<asset_index>().indices().get<by_type>();
	   auto itr = asset_idx.lower_bound( true /** market issued */ );
	   while( itr != asset_idx.end() )
	   {
		  const asset_object& a = *itr;
		  ++itr;
		  // be here, next_maintenance_time should have been updated already
		  db.check_call_orders( a, true, false ); // allow black swan, and call orders are taker
	   }
	   wlog( "Done updating all call orders for hardfork core-343 at block ${n}", ("n",db.head_block_num()) );
	}
	
	
process_bitassets	
-------------------------------

.. code-block:: cpp 

	void database::process_bitassets()
	{
	   time_point_sec head_time = head_block_time();
	   uint32_t head_epoch_seconds = head_time.sec_since_epoch();
	   bool after_hf_core_518 = ( head_time >= HARDFORK_CORE_518_TIME ); // clear expired feeds

	   const auto update_bitasset = [this,head_time,head_epoch_seconds,after_hf_core_518]( asset_bitasset_data_object &o )
	   {
		  o.force_settled_volume = 0; // Reset all BitAsset force settlement volumes to zero

		  // clear expired feeds
		  if( after_hf_core_518 )
		  {
			 const auto &asset = get( o.asset_id );
			 auto flags = asset.options.flags;
			 if ( ( flags & ( witness_fed_asset | committee_fed_asset ) ) &&
				  o.options.feed_lifetime_sec < head_epoch_seconds ) // if smartcoin && check overflow
			 {
				fc::time_point_sec calculated = head_time - o.options.feed_lifetime_sec;
				for( auto itr = o.feeds.rbegin(); itr != o.feeds.rend(); ) // loop feeds
				{
				   auto feed_time = itr->second.first;
				   std::advance( itr, 1 );
				   if( feed_time < calculated )
					  o.feeds.erase( itr.base() ); // delete expired feed
				}
			 }
		  }
	   };

	   for( const auto& d : get_index_type<asset_bitasset_data_index>().indices() )
	   {
		  modify( d, update_bitasset );
		  if( d.has_settlement() )
			 process_bids(d);
	   }
	}
	
process_hf_868_890	
-------------------------------

.. code-block:: cpp 

	/******
	 * @brief one-time data process for hard fork core-868-890
	 *
	 * Prior to hardfork 868, switching a bitasset's shorting asset would not reset its
	 * feeds. This method will run at the hardfork time, and erase (or nullify) feeds
	 * that have incorrect backing assets.
	 * https://github.com/bitshares/bitshares-core/issues/868
	 *
	 * Prior to hardfork 890, changing a bitasset's feed expiration time would not
	 * trigger a median feed update. This method will run at the hardfork time, and
	 * correct all median feed data.
	 * https://github.com/bitshares/bitshares-core/issues/890
	 *
	 * @param db the database
	 * @param skip_check_call_orders true if check_call_orders() should not be called
	 */
	// TODO: for better performance, this function can be removed if it actually updated nothing at hf time.
	//       * Also need to update related test cases
	//       * NOTE: the removal can't be applied to testnet
	void process_hf_868_890( database& db, bool skip_check_call_orders )
	{
	   const auto head_time = db.head_block_time();
	   const auto head_num = db.head_block_num();
	   wlog( "Processing hard fork core-868-890 at block ${n}", ("n",head_num) );
	   // for each market issued asset
	   const auto& asset_idx = db.get_index_type<asset_index>().indices().get<by_type>();
	   for( auto asset_itr = asset_idx.lower_bound(true); asset_itr != asset_idx.end(); ++asset_itr )
	   {
		  const auto& current_asset = *asset_itr;
		  // Incorrect witness & committee feeds can simply be removed.
		  // For non-witness-fed and non-committee-fed assets, set incorrect
		  // feeds to price(), since we can't simply remove them. For more information:
		  // https://github.com/bitshares/bitshares-core/pull/832#issuecomment-384112633
		  bool is_witness_or_committee_fed = false;
		  if ( current_asset.options.flags & ( witness_fed_asset | committee_fed_asset ) )
			 is_witness_or_committee_fed = true;

		  // for each feed
		  const asset_bitasset_data_object& bitasset_data = current_asset.bitasset_data(db);
		  // NOTE: We'll only need old_feed if HF343 hasn't rolled out yet
		  auto old_feed = bitasset_data.current_feed;
		  bool feeds_changed = false; // did any feed change
		  auto itr = bitasset_data.feeds.begin();
		  while( itr != bitasset_data.feeds.end() )
		  {
			 // If the feed is invalid
			 if ( itr->second.second.settlement_price.quote.asset_id != bitasset_data.options.short_backing_asset
				   && ( is_witness_or_committee_fed || itr->second.second.settlement_price != price() ) )
			 {
				feeds_changed = true;
				db.modify( bitasset_data, [&itr, is_witness_or_committee_fed]( asset_bitasset_data_object& obj )
				{
				   if( is_witness_or_committee_fed )
				   {
					  // erase the invalid feed
					  itr = obj.feeds.erase(itr);
				   }
				   else
				   {
					  // nullify the invalid feed
					  obj.feeds[itr->first].second.settlement_price = price();
					  ++itr;
				   }
				});
			 }
			 else
			 {
				// Feed is valid. Skip it.
				++itr;
			 }
		  } // end loop of each feed

		  // if any feed was modified, print a warning message
		  if( feeds_changed )
		  {
			 wlog( "Found invalid feed for asset ${asset_sym} (${asset_id}) during hardfork core-868-890",
				   ("asset_sym", current_asset.symbol)("asset_id", current_asset.id) );
		  }

		  // always update the median feed due to https://github.com/bitshares/bitshares-core/issues/890
		  db.modify( bitasset_data, [&head_time]( asset_bitasset_data_object &obj ) {
			 obj.update_median_feeds( head_time );
		  });

		  bool median_changed = ( old_feed.settlement_price != bitasset_data.current_feed.settlement_price );
		  bool median_feed_changed = ( !( old_feed == bitasset_data.current_feed ) );
		  if( median_feed_changed )
		  {
			 wlog( "Median feed for asset ${asset_sym} (${asset_id}) changed during hardfork core-868-890",
				   ("asset_sym", current_asset.symbol)("asset_id", current_asset.id) );
		  }

		  // Note: due to bitshares-core issue #935, the check below (using median_changed) is incorrect.
		  //       However, `skip_check_call_orders` will likely be true in both testnet and mainnet,
		  //         so effectively the incorrect code won't make a difference.
		  //       Additionally, we have code to update all call orders again during hardfork core-935
		  // TODO cleanup after hard fork
		  if( !skip_check_call_orders && median_changed ) // check_call_orders should be called
		  {
			 db.check_call_orders( current_asset );
		  }
		  else if( !skip_check_call_orders && median_feed_changed )
		  {
			 wlog( "Incorrectly skipped check_call_orders for asset ${asset_sym} (${asset_id}) during hardfork core-868-890",
				   ("asset_sym", current_asset.symbol)("asset_id", current_asset.id) );
		  }
	   } // for each market issued asset
	   wlog( "Done processing hard fork core-868-890 at block ${n}", ("n",head_num) );
	}
	
process_hf_935	
-------------------------------

.. code-block:: cpp 

	/******
	 * @brief one-time data process for hard fork core-935
	 *
	 * Prior to hardfork 935, `check_call_orders` may be unintendedly skipped when
	 * median price feed has changed. This method will run at the hardfork time, and
	 * call `check_call_orders` for all markets.
	 * https://github.com/bitshares/bitshares-core/issues/935
	 *
	 * @param db the database
	 */
	// TODO: for better performance, this function can be removed if it actually updated nothing at hf time.
	//       * Also need to update related test cases
	//       * NOTE: perhaps the removal can't be applied to testnet
	void process_hf_935( database& db )
	{
	   bool changed_something = false;
	   const asset_bitasset_data_object* bitasset = nullptr;
	   bool settled_before_check_call;
	   bool settled_after_check_call;
	   // for each market issued asset
	   const auto& asset_idx = db.get_index_type<asset_index>().indices().get<by_type>();
	   for( auto asset_itr = asset_idx.lower_bound(true); asset_itr != asset_idx.end(); ++asset_itr )
	   {
		  const auto& current_asset = *asset_itr;

		  if( !changed_something )
		  {
			 bitasset = &current_asset.bitasset_data( db );
			 settled_before_check_call = bitasset->has_settlement(); // whether already force settled
		  }

		  bool called_some = db.check_call_orders( current_asset );

		  if( !changed_something )
		  {
			 settled_after_check_call = bitasset->has_settlement(); // whether already force settled

			 if( settled_before_check_call != settled_after_check_call || called_some )
			 {
				changed_something = true;
				wlog( "process_hf_935 changed something" );
			 }
		  }
	   }
	}
	
	
perform_chain_maintenance	
-------------------------------

.. code-block:: cpp 

	void database::perform_chain_maintenance(const signed_block& next_block, const global_property_object& global_props)
	{
	   const auto& gpo = get_global_properties();

	   distribute_fba_balances(*this);
	   create_buyback_orders(*this);

	   struct vote_tally_helper {
		  database& d;
		  const global_property_object& props;

		  vote_tally_helper(database& d, const global_property_object& gpo)
			 : d(d), props(gpo)
		  {
			 d._vote_tally_buffer.resize(props.next_available_vote_id);
			 d._witness_count_histogram_buffer.resize(props.parameters.maximum_witness_count / 2 + 1);
			 d._committee_count_histogram_buffer.resize(props.parameters.maximum_committee_count / 2 + 1);
			 d._total_voting_stake = 0;
		  }

		  void operator()( const account_object& stake_account, const account_statistics_object& stats )
		  {
			 if( props.parameters.count_non_member_votes || stake_account.is_member(d.head_block_time()) )
			 {
				// There may be a difference between the account whose stake is voting and the one specifying opinions.
				// Usually they're the same, but if the stake account has specified a voting_account, that account is the one
				// specifying the opinions.
				const account_object& opinion_account =
					  (stake_account.options.voting_account ==
					   GRAPHENE_PROXY_TO_SELF_ACCOUNT)? stake_account
										 : d.get(stake_account.options.voting_account);

				uint64_t voting_stake = stats.total_core_in_orders.value
					  + (stake_account.cashback_vb.valid() ? (*stake_account.cashback_vb)(d).balance.amount.value: 0)
					  + stats.core_in_balance.value;

				for( vote_id_type id : opinion_account.options.votes )
				{
				   uint32_t offset = id.instance();
				   // if they somehow managed to specify an illegal offset, ignore it.
				   if( offset < d._vote_tally_buffer.size() )
					  d._vote_tally_buffer[offset] += voting_stake;
				}

				if( opinion_account.options.num_witness <= props.parameters.maximum_witness_count )
				{
				   uint16_t offset = std::min(size_t(opinion_account.options.num_witness/2),
											  d._witness_count_histogram_buffer.size() - 1);
				   // votes for a number greater than maximum_witness_count
				   // are turned into votes for maximum_witness_count.
				   //
				   // in particular, this takes care of the case where a
				   // member was voting for a high number, then the
				   // parameter was lowered.
				   d._witness_count_histogram_buffer[offset] += voting_stake;
				}
				if( opinion_account.options.num_committee <= props.parameters.maximum_committee_count )
				{
				   uint16_t offset = std::min(size_t(opinion_account.options.num_committee/2),
											  d._committee_count_histogram_buffer.size() - 1);
				   // votes for a number greater than maximum_committee_count
				   // are turned into votes for maximum_committee_count.
				   //
				   // same rationale as for witnesses
				   d._committee_count_histogram_buffer[offset] += voting_stake;
				}

				d._total_voting_stake += voting_stake;
			 }
		  }
	   } tally_helper(*this, gpo);

	   perform_account_maintenance( tally_helper );

	   struct clear_canary {
		  clear_canary(vector<uint64_t>& target): target(target){}
		  ~clear_canary() { target.clear(); }
	   private:
		  vector<uint64_t>& target;
	   };
	   clear_canary a(_witness_count_histogram_buffer),
					b(_committee_count_histogram_buffer),
					c(_vote_tally_buffer);

	   update_top_n_authorities(*this);
	   update_active_witnesses();
	   update_active_committee_members();
	   update_worker_votes();

	   const dynamic_global_property_object& dgpo = get_dynamic_global_properties();

	   modify(gpo, [&dgpo](global_property_object& p) {
		  // Remove scaling of account registration fee
		  p.parameters.current_fees->get<account_create_operation>().basic_fee >>= p.parameters.account_fee_scale_bitshifts *
				(dgpo.accounts_registered_this_interval / p.parameters.accounts_per_fee_scale);

		  if( p.pending_parameters )
		  {
			 p.parameters = std::move(*p.pending_parameters);
			 p.pending_parameters.reset();
		  }
	   });

	   auto next_maintenance_time = dgpo.next_maintenance_time;
	   auto maintenance_interval = gpo.parameters.maintenance_interval;

	   if( next_maintenance_time <= next_block.timestamp )
	   {
		  if( next_block.block_num() == 1 )
			 next_maintenance_time = time_point_sec() +
				   (((next_block.timestamp.sec_since_epoch() / maintenance_interval) + 1) * maintenance_interval);
		  else
		  {
			 // We want to find the smallest k such that next_maintenance_time + k * maintenance_interval > head_block_time()
			 //  This implies k > ( head_block_time() - next_maintenance_time ) / maintenance_interval
			 //
			 // Let y be the right-hand side of this inequality, i.e.
			 // y = ( head_block_time() - next_maintenance_time ) / maintenance_interval
			 //
			 // and let the fractional part f be y-floor(y).  Clearly 0 <= f < 1.
			 // We can rewrite f = y-floor(y) as floor(y) = y-f.
			 //
			 // Clearly k = floor(y)+1 has k > y as desired.  Now we must
			 // show that this is the least such k, i.e. k-1 <= y.
			 //
			 // But k-1 = floor(y)+1-1 = floor(y) = y-f <= y.
			 // So this k suffices.
			 //
			 auto y = (head_block_time() - next_maintenance_time).to_seconds() / maintenance_interval;
			 next_maintenance_time += (y+1) * maintenance_interval;
		  }
	   }

	   if( (dgpo.next_maintenance_time < HARDFORK_613_TIME) && (next_maintenance_time >= HARDFORK_613_TIME) )
		  deprecate_annual_members(*this);

	   // To reset call_price of all call orders, then match by new rule
	   bool to_update_and_match_call_orders = false;
	   if( (dgpo.next_maintenance_time <= HARDFORK_CORE_343_TIME) && (next_maintenance_time > HARDFORK_CORE_343_TIME) )
		  to_update_and_match_call_orders = true;

	   // Process inconsistent price feeds
	   if( (dgpo.next_maintenance_time <= HARDFORK_CORE_868_890_TIME) && (next_maintenance_time > HARDFORK_CORE_868_890_TIME) )
		  process_hf_868_890( *this, to_update_and_match_call_orders );

	   // Explicitly call check_call_orders of all markets
	   if( (dgpo.next_maintenance_time <= HARDFORK_CORE_935_TIME) && (next_maintenance_time > HARDFORK_CORE_935_TIME)
			 && !to_update_and_match_call_orders )
		  process_hf_935( *this );

	   modify(dgpo, [next_maintenance_time](dynamic_global_property_object& d) {
		  d.next_maintenance_time = next_maintenance_time;
		  d.accounts_registered_this_interval = 0;
	   });

	   // We need to do it after updated next_maintenance_time, to apply new rules here
	   if( to_update_and_match_call_orders )
		  update_and_match_call_orders(*this);

	   process_bitassets();

	   // process_budget needs to run at the bottom because
	   //   it needs to know the next_maintenance_time
	   process_budget();
	}



-------------------

|

db_management
================================


.. code-block:: cpp 

	database::database()
	{
	   initialize_indexes();
	   initialize_evaluators();
	}

	database::~database()
	{
	   clear_pending();
	}


reindex
-------------------------------

.. code-block:: cpp 

	void database::reindex( fc::path data_dir )
	{ try {
	   auto last_block = _block_id_to_block.last();
	   if( !last_block ) {
		  elog( "!no last block" );
		  edump((last_block));
		  return;
	   }
	   if( last_block->block_num() <= head_block_num()) return;

	   ilog( "reindexing blockchain" );
	   auto start = fc::time_point::now();
	   const auto last_block_num = last_block->block_num();
	   uint32_t flush_point = last_block_num < 10000 ? 0 : last_block_num - 10000;
	   uint32_t undo_point = last_block_num < 50 ? 0 : last_block_num - 50;

	   ilog( "Replaying blocks, starting at ${next}...", ("next",head_block_num() + 1) );
	   if( head_block_num() >= undo_point )
	   {
		  if( head_block_num() > 0 )
			 _fork_db.start_block( *fetch_block_by_number( head_block_num() ) );
	   }
	   else
		  _undo_db.disable();

	   uint32_t skip = skip_witness_signature |
					   skip_block_size_check |
					   skip_merkle_check |
					   skip_transaction_signatures |
					   skip_transaction_dupe_check |
					   skip_tapos_check |
					   skip_witness_schedule_check |
					   skip_authority_check;

	   size_t total_processed_block_size;
	   size_t total_block_size = _block_id_to_block.total_block_size();
	   const auto& gpo = get_global_properties();
	   for( uint32_t i = head_block_num() + 1; i <= last_block_num; ++i )
	   {
		  if( i % 10000 == 0 ) 
		  {
			 total_processed_block_size = _block_id_to_block.blocks_current_position();

			 ilog(
				"   [by size: ${size}%   ${processed} of ${total}]   [by num: ${num}%   ${i} of ${last}]",
				("size", double(total_processed_block_size) / total_block_size * 100)
				("processed", total_processed_block_size)
				("total", total_block_size)
				("num", double(i*100)/last_block_num)
				("i", i)
				("last", last_block_num)
			 );
		  }
		  if( i == flush_point )
		  {
			 ilog( "Writing database to disk at block ${i}", ("i",i) );
			 flush();
			 ilog( "Done" );
		  }
		  if( head_block_time() >= last_block->timestamp - gpo.parameters.maximum_time_until_expiration )
			 skip &= ~skip_transaction_dupe_check;
		  fc::optional< signed_block > block = _block_id_to_block.fetch_by_number(i);
		  if( !block.valid() )
		  {
			 wlog( "Reindexing terminated due to gap:  Block ${i} does not exist!", ("i", i) );
			 uint32_t dropped_count = 0;
			 while( true )
			 {
				fc::optional< block_id_type > last_id = _block_id_to_block.last_id();
				// this can trigger if we attempt to e.g. read a file that has block #2 but no block #1
				if( !last_id.valid() )
				   break;
				// we've caught up to the gap
				if( block_header::num_from_id( *last_id ) <= i )
				   break;
				_block_id_to_block.remove( *last_id );
				dropped_count++;
			 }
			 wlog( "Dropped ${n} blocks from after the gap", ("n", dropped_count) );
			 break;
		  }
		  if( i < undo_point )
			 apply_block( *block, skip );
		  else
		  {
			 _undo_db.enable();
			 push_block( *block, skip );
		  }
	   }
	   _undo_db.enable();
	   auto end = fc::time_point::now();
	   ilog( "Done reindexing, elapsed time: ${t} sec", ("t",double((end-start).count())/1000000.0 ) );
	} FC_CAPTURE_AND_RETHROW( (data_dir) ) }
	 //(20181019)


wipe
-------------------------------

.. code-block:: cpp 

	void database::wipe(const fc::path& data_dir, bool include_blocks)
	{
	   ilog("Wiping database", ("include_blocks", include_blocks));
	   if (_opened) {
		 close();
	   }
	   object_database::wipe(data_dir);
	   if( include_blocks )
		  fc::remove_all( data_dir / "database" );
	}


open
-------------------------------

.. code-block:: cpp 

	void database::open(
	   const fc::path& data_dir,
	   std::function<genesis_state_type()> genesis_loader,
	   const std::string& db_version)
	{
	   try
	   {
		  bool wipe_object_db = false;
		  if( !fc::exists( data_dir / "db_version" ) )
			 wipe_object_db = true;
		  else
		  {
			 std::string version_string;
			 fc::read_file_contents( data_dir / "db_version", version_string );
			 wipe_object_db = ( version_string != db_version );
		  }
		  if( wipe_object_db ) {
			  ilog("Wiping object_database due to missing or wrong version");
			  object_database::wipe( data_dir );
			  std::ofstream version_file( (data_dir / "db_version").generic_string().c_str(),
										  std::ios::out | std::ios::binary | std::ios::trunc );
			  version_file.write( db_version.c_str(), db_version.size() );
			  version_file.close();
		  }

		  object_database::open(data_dir);

		  _block_id_to_block.open(data_dir / "database" / "block_num_to_block");

		  if( !find(global_property_id_type()) )
			 init_genesis(genesis_loader());
		  else
		  {
			 _p_core_asset_obj = &get( asset_id_type() );
			 _p_core_dynamic_data_obj = &get( asset_dynamic_data_id_type() );
			 _p_global_prop_obj = &get( global_property_id_type() );
			 _p_chain_property_obj = &get( chain_property_id_type() );
			 _p_dyn_global_prop_obj = &get( dynamic_global_property_id_type() );
			 _p_witness_schedule_obj = &get( witness_schedule_id_type() );
		  }

		  fc::optional<block_id_type> last_block = _block_id_to_block.last_id();
		  if( last_block.valid() )
		  {
			 FC_ASSERT( *last_block >= head_block_id(),
						"last block ID does not match current chain state",
						("last_block->id", last_block)("head_block_id",head_block_num()) );
			 reindex( data_dir );
		  }
		  _opened = true;
	   }
	   FC_CAPTURE_LOG_AND_RETHROW( (data_dir) )
	}


close
-------------------------------

.. code-block:: cpp 

	void database::close(bool rewind)
	{
	   // TODO:  Save pending tx's on close()
	   clear_pending();

	   // pop all of the blocks that we can given our undo history, this should
	   // throw when there is no more undo history to pop
	   if( rewind )
	   {
		  try
		  {
			 uint32_t cutoff = get_dynamic_global_properties().last_irreversible_block_num;

			 ilog( "Rewinding from ${head} to ${cutoff}", ("head",head_block_num())("cutoff",cutoff) );
			 while( head_block_num() > cutoff )
			 {
				block_id_type popped_block_id = head_block_id();
				pop_block();
				_fork_db.remove(popped_block_id); // doesn't throw on missing
			 }
		  }
		  catch ( const fc::exception& e )
		  {
			 wlog( "Database close unexpected exception: ${e}", ("e", e) );
		  }
	   }

	   // Since pop_block() will move tx's in the popped blocks into pending,
	   // we have to clear_pending() after we're done popping to get a clean
	   // DB state (issue #336).
	   clear_pending();

	   object_database::flush();
	   object_database::close();

	   if( _block_id_to_block.is_open() )
		  _block_id_to_block.close();

	   _fork_db.reset();

	   _opened = false;
	}





-------------------

|

db_market
================================
	
	
globally_settle_asset	
-------------------------------

* All margin positions are force closed at the swan price 
* Collateral received goes into a force-settlement fund 
* No new margin positions can be created for this asset 
* Force settlement happens without delay at the swan price, deducting from force-settlement fund
* No more asset updates may be issued.

.. code-block:: cpp 

	void database::globally_settle_asset( const asset_object& mia, const price& settlement_price )
	{ try {
	   /*
	   elog( "BLACK SWAN!" );
	   debug_dump();
	   edump( (mia.symbol)(settlement_price) );
	   */

	   const asset_bitasset_data_object& bitasset = mia.bitasset_data(*this);
	   FC_ASSERT( !bitasset.has_settlement(), "black swan already occurred, it should not happen again" );

	   const asset_object& backing_asset = bitasset.options.short_backing_asset(*this);
	   asset collateral_gathered = backing_asset.amount(0);

	   const asset_dynamic_data_object& mia_dyn = mia.dynamic_asset_data_id(*this);
	   auto original_mia_supply = mia_dyn.current_supply;

	   const call_order_index& call_index = get_index_type<call_order_index>();
	   const auto& call_price_index = call_index.indices().get<by_price>();

	   auto maint_time = get_dynamic_global_properties().next_maintenance_time;
	   bool before_core_hardfork_342 = ( maint_time <= HARDFORK_CORE_342_TIME ); // better rounding

	   // cancel all call orders and accumulate it into collateral_gathered
	   auto call_itr = call_price_index.lower_bound( price::min( bitasset.options.short_backing_asset, mia.id ) );
	   auto call_end = call_price_index.upper_bound( price::max( bitasset.options.short_backing_asset, mia.id ) );
	   asset pays;
	   while( call_itr != call_end )
	   {
		  if( before_core_hardfork_342 )
		  {
			 pays = call_itr->get_debt() * settlement_price; // round down, in favor of call order

			 // Be here, the call order can be paying nothing
			 if( pays.amount == 0 && !bitasset.is_prediction_market ) // TODO remove this warning after hard fork core-342
				wlog( "Something for nothing issue (#184, variant E) occurred at block #${block}", ("block",head_block_num()) );
		  }
		  else
			 pays = call_itr->get_debt().multiply_and_round_up( settlement_price ); // round up, in favor of global settlement fund

		  if( pays > call_itr->get_collateral() )
			 pays = call_itr->get_collateral();

		  collateral_gathered += pays;
		  const auto&  order = *call_itr;
		  ++call_itr;
		  FC_ASSERT( fill_call_order( order, pays, order.get_debt(), settlement_price, true ) ); // call order is maker
	   }

	   modify( bitasset, [&]( asset_bitasset_data_object& obj ){
			   assert( collateral_gathered.asset_id == settlement_price.quote.asset_id );
			   obj.settlement_price = mia.amount(original_mia_supply) / collateral_gathered; //settlement_price;
			   obj.settlement_fund  = collateral_gathered.amount;
			   });

	   /// After all margin positions are closed, the current supply will be reported as 0, but
	   /// that is a lie, the supply didn't change.   We need to capture the current supply before
	   /// filling all call orders and then restore it afterward.   Then in the force settlement
	   /// evaluator reduce the supply
	   modify( mia_dyn, [&]( asset_dynamic_data_object& obj ){
			   obj.current_supply = original_mia_supply;
			 });

	} FC_CAPTURE_AND_RETHROW( (mia)(settlement_price) ) }

	
revive_bitasset
---------------------------------------

.. code-block:: cpp 

	void database::revive_bitasset( const asset_object& bitasset )
	{ try {
	   FC_ASSERT( bitasset.is_market_issued() );
	   const asset_bitasset_data_object& bad = bitasset.bitasset_data(*this);
	   FC_ASSERT( bad.has_settlement() );
	   const asset_dynamic_data_object& bdd = bitasset.dynamic_asset_data_id(*this);
	   FC_ASSERT( !bad.is_prediction_market );
	   FC_ASSERT( !bad.current_feed.settlement_price.is_null() );

	   if( bdd.current_supply > 0 )
	   {
		  // Create + execute a "bid" with 0 additional collateral
		  const collateral_bid_object& pseudo_bid = create<collateral_bid_object>([&](collateral_bid_object& bid) {
			 bid.bidder = bitasset.issuer;
			 bid.inv_swan_price = asset(0, bad.options.short_backing_asset)
								  / asset(bdd.current_supply, bitasset.id);
		  });
		  execute_bid( pseudo_bid, bdd.current_supply, bad.settlement_fund, bad.current_feed );
	   } else
		  FC_ASSERT( bad.settlement_fund == 0 );

	   _cancel_bids_and_revive_mpa( bitasset, bad );
	} FC_CAPTURE_AND_RETHROW( (bitasset) ) }

	
_cancel_bids_and_revive_mpa
---------------------------------------

.. code-block:: cpp 

	void database::_cancel_bids_and_revive_mpa( const asset_object& bitasset, const asset_bitasset_data_object& bad )
	{ try {
	   FC_ASSERT( bitasset.is_market_issued() );
	   FC_ASSERT( bad.has_settlement() );
	   FC_ASSERT( !bad.is_prediction_market );

	   // cancel remaining bids
	   const auto& bid_idx = get_index_type< collateral_bid_index >().indices().get<by_price>();
	   auto itr = bid_idx.lower_bound( boost::make_tuple( bitasset.id, price::max( bad.options.short_backing_asset, bitasset.id ), collateral_bid_id_type() ) );
	   while( itr != bid_idx.end() && itr->inv_swan_price.quote.asset_id == bitasset.id )
	   {
		  const collateral_bid_object& bid = *itr;
		  ++itr;
		  cancel_bid( bid );
	   }

	   // revive
	   modify( bad, [&]( asset_bitasset_data_object& obj ){
				  obj.settlement_price = price();
				  obj.settlement_fund = 0;
			   });
	} FC_CAPTURE_AND_RETHROW( (bitasset) ) }

	
cancel_bid
---------------------------------------

.. code-block:: cpp 

	void database::cancel_bid(const collateral_bid_object& bid, bool create_virtual_op)
	{
	   adjust_balance(bid.bidder, bid.inv_swan_price.base);

	   if( create_virtual_op )
	   {
		  bid_collateral_operation vop;
		  vop.bidder = bid.bidder;
		  vop.additional_collateral = bid.inv_swan_price.base;
		  vop.debt_covered = asset( 0, bid.inv_swan_price.quote.asset_id );
		  push_applied_operation( vop );
	   }
	   remove(bid);
	}

execute_bid
---------------------------------------

.. code-block:: cpp 

	void database::execute_bid( const collateral_bid_object& bid, share_type debt_covered, share_type collateral_from_fund, const price_feed& current_feed )
	{
	   const call_order_object& call_obj = create<call_order_object>( [&](call_order_object& call ){
			 call.borrower = bid.bidder;
			 call.collateral = bid.inv_swan_price.base.amount + collateral_from_fund;
			 call.debt = debt_covered;
			 call.call_price = price::call_price(asset(debt_covered, bid.inv_swan_price.quote.asset_id),
												 asset(call.collateral, bid.inv_swan_price.base.asset_id),
												 current_feed.maintenance_collateral_ratio);
		  });

	   if( bid.inv_swan_price.base.asset_id == asset_id_type() )
		  modify(bid.bidder(*this).statistics(*this), [&](account_statistics_object& stats) {
			 stats.total_core_in_orders += call_obj.collateral;
		  });

	   push_applied_operation( execute_bid_operation( bid.bidder, asset( call_obj.collateral, bid.inv_swan_price.base.asset_id ),
													  asset( debt_covered, bid.inv_swan_price.quote.asset_id ) ) );

	   remove(bid);
	}

cancel_settle_order
---------------------------------------

.. code-block:: cpp 

	void database::cancel_settle_order(const force_settlement_object& order, bool create_virtual_op)
	{
	   adjust_balance(order.owner, order.balance);

	   if( create_virtual_op )
	   {
		  asset_settle_cancel_operation vop;
		  vop.settlement = order.id;
		  vop.account = order.owner;
		  vop.amount = order.balance;
		  push_applied_operation( vop );
	   }
	   remove(order);
	}

cancel_limit_order
---------------------------------------

.. code-block:: cpp 

	void database::cancel_limit_order( const limit_order_object& order, bool create_virtual_op, bool skip_cancel_fee )
	{
	   // if need to create a virtual op, try deduct a cancellation fee here.
	   // there are two scenarios when order is cancelled and need to create a virtual op:
	   // 1. due to expiration: always deduct a fee if there is any fee deferred
	   // 2. due to cull_small: deduct a fee after hard fork 604, but not before (will set skip_cancel_fee)
	   const account_statistics_object* seller_acc_stats = nullptr;
	   const asset_dynamic_data_object* fee_asset_dyn_data = nullptr;
	   limit_order_cancel_operation vop;
	   share_type deferred_fee = order.deferred_fee;
	   asset deferred_paid_fee = order.deferred_paid_fee;
	   if( create_virtual_op )
	   {
		  vop.order = order.id;
		  vop.fee_paying_account = order.seller;
		  // only deduct fee if not skipping fee, and there is any fee deferred
		  if( !skip_cancel_fee && deferred_fee > 0 )
		  {
			 asset core_cancel_fee = current_fee_schedule().calculate_fee( vop );
			 // cap the fee
			 if( core_cancel_fee.amount > deferred_fee )
				core_cancel_fee.amount = deferred_fee;
			 // if there is any CORE fee to deduct, redirect it to referral program
			 if( core_cancel_fee.amount > 0 )
			 {
				seller_acc_stats = &order.seller( *this ).statistics( *this );
				modify( *seller_acc_stats, [&]( account_statistics_object& obj ) {
				   obj.pay_fee( core_cancel_fee.amount, get_global_properties().parameters.cashback_vesting_threshold );
				} );
				deferred_fee -= core_cancel_fee.amount;
				// handle originally paid fee if any:
				//    to_deduct = round_up( paid_fee * core_cancel_fee / deferred_core_fee_before_deduct )
				if( deferred_paid_fee.amount == 0 )
				{
				   vop.fee = core_cancel_fee;
				}
				else
				{
				   fc::uint128 fee128( deferred_paid_fee.amount.value );
				   fee128 *= core_cancel_fee.amount.value;
				   // to round up
				   fee128 += order.deferred_fee.value;
				   fee128 -= 1;
				   fee128 /= order.deferred_fee.value;
				   share_type cancel_fee_amount = fee128.to_uint64();
				   // cancel_fee should be positive, pay it to asset's accumulated_fees
				   fee_asset_dyn_data = &deferred_paid_fee.asset_id(*this).dynamic_asset_data_id(*this);
				   modify( *fee_asset_dyn_data, [&](asset_dynamic_data_object& addo) {
					  addo.accumulated_fees += cancel_fee_amount;
				   });
				   // cancel_fee should be no more than deferred_paid_fee
				   deferred_paid_fee.amount -= cancel_fee_amount;
				   vop.fee = asset( cancel_fee_amount, deferred_paid_fee.asset_id );
				}
			 }
		  }
	   }

	   // refund funds in order
	   auto refunded = order.amount_for_sale();
	   if( refunded.asset_id == asset_id_type() )
	   {
		  if( seller_acc_stats == nullptr )
			 seller_acc_stats = &order.seller( *this ).statistics( *this );
		  modify( *seller_acc_stats, [&]( account_statistics_object& obj ) {
			 obj.total_core_in_orders -= refunded.amount;
		  });
	   }
	   adjust_balance(order.seller, refunded);

	   // refund fee
	   // could be virtual op or real op here
	   if( order.deferred_paid_fee.amount == 0 )
	   {
		  // be here, order.create_time <= HARDFORK_CORE_604_TIME, or fee paid in CORE, or no fee to refund.
		  // if order was created before hard fork 604 then cancelled no matter before or after hard fork 604,
		  //    see it as fee paid in CORE, deferred_fee should be refunded to order owner but not fee pool
		  adjust_balance( order.seller, deferred_fee );
	   }
	   else // need to refund fee in originally paid asset
	   {
		  adjust_balance(order.seller, deferred_paid_fee);
		  // be here, must have: fee_asset != CORE
		  if( fee_asset_dyn_data == nullptr )
			 fee_asset_dyn_data = &deferred_paid_fee.asset_id(*this).dynamic_asset_data_id(*this);
		  modify( *fee_asset_dyn_data, [&](asset_dynamic_data_object& addo) {
			 addo.fee_pool += deferred_fee;
		  });
	   }

	   if( create_virtual_op )
		  push_applied_operation( vop );

	   remove(order);
	}

maybe_cull_small_order
---------------------------------------

.. code-block:: cpp 

	bool maybe_cull_small_order( database& db, const limit_order_object& order )
	{
	   /**
		*  There are times when the AMOUNT_FOR_SALE * SALE_PRICE == 0 which means that we
		*  have hit the limit where the seller is asking for nothing in return.  When this
		*  happens we must refund any balance back to the seller, it is too small to be
		*  sold at the sale price.
		*
		*  If the order is a taker order (as opposed to a maker order), so the price is
		*  set by the counterparty, this check is deferred until the order becomes unmatched
		*  (see #555) -- however, detecting this condition is the responsibility of the caller.
		*/
	   if( order.amount_to_receive().amount == 0 )
	   {
		  if( order.deferred_fee > 0 && db.head_block_time() <= HARDFORK_CORE_604_TIME )
		  { // TODO remove this warning after hard fork core-604
			 wlog( "At block ${n}, cancelling order without charging a fee: ${o}", ("n",db.head_block_num())("o",order) );
			 db.cancel_limit_order( order, true, true );
		  }
		  else
			 db.cancel_limit_order( order );
		  return true;
	   }
	   return false;
	}

apply_order_before_hardfork_625
---------------------------------------

.. code-block:: cpp 

	bool database::(const limit_order_object& new_order_object, bool allow_black_swan)
	{
	   auto order_id = new_order_object.id;
	   const asset_object& sell_asset = get(new_order_object.amount_for_sale().asset_id);
	   const asset_object& receive_asset = get(new_order_object.amount_to_receive().asset_id);

	   // Possible optimization: We only need to check calls if both are true:
	   //  - The new order is at the front of the book
	   //  - The new order is below the call limit price
	   bool called_some = check_call_orders(sell_asset, allow_black_swan, true); // the first time when checking, call order is maker
	   called_some |= check_call_orders(receive_asset, allow_black_swan, true); // the other side, same as above
	   if( called_some && !find_object(order_id) ) // then we were filled by call order
		  return true;

	   const auto& limit_price_idx = get_index_type<limit_order_index>().indices().get<by_price>();

	   // TODO: it should be possible to simply check the NEXT/PREV iterator after new_order_object to
	   // determine whether or not this order has "changed the book" in a way that requires us to
	   // check orders. For now I just lookup the lower bound and check for equality... this is log(n) vs
	   // constant time check. Potential optimization.

	   auto max_price = ~new_order_object.sell_price;
	   auto limit_itr = limit_price_idx.lower_bound(max_price.max());
	   auto limit_end = limit_price_idx.upper_bound(max_price);

	   bool finished = false;
	   while( !finished && limit_itr != limit_end )
	   {
		  auto old_limit_itr = limit_itr;
		  ++limit_itr;
		  // match returns 2 when only the old order was fully filled. In this case, we keep matching; otherwise, we stop.
		  finished = (match(new_order_object, *old_limit_itr, old_limit_itr->sell_price) != 2);
	   }

	   //Possible optimization: only check calls if the new order completely filled some old order
	   //Do I need to check both assets?
	   check_call_orders(sell_asset, allow_black_swan); // after the new limit order filled some orders on the book,
														// if a call order matches another order, the call order is taker
	   check_call_orders(receive_asset, allow_black_swan); // the other side, same as above

	   const limit_order_object* updated_order_object = find< limit_order_object >( order_id );
	   if( updated_order_object == nullptr )
		  return true;
	   if( head_block_time() <= HARDFORK_555_TIME )
		  return false;
	   // before #555 we would have done maybe_cull_small_order() logic as a result of fill_order() being called by match() above
	   // however after #555 we need to get rid of small orders -- #555 hardfork defers logic that was done too eagerly before, and
	   // this is the point it's deferred to.
	   return maybe_cull_small_order( *this, *updated_order_object );
	}

apply_order
---------------------------------------

.. code-block:: cpp 

	bool database::apply_order(const limit_order_object& new_order_object, bool allow_black_swan)
	{
	   auto order_id = new_order_object.id;
	   asset_id_type sell_asset_id = new_order_object.sell_asset_id();
	   asset_id_type recv_asset_id = new_order_object.receive_asset_id();

	   // We only need to check if the new order will match with others if it is at the front of the book
	   const auto& limit_price_idx = get_index_type<limit_order_index>().indices().get<by_price>();
	   auto limit_itr = limit_price_idx.lower_bound( boost::make_tuple( new_order_object.sell_price, order_id ) );
	   if( limit_itr != limit_price_idx.begin() )
	   {
		  --limit_itr;
		  if( limit_itr->sell_asset_id() == sell_asset_id && limit_itr->receive_asset_id() == recv_asset_id )
			 return false;
	   }

	   // this is the opposite side (on the book)
	   auto max_price = ~new_order_object.sell_price;
	   limit_itr = limit_price_idx.lower_bound( max_price.max() );
	   auto limit_end = limit_price_idx.upper_bound( max_price );

	   // Order matching should be in favor of the taker.
	   // When a new limit order is created, e.g. an ask, need to check if it will match the highest bid.
	   // We were checking call orders first. However, due to MSSR (maximum_short_squeeze_ratio),
	   // effective price of call orders may be worse than limit orders, so we should also check limit orders here.

	   // Question: will a new limit order trigger a black swan event?
	   //
	   // 1. as of writing, it's possible due to the call-order-and-limit-order overlapping issue:
	   //       https://github.com/bitshares/bitshares-core/issues/606 .
	   //    when it happens, a call order can be very big but don't match with the opposite,
	   //    even when price feed is too far away, further than swan price,
	   //    if the new limit order is in the same direction with the call orders, it can eat up all the opposite,
	   //    then the call order will lose support and trigger a black swan event.
	   // 2. after issue 606 is fixed, there will be no limit order on the opposite side "supporting" the call order,
	   //    so a new order in the same direction with the call order won't trigger a black swan event.
	   // 3. calling is one direction. if the new limit order is on the opposite direction,
	   //    no matter if matches with the call, it won't trigger a black swan event.
	   //    (if a match at MSSP caused a black swan event, it means the call order is already undercollateralized,
	   //      which should trigger a black swan event earlier.)
	   //
	   // Since it won't trigger a black swan, no need to check here.

	   // currently we don't do cross-market (triangle) matching.
	   // the limit order will only match with a call order if meet all of these:
	   // 1. it's buying collateral, which means sell_asset is the MIA, receive_asset is the backing asset.
	   // 2. sell_asset is not a prediction market
	   // 3. sell_asset is not globally settled
	   // 4. sell_asset has a valid price feed
	   // 5. the call order's collateral ratio is below or equals to MCR
	   // 6. the limit order provided a good price

	   bool to_check_call_orders = false;
	   const asset_object& sell_asset = sell_asset_id( *this );
	   const asset_bitasset_data_object* sell_abd = nullptr;
	   price call_match_price;
	   if( sell_asset.is_market_issued() )
	   {
		  sell_abd = &sell_asset.bitasset_data( *this );
		  if( sell_abd->options.short_backing_asset == recv_asset_id
			  && !sell_abd->is_prediction_market
			  && !sell_abd->has_settlement()
			  && !sell_abd->current_feed.settlement_price.is_null() )
		  {
			 call_match_price = ~sell_abd->current_feed.max_short_squeeze_price();
			 if( ~new_order_object.sell_price <= call_match_price ) // new limit order price is good enough to match a call
				to_check_call_orders = true;
		  }
	   }

	   bool finished = false; // whether the new order is gone
	   if( to_check_call_orders )
	   {
		  // check limit orders first, match the ones with better price in comparison to call orders
		  while( !finished && limit_itr != limit_end && limit_itr->sell_price > call_match_price )
		  {
			 auto old_limit_itr = limit_itr;
			 ++limit_itr;
			 // match returns 2 when only the old order was fully filled. In this case, we keep matching; otherwise, we stop.
			 finished = ( match( new_order_object, *old_limit_itr, old_limit_itr->sell_price ) != 2 );
		  }

		  if( !finished )
		  {
			 // check if there are margin calls
			 const auto& call_price_idx = get_index_type<call_order_index>().indices().get<by_price>();
			 auto call_min = price::min( recv_asset_id, sell_asset_id );
			 while( !finished )
			 {
				// assume hard fork core-343 and core-625 will take place at same time, always check call order with least call_price
				auto call_itr = call_price_idx.lower_bound( call_min );
				if( call_itr == call_price_idx.end()
					  || call_itr->debt_type() != sell_asset_id
					  // feed protected https://github.com/cryptonomex/graphene/issues/436
					  || call_itr->call_price > ~sell_abd->current_feed.settlement_price )
				   break;
				// assume hard fork core-338 and core-625 will take place at same time, not checking HARDFORK_CORE_338_TIME here.
				int match_result = match( new_order_object, *call_itr, call_match_price,
										  sell_abd->current_feed.settlement_price,
										  sell_abd->current_feed.maintenance_collateral_ratio );
				// match returns 1 or 3 when the new order was fully filled. In this case, we stop matching; otherwise keep matching.
				// since match can return 0 due to BSIP38 (hard fork core-834), we no longer only check if the result is 2.
				if( match_result == 1 || match_result == 3 )
				   finished = true;
			 }
		  }
	   }

	   // still need to check limit orders
	   while( !finished && limit_itr != limit_end )
	   {
		  auto old_limit_itr = limit_itr;
		  ++limit_itr;
		  // match returns 2 when only the old order was fully filled. In this case, we keep matching; otherwise, we stop.
		  finished = ( match( new_order_object, *old_limit_itr, old_limit_itr->sell_price ) != 2 );
	   }

	   const limit_order_object* updated_order_object = find< limit_order_object >( order_id );
	   if( updated_order_object == nullptr )
		  return true;

	   // before #555 we would have done maybe_cull_small_order() logic as a result of fill_order() being called by match() above
	   // however after #555 we need to get rid of small orders -- #555 hardfork defers logic that was done too eagerly before, and
	   // this is the point it's deferred to.
	   return maybe_cull_small_order( *this, *updated_order_object );
	}

match (int database)
---------------------------------------

.. code-block:: cpp 

	/**
	 *  Matches the two orders, the first parameter is taker, the second is maker.
	 *
	 *  @return a bit field indicating which orders were filled (and thus removed)
	 *
	 *  0 - no orders were matched
	 *  1 - taker was filled
	 *  2 - maker was filled
	 *  3 - both were filled
	 */
	int database::match( const limit_order_object& usd, const limit_order_object& core, const price& match_price )
	{
	   FC_ASSERT( usd.sell_price.quote.asset_id == core.sell_price.base.asset_id );
	   FC_ASSERT( usd.sell_price.base.asset_id  == core.sell_price.quote.asset_id );
	   FC_ASSERT( usd.for_sale > 0 && core.for_sale > 0 );

	   auto usd_for_sale = usd.amount_for_sale();
	   auto core_for_sale = core.amount_for_sale();

	   asset usd_pays, usd_receives, core_pays, core_receives;

	   auto maint_time = get_dynamic_global_properties().next_maintenance_time;
	   bool before_core_hardfork_342 = ( maint_time <= HARDFORK_CORE_342_TIME ); // better rounding

	   bool cull_taker = false;
	   if( usd_for_sale <= core_for_sale * match_price ) // rounding down here should be fine
	   {
		  usd_receives  = usd_for_sale * match_price; // round down, in favor of bigger order

		  // Be here, it's possible that taker is paying something for nothing due to partially filled in last loop.
		  // In this case, we see it as filled and cancel it later
		  if( usd_receives.amount == 0 && maint_time > HARDFORK_CORE_184_TIME )
			 return 1;

		  if( before_core_hardfork_342 )
			 core_receives = usd_for_sale;
		  else
		  {
			 // The remaining amount in order `usd` would be too small,
			 //   so we should cull the order in fill_limit_order() below.
			 // The order would receive 0 even at `match_price`, so it would receive 0 at its own price,
			 //   so calling maybe_cull_small() will always cull it.
			 core_receives = usd_receives.multiply_and_round_up( match_price );
			 cull_taker = true;
		  }
	   }
	   else
	   {
		  //This line once read: assert( core_for_sale < usd_for_sale * match_price );
		  //This assert is not always true -- see trade_amount_equals_zero in operation_tests.cpp
		  //Although usd_for_sale is greater than core_for_sale * match_price, core_for_sale == usd_for_sale * match_price
		  //Removing the assert seems to be safe -- apparently no asset is created or destroyed.

		  // The maker won't be paying something for nothing, since if it would, it would have been cancelled already.
		  core_receives = core_for_sale * match_price; // round down, in favor of bigger order
		  if( before_core_hardfork_342 )
			 usd_receives = core_for_sale;
		  else
			 // The remaining amount in order `core` would be too small,
			 //   so the order will be culled in fill_limit_order() below
			 usd_receives = core_receives.multiply_and_round_up( match_price );
	   }

	   core_pays = usd_receives;
	   usd_pays  = core_receives;

	   if( before_core_hardfork_342 )
		  FC_ASSERT( usd_pays == usd.amount_for_sale() ||
					 core_pays == core.amount_for_sale() );

	   int result = 0;
	   result |= fill_limit_order( usd, usd_pays, usd_receives, cull_taker, match_price, false ); // the first param is taker
	   result |= fill_limit_order( core, core_pays, core_receives, true, match_price, true ) << 1; // the second param is maker
	   FC_ASSERT( result != 0 );
	   return result;
	}


.. code-block:: cpp 

	int database::match( const limit_order_object& bid, const call_order_object& ask, const price& match_price,
						 const price& feed_price, const uint16_t maintenance_collateral_ratio )
	{
	   FC_ASSERT( bid.sell_asset_id() == ask.debt_type() );
	   FC_ASSERT( bid.receive_asset_id() == ask.collateral_type() );
	   FC_ASSERT( bid.for_sale > 0 && ask.debt > 0 && ask.collateral > 0 );

	   auto maint_time = get_dynamic_global_properties().next_maintenance_time;
	   // TODO remove when we're sure it's always false
	   bool before_core_hardfork_184 = ( maint_time <= HARDFORK_CORE_184_TIME ); // something-for-nothing
	   // TODO remove when we're sure it's always false
	   bool before_core_hardfork_342 = ( maint_time <= HARDFORK_CORE_342_TIME ); // better rounding
	   // TODO remove when we're sure it's always false
	   bool before_core_hardfork_834 = ( maint_time <= HARDFORK_CORE_834_TIME ); // target collateral ratio option
	   if( before_core_hardfork_184 )
		  ilog( "match(limit,call) is called before hardfork core-184 at block #${block}", ("block",head_block_num()) );
	   if( before_core_hardfork_342 )
		  ilog( "match(limit,call) is called before hardfork core-342 at block #${block}", ("block",head_block_num()) );
	   if( before_core_hardfork_834 )
		  ilog( "match(limit,call) is called before hardfork core-834 at block #${block}", ("block",head_block_num()) );

	   bool cull_taker = false;

	   asset usd_for_sale = bid.amount_for_sale();
	   // TODO if we're sure `before_core_hardfork_834` is always false, remove the check
	   asset usd_to_buy   = ( before_core_hardfork_834 ?
							  ask.get_debt() :
							  asset( ask.get_max_debt_to_cover( match_price, feed_price, maintenance_collateral_ratio ),
									 ask.debt_type() ) );

	   asset call_pays, call_receives, order_pays, order_receives;
	   if( usd_to_buy > usd_for_sale )
	   {  // fill limit order
		  order_receives  = usd_for_sale * match_price; // round down here, in favor of call order

		  // Be here, it's possible that taker is paying something for nothing due to partially filled in last loop.
		  // In this case, we see it as filled and cancel it later
		  // TODO remove hardfork check when we're sure it's always after hard fork (but keep the zero amount check)
		  if( order_receives.amount == 0 && !before_core_hardfork_184 )
			 return 1;

		  if( before_core_hardfork_342 ) // TODO remove this "if" when we're sure it's always false (keep the code in else)
			 call_receives = usd_for_sale;
		  else
		  {
			 // The remaining amount in the limit order would be too small,
			 //   so we should cull the order in fill_limit_order() below.
			 // The order would receive 0 even at `match_price`, so it would receive 0 at its own price,
			 //   so calling maybe_cull_small() will always cull it.
			 call_receives = order_receives.multiply_and_round_up( match_price );
			 cull_taker = true;
		  }
	   }
	   else
	   {  // fill call order
		  call_receives  = usd_to_buy;
		  if( before_core_hardfork_342 ) // TODO remove this "if" when we're sure it's always false (keep the code in else)
		  {
			 order_receives = usd_to_buy * match_price; // round down here, in favor of call order
			 // TODO remove hardfork check when we're sure it's always after hard fork (but keep the zero amount check)
			 if( order_receives.amount == 0 && !before_core_hardfork_184 )
				return 1;
		  }
		  else // has hardfork core-342
			 order_receives = usd_to_buy.multiply_and_round_up( match_price ); // round up here, in favor of limit order
	   }

	   call_pays  = order_receives;
	   order_pays = call_receives;

	   int result = 0;
	   result |= fill_limit_order( bid, order_pays, order_receives, cull_taker, match_price, false ); // the limit order is taker
	   result |= fill_call_order( ask, call_pays, call_receives, match_price, true ) << 1;      // the call order is maker
	   // result can be 0 when call order has target_collateral_ratio option set.

	   return result;
	}


match (asset database)
---------------------------------------

.. code-block:: cpp 

	asset database::match( const call_order_object& call, 
						   const force_settlement_object& settle, 
						   const price& match_price,
						   asset max_settlement,
						   const price& fill_price )
	{ try {
	   FC_ASSERT(call.get_debt().asset_id == settle.balance.asset_id );
	   FC_ASSERT(call.debt > 0 && call.collateral > 0 && settle.balance.amount > 0);

	   auto maint_time = get_dynamic_global_properties().next_maintenance_time;
	   bool before_core_hardfork_342 = ( maint_time <= HARDFORK_CORE_342_TIME ); // better rounding

	   auto settle_for_sale = std::min(settle.balance, max_settlement);
	   auto call_debt = call.get_debt();

	   asset call_receives   = std::min(settle_for_sale, call_debt);
	   asset call_pays       = call_receives * match_price; // round down here, in favor of call order, for first check
															// TODO possible optimization: check need to round up or down first

	   // Be here, the call order may be paying nothing.
	   bool cull_settle_order = false; // whether need to cancel dust settle order
	   if( call_pays.amount == 0 )
	   {
		  if( maint_time > HARDFORK_CORE_184_TIME )
		  {
			 if( call_receives == call_debt ) // the call order is smaller than or equal to the settle order
			 {
				wlog( "Something for nothing issue (#184, variant C-1) handled at block #${block}", ("block",head_block_num()) );
				call_pays.amount = 1;
			 }
			 else
			 {
				if( call_receives == settle.balance ) // the settle order is smaller
				{
				   wlog( "Something for nothing issue (#184, variant C-2) handled at block #${block}", ("block",head_block_num()) );
				   cancel_settle_order( settle );
				}
				// else do nothing: neither order will be completely filled, perhaps due to max_settlement too small

				return asset( 0, settle.balance.asset_id );
			 }
		  }
		  else // TODO remove this warning after hard fork core-184
			 wlog( "Something for nothing issue (#184, variant C) occurred at block #${block}", ("block",head_block_num()) );
	   }
	   else // the call order is not paying nothing, but still possible it's paying more than minimum required due to rounding
	   {
		  if( !before_core_hardfork_342 )
		  {
			 if( call_receives == call_debt ) // the call order is smaller than or equal to the settle order
			 {
				call_pays = call_receives.multiply_and_round_up( match_price ); // round up here, in favor of settle order
				// be here, we should have: call_pays <= call_collateral
			 }
			 else
			 {
				// be here, call_pays has been rounded down

				// be here, we should have: call_pays <= call_collateral

				if( call_receives == settle.balance ) // the settle order will be completely filled, assuming we need to cull it
				   cull_settle_order = true;
				// else do nothing, since we can't cull the settle order

				call_receives = call_pays.multiply_and_round_up( match_price ); // round up here to mitigate rounding issue (core-342).
																				// It is important to understand here that the newly
																				// rounded up call_receives won't be greater than the
																				// old call_receives.

				if( call_receives == settle.balance ) // the settle order will be completely filled, no need to cull
				   cull_settle_order = false;
				// else do nothing, since we still need to cull the settle order or still can't cull the settle order
			 }
		  }
	   }

	   asset settle_pays     = call_receives;
	   asset settle_receives = call_pays;

	   /**
		*  If the least collateralized call position lacks sufficient
		*  collateral to cover at the match price then this indicates a black 
		*  swan event according to the price feed, but only the market 
		*  can trigger a black swan.  So now we must cancel the forced settlement
		*  object.
		*/
	   if( before_core_hardfork_342 )
	   {
		  auto call_collateral = call.get_collateral();
		  if( call_pays == call_collateral ) // TODO remove warning after hard fork core-342
			 wlog( "Incorrectly captured black swan event at block #${block}", ("block",head_block_num()) );
		  GRAPHENE_ASSERT( call_pays < call_collateral, black_swan_exception, "" );

		  assert( settle_pays == settle_for_sale || call_receives == call.get_debt() );
	   }
	   // else do nothing, since black swan event won't happen, and the assertion is no longer true

	   fill_call_order( call, call_pays, call_receives, fill_price, true ); // call order is maker
	   fill_settle_order( settle, settle_pays, settle_receives, fill_price, false ); // force settlement order is taker

	   if( cull_settle_order )
		  cancel_settle_order( settle );

	   return call_receives;
	} FC_CAPTURE_AND_RETHROW( (call)(settle)(match_price)(max_settlement) ) }

fill_limit_order
---------------------------------------

.. code-block:: cpp 

	bool database::fill_limit_order( const limit_order_object& order, const asset& pays, const asset& receives, bool cull_if_small,
							   const price& fill_price, const bool is_maker )
	{ try {
	   cull_if_small |= (head_block_time() < HARDFORK_555_TIME);

	   FC_ASSERT( order.amount_for_sale().asset_id == pays.asset_id );
	   FC_ASSERT( pays.asset_id != receives.asset_id );

	   const account_object& seller = order.seller(*this);
	   const asset_object& recv_asset = receives.asset_id(*this);

	   auto issuer_fees = pay_market_fees( recv_asset, receives );
	   pay_order( seller, receives - issuer_fees, pays );

	   assert( pays.asset_id != receives.asset_id );
	   push_applied_operation( fill_order_operation( order.id, order.seller, pays, receives, issuer_fees, fill_price, is_maker ) );

	   // conditional because cheap integer comparison may allow us to avoid two expensive modify() and object lookups
	   if( order.deferred_fee > 0 )
	   {
		  modify( seller.statistics(*this), [&]( account_statistics_object& statistics )
		  {
			 statistics.pay_fee( order.deferred_fee, get_global_properties().parameters.cashback_vesting_threshold );
		  } );
	   }

	   if( order.deferred_paid_fee.amount > 0 ) // implies head_block_time() > HARDFORK_CORE_604_TIME
	   {
		  const auto& fee_asset_dyn_data = order.deferred_paid_fee.asset_id(*this).dynamic_asset_data_id(*this);
		  modify( fee_asset_dyn_data, [&](asset_dynamic_data_object& addo) {
			 addo.accumulated_fees += order.deferred_paid_fee.amount;
		  });
	   }

	   if( pays == order.amount_for_sale() )
	   {
		  remove( order );
		  return true;
	   }
	   else
	   {
		  modify( order, [&]( limit_order_object& b ) {
								 b.for_sale -= pays.amount;
								 b.deferred_fee = 0;
								 b.deferred_paid_fee.amount = 0;
							  });
		  if( cull_if_small )
			 return maybe_cull_small_order( *this, order );
		  return false;
	   }
	} FC_CAPTURE_AND_RETHROW( (order)(pays)(receives) ) }


fill_call_order
---------------------------------------

.. code-block:: cpp 

	bool database::fill_call_order( const call_order_object& order, const asset& pays, const asset& receives,
									const price& fill_price, const bool is_maker )
	{ try {
	   //idump((pays)(receives)(order));
	   FC_ASSERT( order.get_debt().asset_id == receives.asset_id );
	   FC_ASSERT( order.get_collateral().asset_id == pays.asset_id );
	   FC_ASSERT( order.get_collateral() >= pays );

	   const asset_object& mia = receives.asset_id(*this);
	   FC_ASSERT( mia.is_market_issued() );

	   optional<asset> collateral_freed;
	   modify( order, [&]( call_order_object& o ){
				o.debt       -= receives.amount;
				o.collateral -= pays.amount;
				if( o.debt == 0 )
				{
				  collateral_freed = o.get_collateral();
				  o.collateral = 0;
				}
				else if( get_dynamic_global_properties().next_maintenance_time > HARDFORK_CORE_343_TIME )
				  o.call_price = price::call_price( o.get_debt(), o.get_collateral(),
									mia.bitasset_data(*this).current_feed.maintenance_collateral_ratio );
		   });

	   const asset_dynamic_data_object& mia_ddo = mia.dynamic_asset_data_id(*this);

	   modify( mia_ddo, [&]( asset_dynamic_data_object& ao ){
		   //idump((receives));
			ao.current_supply -= receives.amount;
		  });

	   const account_object& borrower = order.borrower(*this);
	   if( collateral_freed.valid() || pays.asset_id == asset_id_type() )
	   {
		  const account_statistics_object& borrower_statistics = borrower.statistics(*this);
		  if( collateral_freed.valid() )
			 adjust_balance(borrower.get_id(), *collateral_freed);

		  modify( borrower_statistics, [&]( account_statistics_object& b ){
				  if( collateral_freed.valid() && collateral_freed->amount > 0 && collateral_freed->asset_id == asset_id_type() )
					b.total_core_in_orders -= collateral_freed->amount;
				  if( pays.asset_id == asset_id_type() )
					b.total_core_in_orders -= pays.amount;

				  assert( b.total_core_in_orders >= 0 );
			   });
	   }

	   assert( pays.asset_id != receives.asset_id );
	   push_applied_operation( fill_order_operation( order.id, order.borrower, pays, receives,
													 asset(0, pays.asset_id), fill_price, is_maker ) );

	   if( collateral_freed.valid() )
		  remove( order );

	   return collateral_freed.valid();
	} FC_CAPTURE_AND_RETHROW( (order)(pays)(receives) ) }

	
fill_settle_order
---------------------------------------

.. code-block:: cpp 

	bool database::fill_settle_order( const force_settlement_object& settle, const asset& pays, const asset& receives,
									  const price& fill_price, const bool is_maker )
	{ try {
	   bool filled = false;

	   auto issuer_fees = pay_market_fees(get(receives.asset_id), receives);

	   if( pays < settle.balance )
	   {
		  modify(settle, [&pays](force_settlement_object& s) {
			 s.balance -= pays;
		  });
		  filled = false;
	   } else {
		  filled = true;
	   }
	   adjust_balance(settle.owner, receives - issuer_fees);

	   assert( pays.asset_id != receives.asset_id );
	   push_applied_operation( fill_order_operation( settle.id, settle.owner, pays, receives, issuer_fees, fill_price, is_maker ) );

	   if (filled)
		  remove(settle);

	   return filled;
	} FC_CAPTURE_AND_RETHROW( (settle)(pays)(receives) ) }

	
check_call_orders
---------------------------------------

.. code-block:: cpp 

	/**
	 *  Starting with the least collateralized orders, fill them if their
	 *  call price is above the max(lowest bid,call_limit).
	 *
	 *  This method will return true if it filled a short or limit
	 *
	 *  @param mia - the market issued asset that should be called.
	 *  @param enable_black_swan - when adjusting collateral, triggering a black swan is invalid and will throw
	 *                             if enable_black_swan is not set to true.
	 *  @param for_new_limit_order - true if this function is called when matching call orders with a new limit order
	 *
	 *  @return true if a margin call was executed.
	 */
	bool database::check_call_orders( const asset_object& mia, bool enable_black_swan, bool for_new_limit_order,
									  const asset_bitasset_data_object* bitasset_ptr )
	{ try {
		const auto& dyn_prop = get_dynamic_global_properties();
		auto maint_time = dyn_prop.next_maintenance_time;
		if( for_new_limit_order )
		   FC_ASSERT( maint_time <= HARDFORK_CORE_625_TIME ); // `for_new_limit_order` is only true before HF 338 / 625

		if( !mia.is_market_issued() ) return false;

		const asset_bitasset_data_object& bitasset = ( bitasset_ptr ? *bitasset_ptr : mia.bitasset_data(*this) );

		if( check_for_blackswan( mia, enable_black_swan, &bitasset ) )
		   return false;

		if( bitasset.is_prediction_market ) return false;
		if( bitasset.current_feed.settlement_price.is_null() ) return false;

		const call_order_index& call_index = get_index_type<call_order_index>();
		const auto& call_price_index = call_index.indices().get<by_price>();

		const limit_order_index& limit_index = get_index_type<limit_order_index>();
		const auto& limit_price_index = limit_index.indices().get<by_price>();

		// looking for limit orders selling the most USD for the least CORE
		auto max_price = price::max( mia.id, bitasset.options.short_backing_asset );
		// stop when limit orders are selling too little USD for too much CORE
		auto min_price = bitasset.current_feed.max_short_squeeze_price();

		assert( max_price.base.asset_id == min_price.base.asset_id );
		// NOTE limit_price_index is sorted from greatest to least
		auto limit_itr = limit_price_index.lower_bound( max_price );
		auto limit_end = limit_price_index.upper_bound( min_price );

		if( limit_itr == limit_end )
		   return false;

		auto call_min = price::min( bitasset.options.short_backing_asset, mia.id );
		auto call_max = price::max( bitasset.options.short_backing_asset, mia.id );
		auto call_itr = call_price_index.lower_bound( call_min );
		auto call_end = call_price_index.upper_bound( call_max );

		bool filled_limit = false;
		bool margin_called = false;

		auto head_time = head_block_time();
		auto head_num = head_block_num();

		bool before_hardfork_615 = ( head_time < HARDFORK_615_TIME );
		bool after_hardfork_436 = ( head_time > HARDFORK_436_TIME );

		bool before_core_hardfork_184 = ( maint_time <= HARDFORK_CORE_184_TIME ); // something-for-nothing
		bool before_core_hardfork_342 = ( maint_time <= HARDFORK_CORE_342_TIME ); // better rounding
		bool before_core_hardfork_343 = ( maint_time <= HARDFORK_CORE_343_TIME ); // update call_price after partially filled
		bool before_core_hardfork_453 = ( maint_time <= HARDFORK_CORE_453_TIME ); // multiple matching issue
		bool before_core_hardfork_606 = ( maint_time <= HARDFORK_CORE_606_TIME ); // feed always trigger call
		bool before_core_hardfork_834 = ( maint_time <= HARDFORK_CORE_834_TIME ); // target collateral ratio option

		while( !check_for_blackswan( mia, enable_black_swan, &bitasset ) && call_itr != call_end )
		{
		   bool  filled_call      = false;
		   price match_price;
		   asset usd_for_sale;
		   if( limit_itr != limit_end )
		   {
			  assert( limit_itr != limit_price_index.end() );
			  match_price      = limit_itr->sell_price;
			  usd_for_sale     = limit_itr->amount_for_sale();
		   }
		   else return margin_called;

		   match_price.validate();

		   // Feed protected (don't call if CR>MCR) https://github.com/cryptonomex/graphene/issues/436
		   if( after_hardfork_436 && ( bitasset.current_feed.settlement_price > ~call_itr->call_price ) )
			  return margin_called;

		   // Old rule: margin calls can only buy high https://github.com/bitshares/bitshares-core/issues/606
		   if( before_core_hardfork_606 && match_price > ~call_itr->call_price )
			  return margin_called;

		   margin_called = true;

		   auto usd_to_buy   = call_itr->get_debt();

		   if( usd_to_buy * match_price > call_itr->get_collateral() )
		   {
			  elog( "black swan detected on asset ${symbol} (${id}) at block ${b}",
					("id",mia.id)("symbol",mia.symbol)("b",head_num) );
			  edump((enable_black_swan));
			  FC_ASSERT( enable_black_swan );
			  globally_settle_asset(mia, bitasset.current_feed.settlement_price );
			  return true;
		   }

		   if( !before_core_hardfork_834 )
			  usd_to_buy.amount = call_itr->get_max_debt_to_cover( match_price,
																   bitasset.current_feed.settlement_price,
																   bitasset.current_feed.maintenance_collateral_ratio );

		   asset call_pays, call_receives, order_pays, order_receives;
		   if( usd_to_buy > usd_for_sale )
		   {  // fill order
			  order_receives  = usd_for_sale * match_price; // round down, in favor of call order

			  // Be here, the limit order won't be paying something for nothing, since if it would, it would have
			  //   been cancelled elsewhere already (a maker limit order won't be paying something for nothing):
			  // * after hard fork core-625, the limit order will be always a maker if entered this function;
			  // * before hard fork core-625,
			  //   * when the limit order is a taker, it could be paying something for nothing only when
			  //     the call order is smaller and is too small
			  //   * when the limit order is a maker, it won't be paying something for nothing
			  if( order_receives.amount == 0 ) // TODO this should not happen. remove the warning after confirmed
			  {
				 if( before_core_hardfork_184 )
					wlog( "Something for nothing issue (#184, variant D-1) occurred at block #${block}", ("block",head_num) );
				 else
					wlog( "Something for nothing issue (#184, variant D-2) occurred at block #${block}", ("block",head_num) );
			  }

			  if( before_core_hardfork_342 )
				 call_receives = usd_for_sale;
			  else
				 // The remaining amount in the limit order would be too small,
				 //   so we should cull the order in fill_limit_order() below.
				 // The order would receive 0 even at `match_price`, so it would receive 0 at its own price,
				 //   so calling maybe_cull_small() will always cull it.
				 call_receives = order_receives.multiply_and_round_up( match_price );

			  filled_limit = true;

		   } else { // fill call
			  call_receives  = usd_to_buy;

			  if( before_core_hardfork_342 )
			  {
				 order_receives = usd_to_buy * match_price; // round down, in favor of call order

				 // Be here, the limit order would be paying something for nothing
				 if( order_receives.amount == 0 ) // TODO remove warning after hard fork core-342
					wlog( "Something for nothing issue (#184, variant D) occurred at block #${block}", ("block",head_num) );
			  }
			  else
				 order_receives = usd_to_buy.multiply_and_round_up( match_price ); // round up, in favor of limit order

			  filled_call    = true; // this is safe, since BSIP38 (hard fork core-834) depends on BSIP31 (hard fork core-343)

			  if( usd_to_buy == usd_for_sale )
				 filled_limit = true;
			  else if( filled_limit && maint_time <= HARDFORK_CORE_453_TIME ) // TODO remove warning after hard fork core-453
			  {
				 wlog( "Multiple limit match problem (issue 453) occurred at block #${block}", ("block",head_num) );
				 if( before_hardfork_615 )
					_issue_453_affected_assets.insert( bitasset.asset_id );
			  }
		   }

		   call_pays  = order_receives;
		   order_pays = call_receives;

		   auto old_call_itr = call_itr;
		   if( filled_call && before_core_hardfork_343 )
			  ++call_itr;
		   // when for_new_limit_order is true, the call order is maker, otherwise the call order is taker
		   fill_call_order(*old_call_itr, call_pays, call_receives, match_price, for_new_limit_order );
		   if( !before_core_hardfork_343 )
			  call_itr = call_price_index.lower_bound( call_min );

		   auto next_limit_itr = std::next( limit_itr );
		   // when for_new_limit_order is true, the limit order is taker, otherwise the limit order is maker
		   bool really_filled = fill_limit_order( *limit_itr, order_pays, order_receives, true, match_price, !for_new_limit_order );
		   if( really_filled || ( filled_limit && before_core_hardfork_453 ) )
			  limit_itr = next_limit_itr;

		} // while call_itr != call_end

		return margin_called;
	} FC_CAPTURE_AND_RETHROW() }

pay_order
---------------------------------------

.. code-block:: cpp 

	void database::pay_order( const account_object& receiver, const asset& receives, const asset& pays )
	{
	   const auto& balances = receiver.statistics(*this);
	   modify( balances, [&]( account_statistics_object& b ){
			 if( pays.asset_id == asset_id_type() )
			 {
				b.total_core_in_orders -= pays.amount;
			 }
	   });
	   adjust_balance(receiver.get_id(), receives);
	}


calculate_market_fee
---------------------------------------

.. code-block:: cpp 

	asset database::calculate_market_fee( const asset_object& trade_asset, const asset& trade_amount )
	{
	   assert( trade_asset.id == trade_amount.asset_id );

	   if( !trade_asset.charges_market_fees() )
		  return trade_asset.amount(0);
	   if( trade_asset.options.market_fee_percent == 0 )
		  return trade_asset.amount(0);

	   fc::uint128 a(trade_amount.amount.value);
	   a *= trade_asset.options.market_fee_percent;
	   a /= GRAPHENE_100_PERCENT;
	   asset percent_fee = trade_asset.amount(a.to_uint64());

	   if( percent_fee.amount > trade_asset.options.max_market_fee )
		  percent_fee.amount = trade_asset.options.max_market_fee;

	   return percent_fee;
	}

pay_market_fees
---------------------------------------

.. code-block:: cpp 

	asset database::pay_market_fees( const asset_object& recv_asset, const asset& receives )
	{
	   auto issuer_fees = calculate_market_fee( recv_asset, receives );
	   assert(issuer_fees <= receives );

	   //Don't dirty undo state if not actually collecting any fees
	   if( issuer_fees.amount > 0 )
	   {
		  const auto& recv_dyn_data = recv_asset.dynamic_asset_data_id(*this);
		  modify( recv_dyn_data, [&]( asset_dynamic_data_object& obj ){
					   //idump((issuer_fees));
			 obj.accumulated_fees += issuer_fees.amount;
		  });
	   }

	   return issuer_fees;
	}



-------------------

|


db_notify
================================



get_impacted_account_visitor
---------------------------------------

.. code-block:: cpp 

	struct get_impacted_account_visitor
	{
	   flat_set<account_id_type>& _impacted;
	   get_impacted_account_visitor( flat_set<account_id_type>& impact ):_impacted(impact) {}
	   typedef void result_type;

	   void operator()( const transfer_operation& op )
	   {
		  _impacted.insert( op.to );
		  _impacted.insert( op.fee_payer() ); // from
	   }
	   void operator()( const asset_claim_fees_operation& op )
	   {
		  _impacted.insert( op.fee_payer() ); // issuer
	   }
	   void operator()( const asset_claim_pool_operation& op )
	   {
		  _impacted.insert( op.fee_payer() ); // issuer
	   }
	   void operator()( const limit_order_create_operation& op )
	   {
		  _impacted.insert( op.fee_payer() ); // seller
	   }
	   void operator()( const limit_order_cancel_operation& op )
	   {
		  _impacted.insert( op.fee_payer() ); // fee_paying_account
	   }
	   void operator()( const call_order_update_operation& op )
	   {
		  _impacted.insert( op.fee_payer() ); // funding_account
	   }
	   void operator()( const bid_collateral_operation& op )
	   {
		  _impacted.insert( op.fee_payer() ); // bidder
	   }
	   void operator()( const fill_order_operation& op )
	   {
		  _impacted.insert( op.fee_payer() ); // account_id
	   }
	   void operator()( const execute_bid_operation& op )
	   {
		  _impacted.insert( op.fee_payer() ); // bidder
	   }
	   void operator()( const account_create_operation& op )
	   {
		  _impacted.insert( op.fee_payer() ); // registrar
		  _impacted.insert( op.referrer );
		  add_authority_accounts( _impacted, op.owner );
		  add_authority_accounts( _impacted, op.active );
	   }
	   void operator()( const account_update_operation& op )
	   {
		  _impacted.insert( op.fee_payer() ); // account
		  if( op.owner )
			 add_authority_accounts( _impacted, *(op.owner) );
		  if( op.active )
			 add_authority_accounts( _impacted, *(op.active) );
	   }
	   void operator()( const account_whitelist_operation& op )
	   {
		  _impacted.insert( op.fee_payer() ); // authorizing_account
		  _impacted.insert( op.account_to_list );
	   }
	   void operator()( const account_upgrade_operation& op )
	   {
		  _impacted.insert( op.fee_payer() ); // account_to_upgrade
	   }
	   void operator()( const account_transfer_operation& op )
	   {
		  _impacted.insert( op.fee_payer() ); // account_id
	   }
	   void operator()( const asset_create_operation& op )
	   {
		  _impacted.insert( op.fee_payer() ); // issuer
	   }
	   void operator()( const asset_update_operation& op )
	   {
		  _impacted.insert( op.fee_payer() ); // issuer
		  if( op.new_issuer )
			 _impacted.insert( *(op.new_issuer) );
	   }
	   void operator()( const asset_update_issuer_operation& op )
	   {
		  _impacted.insert( op.fee_payer() ); // issuer
		  _impacted.insert( op.new_issuer );
	   }
	   void operator()( const asset_update_bitasset_operation& op )
	   {
		  _impacted.insert( op.fee_payer() ); // issuer
	   }
	   void operator()( const asset_update_feed_producers_operation& op )
	   {
		  _impacted.insert( op.fee_payer() ); // issuer
	   }
	   void operator()( const asset_issue_operation& op )
	   {
		  _impacted.insert( op.fee_payer() ); // issuer
		  _impacted.insert( op.issue_to_account );
	   }
	   void operator()( const asset_reserve_operation& op )
	   {
		  _impacted.insert( op.fee_payer() ); // payer
	   }
	   void operator()( const asset_fund_fee_pool_operation& op )
	   {
		  _impacted.insert( op.fee_payer() ); // from_account
	   }
	   void operator()( const asset_settle_operation& op )
	   {
		  _impacted.insert( op.fee_payer() ); // account
	   }
	   void operator()( const asset_global_settle_operation& op )
	   {
		  _impacted.insert( op.fee_payer() ); // issuer
	   }
	   void operator()( const asset_publish_feed_operation& op )
	   {
		  _impacted.insert( op.fee_payer() ); // publisher
	   }
	   void operator()( const witness_create_operation& op )
	   {
		  _impacted.insert( op.fee_payer() ); // witness_account
	   }
	   void operator()( const witness_update_operation& op )
	   {
		  _impacted.insert( op.fee_payer() ); // witness_account
	   }
	   void operator()( const proposal_create_operation& op )
	   {
		  _impacted.insert( op.fee_payer() ); // fee_paying_account
		  vector<authority> other;
		  for( const auto& proposed_op : op.proposed_ops )
			 operation_get_required_authorities( proposed_op.op, _impacted, _impacted, other );
		  for( auto& o : other )
			 add_authority_accounts( _impacted, o );
	   }
	   void operator()( const proposal_update_operation& op )
	   {
		  _impacted.insert( op.fee_payer() ); // fee_paying_account
	   }
	   void operator()( const proposal_delete_operation& op )
	   {
		  _impacted.insert( op.fee_payer() ); // fee_paying_account
	   }
	   void operator()( const withdraw_permission_create_operation& op )
	   {
		  _impacted.insert( op.fee_payer() ); // withdraw_from_account
		  _impacted.insert( op.authorized_account );
	   }
	   void operator()( const withdraw_permission_update_operation& op )
	   {
		  _impacted.insert( op.fee_payer() ); // withdraw_from_account
		  _impacted.insert( op.authorized_account );
	   }
	   void operator()( const withdraw_permission_claim_operation& op )
	   {
		  _impacted.insert( op.fee_payer() ); // withdraw_to_account
		  _impacted.insert( op.withdraw_from_account );
	   }
	   void operator()( const withdraw_permission_delete_operation& op )
	   {
		  _impacted.insert( op.fee_payer() ); // withdraw_from_account
		  _impacted.insert( op.authorized_account );
	   }
	   void operator()( const committee_member_create_operation& op )
	   {
		  _impacted.insert( op.fee_payer() ); // committee_member_account
	   }
	   void operator()( const committee_member_update_operation& op )
	   {
		  _impacted.insert( op.fee_payer() ); // committee_member_account
	   }
	   void operator()( const committee_member_update_global_parameters_operation& op )
	   {
		  _impacted.insert( op.fee_payer() ); // account_id_type()
	   }
	   void operator()( const vesting_balance_create_operation& op )
	   {
		  _impacted.insert( op.fee_payer() ); // creator
		  _impacted.insert( op.owner );
	   }
	   void operator()( const vesting_balance_withdraw_operation& op )
	   {
		  _impacted.insert( op.fee_payer() ); // owner
	   }
	   void operator()( const worker_create_operation& op )
	   {
		  _impacted.insert( op.fee_payer() ); // owner
	   }
	   void operator()( const custom_operation& op )
	   {
		  _impacted.insert( op.fee_payer() ); // payer
	   }
	   void operator()( const assert_operation& op )
	   {
		  _impacted.insert( op.fee_payer() ); // fee_paying_account
	   }
	   void operator()( const balance_claim_operation& op )
	   {
		  _impacted.insert( op.fee_payer() ); // deposit_to_account
	   }
	   void operator()( const override_transfer_operation& op )
	   {
		  _impacted.insert( op.to );
		  _impacted.insert( op.from );
		  _impacted.insert( op.fee_payer() ); // issuer
	   }
	   void operator()( const transfer_to_blind_operation& op )
	   {
		  _impacted.insert( op.fee_payer() ); // from
		  for( const auto& out : op.outputs )
			 add_authority_accounts( _impacted, out.owner );
	   }
	   void operator()( const blind_transfer_operation& op )
	   {
		  _impacted.insert( op.fee_payer() ); // GRAPHENE_TEMP_ACCOUNT
		  for( const auto& in : op.inputs )
			 add_authority_accounts( _impacted, in.owner );
		  for( const auto& out : op.outputs )
			 add_authority_accounts( _impacted, out.owner );
	   }
	   void operator()( const transfer_from_blind_operation& op )
	   {
		  _impacted.insert( op.fee_payer() ); // GRAPHENE_TEMP_ACCOUNT
		  _impacted.insert( op.to );
		  for( const auto& in : op.inputs )
			 add_authority_accounts( _impacted, in.owner );
	   }
	   void operator()( const asset_settle_cancel_operation& op )
	   {
		  _impacted.insert( op.fee_payer() ); // account
	   }
	   void operator()( const fba_distribute_operation& op )
	   {
		  _impacted.insert( op.fee_payer() ); // account_id
	   }
	};


operation_get_impacted_accounts
---------------------------------------

.. code-block:: cpp 

	void graphene::chain::operation_get_impacted_accounts( const operation& op, flat_set<account_id_type>& result )
	{
	  get_impacted_account_visitor vtor = get_impacted_account_visitor( result );
	  op.visit( vtor );
	}


transaction_get_impacted_accounts
---------------------------------------

.. code-block:: cpp 

	void graphene::chain::transaction_get_impacted_accounts( const transaction& tx, flat_set<account_id_type>& result )
	{
	  for( const auto& op : tx.operations )
		operation_get_impacted_accounts( op, result );
	}


get_relevant_accounts
---------------------------------------

.. code-block:: cpp 

	void get_relevant_accounts( const object* obj, flat_set<account_id_type>& accounts )
	{
	   if( obj->id.space() == protocol_ids )
	   {
		  switch( (object_type)obj->id.type() )
		  {
			case null_object_type:
			case base_object_type:
			case OBJECT_TYPE_COUNT:
			   return;
			case account_object_type:{
			   accounts.insert( obj->id );
			   break;
			} case asset_object_type:{
			   const auto& aobj = dynamic_cast<const asset_object*>(obj);
			   FC_ASSERT( aobj != nullptr );
			   accounts.insert( aobj->issuer );
			   break;
			} case force_settlement_object_type:{
			   const auto& aobj = dynamic_cast<const force_settlement_object*>(obj);
			   FC_ASSERT( aobj != nullptr );
			   accounts.insert( aobj->owner );
			   break;
			} case committee_member_object_type:{
			   const auto& aobj = dynamic_cast<const committee_member_object*>(obj);
			   FC_ASSERT( aobj != nullptr );
			   accounts.insert( aobj->committee_member_account );
			   break;
			} case witness_object_type:{
			   const auto& aobj = dynamic_cast<const witness_object*>(obj);
			   FC_ASSERT( aobj != nullptr );
			   accounts.insert( aobj->witness_account );
			   break;
			} case limit_order_object_type:{
			   const auto& aobj = dynamic_cast<const limit_order_object*>(obj);
			   FC_ASSERT( aobj != nullptr );
			   accounts.insert( aobj->seller );
			   break;
			} case call_order_object_type:{
			   const auto& aobj = dynamic_cast<const call_order_object*>(obj);
			   FC_ASSERT( aobj != nullptr );
			   accounts.insert( aobj->borrower );
			   break;
			} case custom_object_type:{
			  break;
			} case proposal_object_type:{
			   const auto& aobj = dynamic_cast<const proposal_object*>(obj);
			   FC_ASSERT( aobj != nullptr );
			   transaction_get_impacted_accounts( aobj->proposed_transaction, accounts );
			   break;
			} case operation_history_object_type:{
			   const auto& aobj = dynamic_cast<const operation_history_object*>(obj);
			   FC_ASSERT( aobj != nullptr );
			   operation_get_impacted_accounts( aobj->op, accounts );
			   break;
			} case withdraw_permission_object_type:{
			   const auto& aobj = dynamic_cast<const withdraw_permission_object*>(obj);
			   FC_ASSERT( aobj != nullptr );
			   accounts.insert( aobj->withdraw_from_account );
			   accounts.insert( aobj->authorized_account );
			   break;
			} case vesting_balance_object_type:{
			   const auto& aobj = dynamic_cast<const vesting_balance_object*>(obj);
			   FC_ASSERT( aobj != nullptr );
			   accounts.insert( aobj->owner );
			   break;
			} case worker_object_type:{
			   const auto& aobj = dynamic_cast<const worker_object*>(obj);
			   FC_ASSERT( aobj != nullptr );
			   accounts.insert( aobj->worker_account );
			   break;
			} case balance_object_type:{
			   /** these are free from any accounts */
			   break;
			}
		  }
	   }
	   else if( obj->id.space() == implementation_ids )
	   {
		  switch( (impl_object_type)obj->id.type() )
		  {
				 case impl_global_property_object_type:
				  break;
				 case impl_dynamic_global_property_object_type:
				  break;
				 case impl_reserved0_object_type:
				  break;
				 case impl_asset_dynamic_data_type:
				  break;
				 case impl_asset_bitasset_data_type:
				  break;
				 case impl_account_balance_object_type:{
				  const auto& aobj = dynamic_cast<const account_balance_object*>(obj);
				  FC_ASSERT( aobj != nullptr );
				  accounts.insert( aobj->owner );
				  break;
			   } case impl_account_statistics_object_type:{
				  const auto& aobj = dynamic_cast<const account_statistics_object*>(obj);
				  FC_ASSERT( aobj != nullptr );
				  accounts.insert( aobj->owner );
				  break;
			   } case impl_transaction_object_type:{
				  const auto& aobj = dynamic_cast<const transaction_object*>(obj);
				  FC_ASSERT( aobj != nullptr );
				  transaction_get_impacted_accounts( aobj->trx, accounts );
				  break;
			   } case impl_blinded_balance_object_type:{
				  const auto& aobj = dynamic_cast<const blinded_balance_object*>(obj);
				  FC_ASSERT( aobj != nullptr );
				  for( const auto& a : aobj->owner.account_auths )
					accounts.insert( a.first );
				  break;
			   } case impl_block_summary_object_type:
				  break;
				 case impl_account_transaction_history_object_type: {
				  const auto& aobj = dynamic_cast<const account_transaction_history_object*>(obj);
				  FC_ASSERT( aobj != nullptr );
				  accounts.insert( aobj->account );
				  break;
			   } case impl_chain_property_object_type:
				  break;
				 case impl_witness_schedule_object_type:
				  break;
				 case impl_budget_record_object_type:
				  break;
				 case impl_special_authority_object_type:
				  break;
				 case impl_buyback_object_type:
				  break;
				 case impl_fba_accumulator_object_type:
				  break;
				 case impl_collateral_bid_object_type:{
				  const auto& aobj = dynamic_cast<const collateral_bid_object*>(obj);
				  FC_ASSERT( aobj != nullptr );
				  accounts.insert( aobj->bidder );
				  break;
			   }
		  }
	   }
	} // end get_relevant_accounts( const object* obj, flat_set<account_id_type>& accounts )


notify_applied_block
---------------------------------------

.. code-block:: cpp 

	void database::notify_applied_block( const signed_block& block )
	{
	   GRAPHENE_TRY_NOTIFY( applied_block, block )
	}


notify_on_pending_transaction
---------------------------------------

.. code-block:: cpp 

	void database::notify_on_pending_transaction( const signed_transaction& tx )
	{
	   GRAPHENE_TRY_NOTIFY( on_pending_transaction, tx )
	}


notify_changed_objects
---------------------------------------

.. code-block:: cpp 

	void database::notify_changed_objects()
	{ try {
	   if( _undo_db.enabled() ) 
	   {
		  const auto& head_undo = _undo_db.head();

		  // New
		  if( !new_objects.empty() )
		  {
			vector<object_id_type> new_ids;  new_ids.reserve(head_undo.new_ids.size());
			flat_set<account_id_type> new_accounts_impacted;
			for( const auto& item : head_undo.new_ids )
			{
			  new_ids.push_back(item);
			  auto obj = find_object(item);
			  if(obj != nullptr)
				get_relevant_accounts(obj, new_accounts_impacted);
			}

			if( new_ids.size() )
			   GRAPHENE_TRY_NOTIFY( new_objects, new_ids, new_accounts_impacted)
		  }

		  // Changed
		  if( !changed_objects.empty() )
		  {
			vector<object_id_type> changed_ids;  changed_ids.reserve(head_undo.old_values.size());
			flat_set<account_id_type> changed_accounts_impacted;
			for( const auto& item : head_undo.old_values )
			{
			  changed_ids.push_back(item.first);
			  get_relevant_accounts(item.second.get(), changed_accounts_impacted);
			}

			if( changed_ids.size() )
			   GRAPHENE_TRY_NOTIFY( changed_objects, changed_ids, changed_accounts_impacted)
		  }

		  // Removed
		  if( !removed_objects.empty() )
		  {
			vector<object_id_type> removed_ids; removed_ids.reserve( head_undo.removed.size() );
			vector<const object*> removed; removed.reserve( head_undo.removed.size() );
			flat_set<account_id_type> removed_accounts_impacted;
			for( const auto& item : head_undo.removed )
			{
			  removed_ids.emplace_back( item.first );
			  auto obj = item.second.get();
			  removed.emplace_back( obj );
			  get_relevant_accounts(obj, removed_accounts_impacted);
			}

			if( removed_ids.size() )
			   GRAPHENE_TRY_NOTIFY( removed_objects, removed_ids, removed, removed_accounts_impacted)
		  }
	   }
	} FC_CAPTURE_AND_LOG( (0) ) }









-------------------

|

db_update
================================


update_global_dynamic_data
---------------------------------------

.. code-block:: cpp 

	void database::update_global_dynamic_data( const signed_block& b, const uint32_t missed_blocks )
	{
	   const dynamic_global_property_object& _dgp = get_dynamic_global_properties();

	   // dynamic global properties updating
	   modify( _dgp, [&b,this,missed_blocks]( dynamic_global_property_object& dgp ){
		  const uint32_t block_num = b.block_num();
		  if( BOOST_UNLIKELY( block_num == 1 ) )
			 dgp.recently_missed_count = 0;
		  else if( _checkpoints.size() && _checkpoints.rbegin()->first >= block_num )
			 dgp.recently_missed_count = 0;
		  else if( missed_blocks )
			 dgp.recently_missed_count += GRAPHENE_RECENTLY_MISSED_COUNT_INCREMENT*missed_blocks;
		  else if( dgp.recently_missed_count > GRAPHENE_RECENTLY_MISSED_COUNT_INCREMENT )
			 dgp.recently_missed_count -= GRAPHENE_RECENTLY_MISSED_COUNT_DECREMENT;
		  else if( dgp.recently_missed_count > 0 )
			 dgp.recently_missed_count--;

		  dgp.head_block_number = block_num;
		  dgp.head_block_id = b.id();
		  dgp.time = b.timestamp;
		  dgp.current_witness = b.witness;
		  dgp.recent_slots_filled = (
			   (dgp.recent_slots_filled << 1)
			   + 1) << missed_blocks;
		  dgp.current_aslot += missed_blocks+1;
	   });

	   if( !(get_node_properties().skip_flags & skip_undo_history_check) )
	   {
		  GRAPHENE_ASSERT( _dgp.head_block_number - _dgp.last_irreversible_block_num  < GRAPHENE_MAX_UNDO_HISTORY, undo_database_exception,
					 "The database does not have enough undo history to support a blockchain with so many missed blocks. "
					 "Please add a checkpoint if you would like to continue applying blocks beyond this point.",
					 ("last_irreversible_block_num",_dgp.last_irreversible_block_num)("head", _dgp.head_block_number)
					 ("recently_missed",_dgp.recently_missed_count)("max_undo",GRAPHENE_MAX_UNDO_HISTORY) );
	   }

	   _undo_db.set_max_size( _dgp.head_block_number - _dgp.last_irreversible_block_num + 1 );
	   _fork_db.set_max_size( _dgp.head_block_number - _dgp.last_irreversible_block_num + 1 );
	}


update_signing_witness
---------------------------------------

.. code-block:: cpp 

	void database::update_signing_witness(const witness_object& signing_witness, const signed_block& new_block)
	{
	   const global_property_object& gpo = get_global_properties();
	   const dynamic_global_property_object& dpo = get_dynamic_global_properties();
	   uint64_t new_block_aslot = dpo.current_aslot + get_slot_at_time( new_block.timestamp );

	   share_type witness_pay = std::min( gpo.parameters.witness_pay_per_block, dpo.witness_budget );

	   modify( dpo, [&]( dynamic_global_property_object& _dpo )
	   {
		  _dpo.witness_budget -= witness_pay;
	   } );

	   deposit_witness_pay( signing_witness, witness_pay );

	   modify( signing_witness, [&]( witness_object& _wit )
	   {
		  _wit.last_aslot = new_block_aslot;
		  _wit.last_confirmed_block_num = new_block.block_num();
	   } );
	}


update_last_irreversible_block
---------------------------------------

.. code-block:: cpp 

	void database::update_last_irreversible_block()
	{
	   const global_property_object& gpo = get_global_properties();
	   const dynamic_global_property_object& dpo = get_dynamic_global_properties();

	   // TODO for better performance, move this to db_maint, because only need to do it once per maintenance interval
	   vector< const witness_object* > wit_objs;
	   wit_objs.reserve( gpo.active_witnesses.size() );
	   for( const witness_id_type& wid : gpo.active_witnesses )
		  wit_objs.push_back( &(wid(*this)) );

	   static_assert( GRAPHENE_IRREVERSIBLE_THRESHOLD > 0, "irreversible threshold must be nonzero" );

	   // 1 1 1 2 2 2 2 2 2 2 -> 2     .7*10 = 7
	   // 1 1 1 1 1 1 1 2 2 2 -> 1
	   // 3 3 3 3 3 3 3 3 3 3 -> 3

	   size_t offset = ((GRAPHENE_100_PERCENT - GRAPHENE_IRREVERSIBLE_THRESHOLD) * wit_objs.size() / GRAPHENE_100_PERCENT);

	   std::nth_element( wit_objs.begin(), wit_objs.begin() + offset, wit_objs.end(),
		  []( const witness_object* a, const witness_object* b )
		  {
			 return a->last_confirmed_block_num < b->last_confirmed_block_num;
		  } );

	   uint32_t new_last_irreversible_block_num = wit_objs[offset]->last_confirmed_block_num;

	   if( new_last_irreversible_block_num > dpo.last_irreversible_block_num )
	   {
		  modify( dpo, [&]( dynamic_global_property_object& _dpo )
		  {
			 _dpo.last_irreversible_block_num = new_last_irreversible_block_num;
		  } );
	   }
	}


clear_expired_transactions
---------------------------------------

.. code-block:: cpp 

	void database::clear_expired_transactions()
	{ try {
	   //Look for expired transactions in the deduplication list, and remove them.
	   //Transactions must have expired by at least two forking windows in order to be removed.
	   auto& transaction_idx = static_cast<transaction_index&>(get_mutable_index(implementation_ids, impl_transaction_object_type));
	   const auto& dedupe_index = transaction_idx.indices().get<by_expiration>();
	   while( (!dedupe_index.empty()) && (head_block_time() > dedupe_index.begin()->trx.expiration) )
		  transaction_idx.remove(*dedupe_index.begin());
	} FC_CAPTURE_AND_RETHROW() }


clear_expired_proposals
---------------------------------------

.. code-block:: cpp 

	void database::clear_expired_proposals()
	{
	   const auto& proposal_expiration_index = get_index_type<proposal_index>().indices().get<by_expiration>();
	   while( !proposal_expiration_index.empty() && proposal_expiration_index.begin()->expiration_time <= head_block_time() )
	   {
		  const proposal_object& proposal = *proposal_expiration_index.begin();
		  processed_transaction result;
		  try {
			 if( proposal.is_authorized_to_execute(*this) )
			 {
				result = push_proposal(proposal);
				//TODO: Do something with result so plugins can process it.
				continue;
			 }
		  } catch( const fc::exception& e ) {
			 elog("Failed to apply proposed transaction on its expiration. Deleting it.\n${proposal}\n${error}",
				  ("proposal", proposal)("error", e.to_detail_string()));
		  }
		  remove(proposal);
	   }
	}


check_for_blackswan
---------------------------------------

.. code-block:: cpp 

	/**
	 *  let HB = the highest bid for the collateral  (aka who will pay the most DEBT for the least collateral)
	 *  let SP = current median feed's Settlement Price 
	 *  let LC = the least collateralized call order's swan price (debt/collateral)
	 *
	 *  If there is no valid price feed or no bids then there is no black swan.
	 *
	 *  A black swan occurs if MAX(HB,SP) <= LC
	 */
	bool database::check_for_blackswan( const asset_object& mia, bool enable_black_swan,
										const asset_bitasset_data_object* bitasset_ptr )
	{
		if( !mia.is_market_issued() ) return false;

		const asset_bitasset_data_object& bitasset = ( bitasset_ptr ? *bitasset_ptr : mia.bitasset_data(*this) );
		if( bitasset.has_settlement() ) return true; // already force settled
		auto settle_price = bitasset.current_feed.settlement_price;
		if( settle_price.is_null() ) return false; // no feed

		const call_order_index& call_index = get_index_type<call_order_index>();
		const auto& call_price_index = call_index.indices().get<by_price>();

		auto call_min = price::min( bitasset.options.short_backing_asset, mia.id );
		auto call_max = price::max( bitasset.options.short_backing_asset, mia.id );
		auto call_itr = call_price_index.lower_bound( call_min );
		auto call_end = call_price_index.upper_bound( call_max );

		if( call_itr == call_end ) return false;  // no call orders

		price highest = settle_price;

		const auto& dyn_prop = get_dynamic_global_properties();
		auto maint_time = dyn_prop.next_maintenance_time;
		if( maint_time > HARDFORK_CORE_338_TIME )
		   // due to #338, we won't check for black swan on incoming limit order, so need to check with MSSP here
		   highest = bitasset.current_feed.max_short_squeeze_price();

		const limit_order_index& limit_index = get_index_type<limit_order_index>();
		const auto& limit_price_index = limit_index.indices().get<by_price>();

		// looking for limit orders selling the most USD for the least CORE
		auto highest_possible_bid = price::max( mia.id, bitasset.options.short_backing_asset );
		// stop when limit orders are selling too little USD for too much CORE
		auto lowest_possible_bid  = price::min( mia.id, bitasset.options.short_backing_asset );

		FC_ASSERT( highest_possible_bid.base.asset_id == lowest_possible_bid.base.asset_id );
		// NOTE limit_price_index is sorted from greatest to least
		auto limit_itr = limit_price_index.lower_bound( highest_possible_bid );
		auto limit_end = limit_price_index.upper_bound( lowest_possible_bid );

		if( limit_itr != limit_end ) {
		   FC_ASSERT( highest.base.asset_id == limit_itr->sell_price.base.asset_id );
		   highest = std::max( limit_itr->sell_price, highest );
		}

		auto least_collateral = call_itr->collateralization();
		if( ~least_collateral >= highest  ) 
		{
		   wdump( (*call_itr) );
		   elog( "Black Swan detected on asset ${symbol} (${id}) at block ${b}: \n"
				 "   Least collateralized call: ${lc}  ${~lc}\n"
			   //  "   Highest Bid:               ${hb}  ${~hb}\n"
				 "   Settle Price:              ${~sp}  ${sp}\n"
				 "   Max:                       ${~h}  ${h}\n",
				("id",mia.id)("symbol",mia.symbol)("b",head_block_num())
				("lc",least_collateral.to_real())("~lc",(~least_collateral).to_real())
			  //  ("hb",limit_itr->sell_price.to_real())("~hb",(~limit_itr->sell_price).to_real())
				("sp",settle_price.to_real())("~sp",(~settle_price).to_real())
				("h",highest.to_real())("~h",(~highest).to_real()) );
		   edump((enable_black_swan));
		   FC_ASSERT( enable_black_swan, "Black swan was detected during a margin update which is not allowed to trigger a blackswan" );
		   if( maint_time > HARDFORK_CORE_338_TIME && ~least_collateral <= settle_price )
			  // global settle at feed price if possible
			  globally_settle_asset(mia, settle_price );
		   else
			  globally_settle_asset(mia, ~least_collateral );
		   return true;
		} 
		return false;
	}


clear_expired_orders
---------------------------------------

.. code-block:: cpp 

	void database::clear_expired_orders()
	{ try {
			 //Cancel expired limit orders
			 auto head_time = head_block_time();
			 auto maint_time = get_dynamic_global_properties().next_maintenance_time;

			 bool before_core_hardfork_184 = ( maint_time <= HARDFORK_CORE_184_TIME ); // something-for-nothing
			 bool before_core_hardfork_342 = ( maint_time <= HARDFORK_CORE_342_TIME ); // better rounding
			 bool before_core_hardfork_606 = ( maint_time <= HARDFORK_CORE_606_TIME ); // feed always trigger call

			 auto& limit_index = get_index_type<limit_order_index>().indices().get<by_expiration>();
			 while( !limit_index.empty() && limit_index.begin()->expiration <= head_time )
			 {
				const limit_order_object& order = *limit_index.begin();
				auto base_asset = order.sell_price.base.asset_id;
				auto quote_asset = order.sell_price.quote.asset_id;
				cancel_limit_order( order );
				if( before_core_hardfork_606 )
				{
				   // check call orders
				   // Comments below are copied from limit_order_cancel_evaluator::do_apply(...)
				   // Possible optimization: order can be called by cancelling a limit order
				   //   if the canceled order was at the top of the book.
				   // Do I need to check calls in both assets?
				   check_call_orders( base_asset( *this ) );
				   check_call_orders( quote_asset( *this ) );
				}
			 }

	   //Process expired force settlement orders
	   auto& settlement_index = get_index_type<force_settlement_index>().indices().get<by_expiration>();
	   if( !settlement_index.empty() )
	   {
		  asset_id_type current_asset = settlement_index.begin()->settlement_asset_id();
		  asset max_settlement_volume;
		  price settlement_fill_price;
		  price settlement_price;
		  bool current_asset_finished = false;
		  bool extra_dump = false;

		  auto next_asset = [&current_asset, &current_asset_finished, &settlement_index, &extra_dump] {
			 auto bound = settlement_index.upper_bound(current_asset);
			 if( bound == settlement_index.end() )
			 {
				if( extra_dump )
				{
				   ilog( "next_asset() returning false" );
				}
				return false;
			 }
			 if( extra_dump )
			 {
				ilog( "next_asset returning true, bound is ${b}", ("b", *bound) );
			 }
			 current_asset = bound->settlement_asset_id();
			 current_asset_finished = false;
			 return true;
		  };

		  uint32_t count = 0;

		  // At each iteration, we either consume the current order and remove it, or we move to the next asset
		  for( auto itr = settlement_index.lower_bound(current_asset);
			   itr != settlement_index.end();
			   itr = settlement_index.lower_bound(current_asset) )
		  {
			 ++count;
			 const force_settlement_object& order = *itr;
			 auto order_id = order.id;
			 current_asset = order.settlement_asset_id();
			 const asset_object& mia_object = get(current_asset);
			 const asset_bitasset_data_object& mia = mia_object.bitasset_data(*this);

			 extra_dump = ((count >= 1000) && (count <= 1020));

			 if( extra_dump )
			 {
				wlog( "clear_expired_orders() dumping extra data for iteration ${c}", ("c", count) );
				ilog( "head_block_num is ${hb} current_asset is ${a}", ("hb", head_block_num())("a", current_asset) );
			 }

			 if( mia.has_settlement() )
			 {
				ilog( "Canceling a force settlement because of black swan" );
				cancel_settle_order( order );
				continue;
			 }

			 // Has this order not reached its settlement date?
			 if( order.settlement_date > head_time )
			 {
				if( next_asset() )
				{
				   if( extra_dump )
				   {
					  ilog( "next_asset() returned true when order.settlement_date > head_block_time()" );
				   }
				   continue;
				}
				break;
			 }
			 // Can we still settle in this asset?
			 if( mia.current_feed.settlement_price.is_null() )
			 {
				ilog("Canceling a force settlement in ${asset} because settlement price is null",
					 ("asset", mia_object.symbol));
				cancel_settle_order(order);
				continue;
			 }
			 if( max_settlement_volume.asset_id != current_asset )
				max_settlement_volume = mia_object.amount(mia.max_force_settlement_volume(mia_object.dynamic_data(*this).current_supply));
			 // When current_asset_finished is true, this would be the 2nd time processing the same order.
			 // In this case, we move to the next asset.
			 if( mia.force_settled_volume >= max_settlement_volume.amount || current_asset_finished )
			 {
				/*
				ilog("Skipping force settlement in ${asset}; settled ${settled_volume} / ${max_volume}",
					 ("asset", mia_object.symbol)("settlement_price_null",mia.current_feed.settlement_price.is_null())
					 ("settled_volume", mia.force_settled_volume)("max_volume", max_settlement_volume));
					 */
				if( next_asset() )
				{
				   if( extra_dump )
				   {
					  ilog( "next_asset() returned true when mia.force_settled_volume >= max_settlement_volume.amount" );
				   }
				   continue;
				}
				break;
			 }

			 if( settlement_fill_price.base.asset_id != current_asset ) // only calculate once per asset
				settlement_fill_price = mia.current_feed.settlement_price
										/ ratio_type( GRAPHENE_100_PERCENT - mia.options.force_settlement_offset_percent,
													  GRAPHENE_100_PERCENT );

			 if( before_core_hardfork_342 )
			 {
				auto& pays = order.balance;
				auto receives = (order.balance * mia.current_feed.settlement_price);
				receives.amount = ( fc::uint128_t(receives.amount.value) *
									(GRAPHENE_100_PERCENT - mia.options.force_settlement_offset_percent) /
									GRAPHENE_100_PERCENT ).to_uint64();
				assert(receives <= order.balance * mia.current_feed.settlement_price);
				settlement_price = pays / receives;
			 }
			 else if( settlement_price.base.asset_id != current_asset ) // only calculate once per asset
				settlement_price = settlement_fill_price;

			 auto& call_index = get_index_type<call_order_index>().indices().get<by_collateral>();
			 asset settled = mia_object.amount(mia.force_settled_volume);
			 // Match against the least collateralized short until the settlement is finished or we reach max settlements
			 while( settled < max_settlement_volume && find_object(order_id) )
			 {
				auto itr = call_index.lower_bound(boost::make_tuple(price::min(mia_object.bitasset_data(*this).options.short_backing_asset,
																			   mia_object.get_id())));
				// There should always be a call order, since asset exists!
				assert(itr != call_index.end() && itr->debt_type() == mia_object.get_id());
				asset max_settlement = max_settlement_volume - settled;

				if( order.balance.amount == 0 )
				{
				   wlog( "0 settlement detected" );
				   cancel_settle_order( order );
				   break;
				}
				try {
				   asset new_settled = match(*itr, order, settlement_price, max_settlement, settlement_fill_price);
				   if( !before_core_hardfork_184 && new_settled.amount == 0 ) // unable to fill this settle order
				   {
					  if( find_object( order_id ) ) // the settle order hasn't been cancelled
						 current_asset_finished = true;
					  break;
				   }
				   settled += new_settled;
				   // before hard fork core-342, `new_settled > 0` is always true, we'll have:
				   // * call order is completely filled (thus itr will change in next loop), or
				   // * settle order is completely filled (thus find_object(order_id) will be false so will break out), or
				   // * reached max_settlement_volume limit (thus new_settled == max_settlement so will break out).
				   //
				   // after hard fork core-342, if new_settled > 0, we'll have:
				   // * call order is completely filled (thus itr will change in next loop), or
				   // * settle order is completely filled (thus find_object(order_id) will be false so will break out), or
				   // * reached max_settlement_volume limit, but it's possible that new_settled < max_settlement,
				   //   in this case, new_settled will be zero in next iteration of the loop, so no need to check here.
				} 
				catch ( const black_swan_exception& e ) { 
				   wlog( "Cancelling a settle_order since it may trigger a black swan: ${o}, ${e}",
						 ("o", order)("e", e.to_detail_string()) );
				   cancel_settle_order( order );
				   break;
				}
			 }
			 if( mia.force_settled_volume != settled.amount )
			 {
				modify(mia, [settled](asset_bitasset_data_object& b) {
				   b.force_settled_volume = settled.amount;
				});
			 }
		  }
	   }
	} FC_CAPTURE_AND_RETHROW() }


update_expired_feeds
---------------------------------------

.. code-block:: cpp 

	void database::update_expired_feeds()
	{
	   const auto head_time = head_block_time();
	   bool after_hardfork_615 = ( head_time >= HARDFORK_615_TIME );

	   const auto& idx = get_index_type<asset_bitasset_data_index>().indices().get<by_feed_expiration>();
	   auto itr = idx.begin();
	   while( itr != idx.end() && itr->feed_is_expired( head_time ) )
	   {
		  const asset_bitasset_data_object& b = *itr;
		  ++itr; // not always process begin() because old code skipped updating some assets before hf 615
		  bool update_cer = false; // for better performance, to only update bitasset once, also check CER in this function
		  const asset_object* asset_ptr = nullptr;
		  // update feeds, check margin calls
		  if( after_hardfork_615 || b.feed_is_expired_before_hardfork_615( head_time ) )
		  {
			 auto old_median_feed = b.current_feed;
			 modify( b, [head_time,&update_cer]( asset_bitasset_data_object& abdo )
			 {
				abdo.update_median_feeds( head_time );
				if( abdo.need_to_update_cer() )
				{
				   update_cer = true;
				   abdo.asset_cer_updated = false;
				   abdo.feed_cer_updated = false;
				}
			 });
			 if( !b.current_feed.settlement_price.is_null() && !( b.current_feed == old_median_feed ) ) // `==` check is safe here
			 {
				asset_ptr = &b.asset_id( *this );
				check_call_orders( *asset_ptr, true, false, &b );
			 }
		  }
		  // update CER
		  if( update_cer )
		  {
			 if( !asset_ptr )
				asset_ptr = &b.asset_id( *this );
			 if( asset_ptr->options.core_exchange_rate != b.current_feed.core_exchange_rate )
			 {
				modify( *asset_ptr, [&b]( asset_object& ao )
				{
				   ao.options.core_exchange_rate = b.current_feed.core_exchange_rate;
				});
			 }
		  }
	   } // for each asset whose feed is expired

	   // process assets affected by bitshares-core issue 453 before hard fork 615
	   if( !after_hardfork_615 )
	   {
		  for( asset_id_type a : _issue_453_affected_assets )
		  {
			 check_call_orders( a(*this) );
		  }
	   }
	}


update_core_exchange_rates
---------------------------------------

.. code-block:: cpp 

	void database::update_core_exchange_rates()
	{
	   const auto& idx = get_index_type<asset_bitasset_data_index>().indices().get<by_cer_update>();
	   if( idx.begin() != idx.end() )
	   {
		  for( auto itr = idx.rbegin(); itr->need_to_update_cer(); itr = idx.rbegin() )
		  {
			 const asset_bitasset_data_object& b = *itr;
			 const asset_object& a = b.asset_id( *this );
			 if( a.options.core_exchange_rate != b.current_feed.core_exchange_rate )
			 {
				modify( a, [&b]( asset_object& ao )
				{
				   ao.options.core_exchange_rate = b.current_feed.core_exchange_rate;
				});
			 }
			 modify( b, []( asset_bitasset_data_object& abdo )
			 {
				abdo.asset_cer_updated = false;
				abdo.feed_cer_updated = false;
			 });
		  }
	   }
	}


update_maintenance_flag
---------------------------------------

.. code-block:: cpp 

	void database::update_maintenance_flag( bool new_maintenance_flag )
	{
	   modify( get_dynamic_global_properties(), [&]( dynamic_global_property_object& dpo )
	   {
		  auto maintenance_flag = dynamic_global_property_object::maintenance_flag;
		  dpo.dynamic_flags =
			   (dpo.dynamic_flags & ~maintenance_flag)
			 | (new_maintenance_flag ? maintenance_flag : 0);
	   } );
	   return;
	}


update_withdraw_permissions
---------------------------------------

.. code-block:: cpp 

	void database::update_withdraw_permissions()
	{
	   auto& permit_index = get_index_type<withdraw_permission_index>().indices().get<by_expiration>();
	   while( !permit_index.empty() && permit_index.begin()->expiration <= head_block_time() )
		  remove(*permit_index.begin());
	}

-------------------

|

db_witness_schedule.cpp
================================


.. code-block:: cpp 

	using boost::container::flat_set;

	
get_scheduled_witness	
---------------------------------------

.. code-block:: cpp 

	witness_id_type database::get_scheduled_witness( uint32_t slot_num )const
	{
	   const dynamic_global_property_object& dpo = get_dynamic_global_properties();
	   const witness_schedule_object& wso = get_witness_schedule_object();
	   uint64_t current_aslot = dpo.current_aslot + slot_num;
	   return wso.current_shuffled_witnesses[ current_aslot % wso.current_shuffled_witnesses.size() ];
	}

get_slot_time	
---------------------------------------

.. code-block:: cpp 

	fc::time_point_sec database::get_slot_time(uint32_t slot_num)const
	{
	   if( slot_num == 0 )
		  return fc::time_point_sec();

	   auto interval = block_interval();
	   const dynamic_global_property_object& dpo = get_dynamic_global_properties();

	   if( head_block_num() == 0 )
	   {
		  // n.b. first block is at genesis_time plus one block interval
		  fc::time_point_sec genesis_time = dpo.time;
		  return genesis_time + slot_num * interval;
	   }

	   int64_t head_block_abs_slot = head_block_time().sec_since_epoch() / interval;
	   fc::time_point_sec head_slot_time(head_block_abs_slot * interval);

	   const global_property_object& gpo = get_global_properties();

	   if( dpo.dynamic_flags & dynamic_global_property_object::maintenance_flag )
		  slot_num += gpo.parameters.maintenance_skip_slots;

	   // "slot 0" is head_slot_time
	   // "slot 1" is head_slot_time,
	   //   plus maint interval if head block is a maint block
	   //   plus block interval if head block is not a maint block
	   return head_slot_time + (slot_num * interval);
	}

	
get_slot_at_time
---------------------------------------

.. code-block:: cpp 

	uint32_t database::get_slot_at_time(fc::time_point_sec when)const
	{
	   fc::time_point_sec first_slot_time = get_slot_time( 1 );
	   if( when < first_slot_time )
		  return 0;
	   return (when - first_slot_time).to_seconds() / block_interval() + 1;
	}

	
update_witness_missed_blocks	
---------------------------------------

.. code-block:: cpp 

	uint32_t database::update_witness_missed_blocks( const signed_block& b )
	{
	   uint32_t missed_blocks = get_slot_at_time( b.timestamp );
	   FC_ASSERT( missed_blocks != 0, "Trying to push double-produced block onto current block?!" );
	   missed_blocks--;
	   const auto& witnesses = witness_schedule_id_type()(*this).current_shuffled_witnesses;
	   if( missed_blocks < witnesses.size() )
		  for( uint32_t i = 0; i < missed_blocks; ++i ) {
			 const auto& witness_missed = get_scheduled_witness( i+1 )(*this);
			 modify( witness_missed, []( witness_object& w ) {
				w.total_missed++;
			 });
		  }
	   return missed_blocks;
	}

witness_participation_rate	
---------------------------------------

.. code-block:: cpp 

	uint32_t database::witness_participation_rate()const
	{
	   const dynamic_global_property_object& dpo = get_dynamic_global_properties();
	   return uint64_t(GRAPHENE_100_PERCENT) * dpo.recent_slots_filled.popcount() / 128;
	}

update_witness_schedule
---------------------------------------

.. code-block:: cpp 
	
	void database::update_witness_schedule()
	{
	   const witness_schedule_object& wso = get_witness_schedule_object();
	   const global_property_object& gpo = get_global_properties();

	   if( head_block_num() % gpo.active_witnesses.size() == 0 )
	   {
		  modify( wso, [&]( witness_schedule_object& _wso )
		  {
			 _wso.current_shuffled_witnesses.clear();
			 _wso.current_shuffled_witnesses.reserve( gpo.active_witnesses.size() );

			 for( const witness_id_type& w : gpo.active_witnesses )
				_wso.current_shuffled_witnesses.push_back( w );

			 auto now_hi = uint64_t(head_block_time().sec_since_epoch()) << 32;
			 for( uint32_t i = 0; i < _wso.current_shuffled_witnesses.size(); ++i )
			 {
				/// High performance random generator
				/// http://xorshift.di.unimi.it/
				uint64_t k = now_hi + uint64_t(i)*2685821657736338717ULL;
				k ^= (k >> 12);
				k ^= (k << 25);
				k ^= (k >> 27);
				k *= 2685821657736338717ULL;

				uint32_t jmax = _wso.current_shuffled_witnesses.size() - i;
				uint32_t j = i + k%jmax;
				std::swap( _wso.current_shuffled_witnesses[i],
						   _wso.current_shuffled_witnesses[j] );
			 }
		  });
	   }
	}





|