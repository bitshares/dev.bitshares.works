### Add CLI startup option to only generate keys #1011

***

#### Request

**User Story:** As a user I want to generate public/private key pairs without connecting to a node.

I often use the suggest_brain_key API of the cli_wallet to generate a new signing key pair. It would be helpful to call a command line app with this parameter to achieve the same result on an offline workstation.
 
***

*This document purpose: as a result, we made changes. The below shows those changes.*

***

#### Changes:

For code reuse, moved origin `wallet_api::suggest_brain_key`'s code into `graphene::wallet::utility` to make it static method.

(1)
libraries/wallet/include/graphene/wallet/wallet.hpp
- added

      class utility {
         public:
              ...

            /** Suggests a safe brain key to use for creating your account.
             * \c create_account_with_brain_key() requires you to specify a 'brain key',
             * a long passphrase that provides enough entropy to generate cyrptographic
             * keys.  This function will suggest a suitably random string that should
             * be easy to write down (and, with effort, memorize).
             * @returns a suggested brain_key
             */
            static brain_key_info suggest_brain_key();
      };


(2)
libraries/wallet/wallet.cpp
- inside body part was moved into (3) `brain_key_info utility::suggest_brain_key()` section. And in here, get `return graphene::wallet::utility::suggest_brain_key();`

      brain_key_info wallet_api::suggest_brain_key()const
       {
          return graphene::wallet::utility::suggest_brain_key();
       }
     
(3)
libraries/wallet/wallet.cpp
- `brain_key_info utility::suggest_brain_key()` was added


      namespace graphene { namespace wallet {
         vector<brain_key_info> utility::derive_owner_keys_from_brain_key(string brain_key, int number_of_desired_keys)
         {
            // Safety-check
            FC_ASSERT( number_of_desired_keys >= 1 );

            // Create as many derived owner keys as requested
            vector<brain_key_info> results;
            brain_key = graphene::wallet::detail::normalize_brain_key(brain_key);
            for (int i = 0; i < number_of_desired_keys; ++i) {
              fc::ecc::private_key priv_key = graphene::wallet::detail::derive_private_key( brain_key, i );

              brain_key_info result;
              result.brain_priv_key = brain_key;
              result.wif_priv_key = key_to_wif( priv_key );
              result.pub_key = priv_key.get_public_key();

              results.push_back(result);
            }

            return results;
         }

         brain_key_info utility::suggest_brain_key()
         {
            brain_key_info result;
            // create a private key for secure entropy
            fc::sha256 sha_entropy1 = fc::ecc::private_key::generate().get_secret();
            fc::sha256 sha_entropy2 = fc::ecc::private_key::generate().get_secret();
            fc::bigint entropy1(sha_entropy1.data(), sha_entropy1.data_size());
            fc::bigint entropy2(sha_entropy2.data(), sha_entropy2.data_size());
            fc::bigint entropy(entropy1);
            entropy <<= 8 * sha_entropy1.data_size();
            entropy += entropy2;
            string brain_key = "";

            for (int i = 0; i < BRAIN_KEY_WORD_COUNT; i++)
            {
               fc::bigint choice = entropy % graphene::words::word_list_size;
               entropy /= graphene::words::word_list_size;
               if (i > 0)
                  brain_key += " ";
               brain_key += graphene::words::word_list[choice.to_int64()];
            }

            brain_key = detail::normalize_brain_key(brain_key);
            fc::ecc::private_key priv_key = detail::derive_private_key(brain_key, 0);
            result.brain_priv_key = brain_key;
            result.wif_priv_key = key_to_wif(priv_key);
            result.pub_key = priv_key.get_public_key();
            return result;
         }
      }}

     
(4) programs/cli_wallet/main.cpp
- added the below
- `Tree:4a2f333444`
     
      ("suggest-brain-key", "Suggest a safe brain key to use for creaing your account")
 
    ...

      if( options.count("suggest-brain-key") )
      {
         auto keyinfo = graphene::wallet::utility::suggest_brain_key();
         string data = fc::json::to_pretty_string( keyinfo );
         std::cout << data.c_str() << std::endl;
         return 0;
      }

***

(*created:7/8/2018*)
