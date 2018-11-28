
.. _lib-block:

*******************************************
Block
*******************************************

This section shows how each block is constructed and what kinds of elements are creating the block.

.. contents:: Table of Contents
   :local:
   
-------

Block 
==========

The below is simple blockchain structure image. 

- Each block has a block_id and refers a previous block_id. And each block has more than one transaction(s). 
- Each transaction has more than one operation(s). 
- Each operation has "fee" and element(s). Each operation has different elements (processes). 


.. image:: ../../_static/structures/block_structure_si1.png
        :alt: BitShares Architecture
        :width: 700px
        :align: center


  
Block Header - inheritance 
================================


block_header
----------------------

.. code-block:: cpp 

	// *Block Header Inheritance* (i.e.) 1.0.0.0 
	// graphene::chain

	struct block_header
	{
	  digest_type                   digest()const;
	  block_id_type                 previous;
	  uint32_t                      block_num()const { return num_from_id(previous) + 1; }
	  fc::time_point_sec            timestamp;
	  witness_id_type               witness;
	  checksum_type                 transaction_merkle_root;
	  // Note: when we need to add data to `extensions`, remember to review `database::_generate_block()`.
	  //       More info in https://github.com/bitshares/bitshares-core/issues/1136
	  extensions_type               extensions;

	  static uint32_t num_from_id(const block_id_type& id);
	};
	// (11/27/2018)


.. Note:: when we need to add data to `extensions`, remember to review ``database::_generate_block()``.
  - More info in https://github.com/bitshares/bitshares-core/issues/1136
      
signed_block_header
---------------------

.. code-block:: cpp

	// *Block Header Inheritance* (i.e.) 1.1.0.0
	// graphene::chain

	struct signed_block_header : public block_header
	{
		block_id_type        id()const;
		fc::ecc::public_key  signee()const;
		void                 sign( const fc::ecc::private_key& signer );
		bool                 validate_signee( const fc::ecc::public_key& expected_signee )const;

		signature_type       witness_signature;
	};



signed_block
--------------

.. code-block:: cpp

	// *Block Header Inheritance* (i.e.) 1.1.1.0
	// graphene::chain

	struct signed_block : public signed_block_header
	{
		checksum_type                 calculate_merkle_root()const;
		vector<processed_transaction> transactions;
	};

 
signed_block_with_info
-----------------------

.. code-block:: cpp  

	// *Block Header Inheritance* (i.e.) 1.1.1.1
	// graphene::wallet

	struct signed_block_with_info : public signed_block
	{
		signed_block_with_info( const signed_block& block );
		signed_block_with_info( const signed_block_with_info& block ) = default;

		block_id_type                  block_id;
		public_key_type                signing_key;
		vector< transaction_id_type >  transaction_ids;
	};
	 
----------------------------


Block (detail image)
===========================
		
.. image:: ../../_static/structures/block_structure_detail1.png
        :alt: BitShares Architecture
        :width: 700px
        :align: center

|

----------------------

(**ibraries/chain/include/graphene/chain/block_database.hpp)

block_database
==========================
			
.. code-block:: cpp  

	namespace graphene { namespace chain {
	   struct index_entry;

	   class block_database 
	   {
		  public:
			 void open( const fc::path& dbdir );
			 bool is_open()const;
			 void flush();
			 void close();

			 void store( const block_id_type& id, const signed_block& b );
			 void remove( const block_id_type& id );

			 bool                   contains( const block_id_type& id )const;
			 block_id_type          fetch_block_id( uint32_t block_num )const;
			 optional<signed_block> fetch_optional( const block_id_type& id )const;
			 optional<signed_block> fetch_by_number( uint32_t block_num )const;
			 optional<signed_block> last()const;
			 optional<block_id_type> last_id()const;
			 size_t                 blocks_current_position()const;
			 size_t                 total_block_size()const;
		  private:
			 optional<index_entry> last_index_entry()const;
			 fc::path _index_filename;
			 mutable std::fstream _blocks;
			 mutable std::fstream _block_num_to_pos;
	   };
	} }
    //(11/27/2018)


	

|

