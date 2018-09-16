
.. _lib-wallet:

********************************
libraries - wallet 
********************************


.. contents:: Table of Contents
   :local:
   
-------


Directories and files
===========================

For more information: `graphene::wallet Namespace Reference <https://bitshares.org/doxygen/namespacegraphene_1_1wallet.html>`_



wallet.hpp	
===========================

**namespace fc**

.. code-block:: cpp
   
	namespace fc
	{
	   void to_variant( const account_multi_index_type& accts, variant& vo, uint32_t max_depth );
	   void from_variant( const variant &var, account_multi_index_type &vo, uint32_t max_depth );
	}

-------------------

**namespace graphene - namespace wallet**

.. code-block:: cpp
   
	typedef uint16_t transaction_handle_type;

	/**
	 * This class takes a variant and turns it into an object
	 * of the given type, with the new operator.
	 */

	object* create_object( const variant& v );

	
struct	
---------------------

plain_keys
^^^^^^^^^^^^^^

.. code-block:: cpp
   	
	struct plain_keys
	{
	   map<public_key_type, string>  keys;
	   fc::sha512                    checksum;
	};


brain_key_info
^^^^^^^^^^^^^^

.. code-block:: cpp 
 
	struct brain_key_info
	{
	   string brain_priv_key;
	   string wif_priv_key;
	   public_key_type pub_key;
	};



blind_confirmation	
^^^^^^^^^^^^^^
* Contains the confirmation receipt the sender must give the receiver and the meta data about the receipt that helps the sender identify which receipt is for the receiver and which is for the change address.

.. code-block:: cpp
 
	struct blind_confirmation 
	{
	   struct output
	   {
		  string                          label;
		  public_key_type                 pub_key;
		  stealth_confirmation::memo_data decrypted_memo;
		  stealth_confirmation            confirmation;
		  authority                       auth;
		  string                          confirmation_receipt;
	   };

	   signed_transaction     trx;
	   vector<output>         outputs;
	};

	

blind_balance
^^^^^^^^^^^^^^

.. code-block:: cpp
 
	struct blind_balance
	{
	   asset                     amount;
	   public_key_type           from; ///< the account this balance came from
	   public_key_type           to; ///< the account this balance is logically associated with
	   public_key_type           one_time_key; ///< used to derive the authority key and blinding factor
	   fc::sha256                blinding_factor;
	   fc::ecc::commitment_type  commitment;
	   bool                      used = false;
	};
	

blind_receipt
^^^^^^^^^^^^^^

.. code-block:: cpp

	struct blind_receipt
	{
	   std::pair<public_key_type,fc::time_point>        from_date()const { return std::make_pair(from_key,date); }
	   std::pair<public_key_type,fc::time_point>        to_date()const   { return std::make_pair(to_key,date);   }
	   std::tuple<public_key_type,asset_id_type,bool>   to_asset_used()const   { return std::make_tuple(to_key,amount.asset_id,used);   }
	   const commitment_type& commitment()const        { return data.commitment; }

	   fc::time_point                  date;
	   public_key_type                 from_key;
	   string                          from_label;
	   public_key_type                 to_key;
	   string                          to_label;
	   asset                           amount;
	   string                          memo;
	   authority                       control_authority;
	   stealth_confirmation::memo_data data;
	   bool                            used = false;
	   stealth_confirmation            conf;
	};


struct (one line1)	
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

.. code-block:: cpp

	struct by_from;
	struct by_to;
	struct by_to_asset_used;
	struct by_commitment;
	

typedef: multi_index_container 
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

.. code-block:: cpp

	typedef multi_index_container< blind_receipt,
	   indexed_by<
		  ordered_unique< tag<by_commitment>, const_mem_fun< blind_receipt, const commitment_type&, &blind_receipt::commitment > >,
		  ordered_unique< tag<by_to>, const_mem_fun< blind_receipt, std::pair<public_key_type,fc::time_point>, &blind_receipt::to_date > >,
		  ordered_non_unique< tag<by_to_asset_used>, const_mem_fun< blind_receipt, std::tuple<public_key_type,asset_id_type,bool>, &blind_receipt::to_asset_used > >,
		  ordered_unique< tag<by_from>, const_mem_fun< blind_receipt, std::pair<public_key_type,fc::time_point>, &blind_receipt::from_date > >
	   >
	> blind_receipt_index_type;


key_label	
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

.. code-block:: cpp

	struct key_label
	{
	   string          label;
	   public_key_type key;
	};


struct (one line2)	
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

.. code-block:: cpp

	struct by_label;
	struct by_key;
	
	
typedef: multi_index_container
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

.. code-block:: cpp

	typedef multi_index_container<
	   key_label,
	   indexed_by<
		  ordered_unique< tag<by_label>, member< key_label, string, &key_label::label > >,
		  ordered_unique< tag<by_key>, member< key_label, public_key_type, &key_label::key > >
	   >
	> key_label_index_type;


	
wallet_data	
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

