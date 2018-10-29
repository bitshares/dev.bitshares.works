
.. _ex-broadcase_trnx:

Broadcast transactions
**********************************************************


BitShares blockchain already handles many features (i.e., transactions, building block, validations, DOPS, etc). 


Broadcast transaction
========================



Return pattern (1)
-------------------

.. code-block:: cpp 

   .......
	signed_transaction trx;
	trx.operations = {op};
	set_operation_fees( trx, _remote_db->get_global_properties().parameters.current_fees);
	trx.validate();

	return sign_transaction(trx, broadcast);


Return pattern (2)
-------------------

.. code-block:: cpp 

      if( broadcast )
         _remote_net_broadcast->broadcast_transaction( tx );
		// do something... 
		 		 
      return tx;
	  


|

|

