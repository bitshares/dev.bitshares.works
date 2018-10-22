

Crypto API
******************

The crypto API is available from the full node via websockets.

If you have not set up your websockets connection, please read :doc:`this
article <websocket>`.

.. contents:: Table of Contents
   :local:
   
----------------
   

Blinding and Un-Blinding
===========================

blind
-----------------------------------
.. doxygenfunction:: graphene::app::crypto_api::blind

blind_sum
------------------------------------
.. doxygenfunction:: graphene::app::crypto_api::blind_sum

|

**************

Rage Proofs
========================

range_get_info
------------------------------------
.. doxygenfunction:: graphene::app::crypto_api::range_get_info

range_proof_sign
------------------------------------
.. doxygenfunction:: graphene::app::crypto_api::range_proof_sign

|

**************

Verification
===========================

verify_sum
------------------------------------
.. doxygenfunction:: graphene::app::crypto_api::verify_sum

verify_range
------------------------------------
.. doxygenfunction:: graphene::app::crypto_api::verify_range

verify_range_proof_rewind
------------------------------------
.. doxygenfunction:: graphene::app::crypto_api::verify_range_proof_rewind

|