.. code-block:: cpp

	struct wallet_data
	{
	   /** Chain ID this wallet is used with */
	   chain_id_type chain_id;
	   account_multi_index_type my_accounts;
	   /// @return IDs of all accounts in @ref my_accounts
	   vector<object_id_type> my_account_ids()const
	   {
		  vector<object_id_type> ids;
		  ids.reserve(my_accounts.size());
		  std::transform(my_accounts.begin(), my_accounts.end(), std::back_inserter(ids),
						 [](const account_object& ao) { return ao.id; });
		  return ids;
	   }
	   /// Add acct to @ref my_accounts, or update it if it is already in @ref my_accounts
	   /// @return true if the account was newly inserted; false if it was only updated
	   bool update_account(const account_object& acct)
	   {
		  auto& idx = my_accounts.get<by_id>();
		  auto itr = idx.find(acct.get_id());
		  if( itr != idx.end() )
		  {
			 idx.replace(itr, acct);
			 return false;
		  } else {
			 idx.insert(acct);
			 return true;
		  }
	   }

	   /** encrypted keys */
	   vector<char>              cipher_keys;

	   /** map an account to a set of extra keys that have been imported for that account */
	   map<account_id_type, set<public_key_type> >  extra_keys;

	   // map of account_name -> base58_private_key for
	   //    incomplete account regs
	   map<string, vector<string> > pending_account_registrations;
	   map<string, string> pending_witness_registrations;

	   key_label_index_type                                              labeled_keys;
	   blind_receipt_index_type                                          blind_receipts;

	   string                    ws_server = "ws://localhost:8090";
	   string                    ws_user;
	   string                    ws_password;
	};

exported_account_keys
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

.. code-block:: cpp

	struct exported_account_keys
	{
		string account_name;
		vector<vector<char>> encrypted_private_keys;
		vector<public_key_type> public_keys;
	};

	
	
exported_keys	
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

.. code-block:: cpp

	struct exported_keys
	{
		fc::sha512 password_checksum;
		vector<exported_account_keys> account_keys;
	};


approval_delta
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

.. code-block:: cpp

	struct approval_delta
	{
	   vector<string> active_approvals_to_add;
	   vector<string> active_approvals_to_remove;
	   vector<string> owner_approvals_to_add;
	   vector<string> owner_approvals_to_remove;
	   vector<string> key_approvals_to_add;
	   vector<string> key_approvals_to_remove;
	};


worker_vote_delta
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

.. code-block:: cpp

	struct worker_vote_delta
	{
	   flat_set<worker_id_type> vote_for;
	   flat_set<worker_id_type> vote_against;
	   flat_set<worker_id_type> vote_abstain;
	};


signed_block_with_info
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

.. code-block:: cpp

	struct signed_block_with_info : public signed_block
	{
	   signed_block_with_info( const signed_block& block );
	   signed_block_with_info( const signed_block_with_info& block ) = default;

	   block_id_type block_id;
	   public_key_type signing_key;
	   vector< transaction_id_type > transaction_ids;
	};


vesting_balance_object_with_info
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

.. code-block:: cpp

	struct vesting_balance_object_with_info : public vesting_balance_object
	{
	   vesting_balance_object_with_info( const vesting_balance_object& vbo, fc::time_point_sec now );
	   vesting_balance_object_with_info( const vesting_balance_object_with_info& vbo ) = default;

	   /**
		* How much is allowed to be withdrawn.
		*/
	   asset allowed_withdraw;

	   /**
		* The time at which allowed_withdrawal was calculated.
		*/
	   fc::time_point_sec allowed_withdraw_time;
	};


operation_detail
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

.. code-block:: cpp

	struct operation_detail {
	   string                   memo;
	   string                   description;
	   operation_history_object op;
	};


operation_detail_ex
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

.. code-block:: cpp

	struct operation_detail_ex {
		string                   memo;
		string                   description;
		operation_history_object op;
		transaction_id_type      transaction_id;
	};


account_history_operation_detail
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

.. code-block:: cpp

	struct account_history_operation_detail {
	   uint32_t                     total_count = 0;
	   uint32_t                     result_count = 0;
	   vector<operation_detail_ex>  details;
	};

-----------------------

|
	
class
------------------------------

utility
^^^^^^^^^^^^^^^^^^
* A utility class for performing various state-less actions that are related to wallets

.. code-block:: cpp

	class utility {
	   public:
		  /**
		   * Derive any number of *possible* owner keys from a given brain key.
		   *
		   * NOTE: These keys may or may not match with the owner keys of any account.
		   * This function is merely intended to assist with account or key recovery.
		   *
		   * @see suggest_brain_key()
		   *
		   * @param brain_key    Brain key
		   * @param number_of_desired_keys  Number of desired keys
		   * @return A list of keys that are deterministically derived from the brainkey
		   */
		  static vector<brain_key_info> derive_owner_keys_from_brain_key(string brain_key, int number_of_desired_keys = 1);

		  /** Suggests a safe brain key to use for creating your account.
		   * \c create_account_with_brain_key() requires you to specify a 'brain key',
		   * a long passphrase that provides enough entropy to generate cyrptographic
		   * keys.  This function will suggest a suitably random string that should
		   * be easy to write down (and, with effort, memorize).
		   * @returns a suggested brain_key
		   */
		  static brain_key_info suggest_brain_key();
	};

-----------------------

|

wallet_api
^^^^^^^^^^^^^^^^^^^^
* This wallet assumes it is connected to the database server with a high-bandwidth, low-latency connection and performs minimal caching. This API could be provided locally to be used by a web interface.


For detail information, please check: `graphene::wallet::wallet_api Class Reference <https://bitshares.org/doxygen/classgraphene_1_1wallet_1_1wallet__api.html>`_




|

