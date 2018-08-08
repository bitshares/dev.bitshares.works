## Components Structures and Descriptions

### Block
- [A Block Elements and Structure(PDF)](../knowledge_base/shared_files/structures/BitShares-Block-Structurev1.pdf)
- [Block Header - inheritance](README.md#block-header)


### Evaluators
- graphene::chain::account_create_evaluator 


### Operations 
- [graphene::chain::base_operation](../components/operations.md#bitshares-core---graphenechain)

### Objects
- [graphene::chain Namespace: Class - Objects](../components/objects.md#bitshares-core---graphenechain)


***



### Block Header

#### block_header

    // *Block Header Inheritance* (i.e.) 1.0.0.0 
    // graphene::chain
    
    struct block_header
    {
        digest_type digest()const;
        block_id_type previous;
        uint32_t block_num()const { return num_from_id(previous) + 1; }
        fc::time_point_sec timestamp;
        witness_id_type witness;
        checksum_type transaction_merkle_root;
        extensions_type extensions;

        static uint32_t num_from_id(const block_id_type& id);
    };

 
#### signed_block_header
 
    // *Block Header Inheritance* (i.e.) 1.1.0.0
    // graphene::chain
        
    struct signed_block_header : public block_header
    {
        block_id_type id()const;
        fc::ecc::public_key signee()const;
        void sign( const fc::ecc::private_key& signer );
        bool validate_signee( const fc::ecc::public_key& expected_signee )const;

        signature_type witness_signature;
    };
 
#### signed_block
 
    // *Block Header Inheritance* (i.e.) 1.1.1.0
    // graphene::chain
        
    struct signed_block : public signed_block_header
    {
        checksum_type calculate_merkle_root()const;
        vector<processed_transaction> transactions;
    };

 
#### signed_block_with_info
  
    // *Block Header Inheritance* (i.e.) 1.1.1.1
    // graphene::wallet
    
    struct signed_block_with_info : public signed_block
    {
        signed_block_with_info( const signed_block& block );
        signed_block_with_info( const signed_block_with_info& block ) = default;

        block_id_type block_id;
        public_key_type signing_key;
        vector< transaction_id_type > transaction_ids;
    };
 
 
