
.. _cli-faq:


CLI Wallet - FAQ 
=================
 
.. _cli-faq1:
 
How can I close the CLI client in a clean way?
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

In Windows closing the whole window produces a nasty exception. In
Windows you can try `ctrl-d` which stops the process but stil produces a
nasty exception.


.. _cli-faq5:  
  
Why does the CLI client crash immediately when I try to run it for the first time?
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

The CLI client is unable to run on its own, i.e. without being connected
to the witness node (via a web socket connection). So to successfully
run the CLI client you need to do this:

* make sure you have this entry uncommented in the
  `witness_node_data_dir/config.ini` file  
  `rpc-endpoint = 127.0.0.1:8090`
* before you start the CLI client, you need to start the witness node
  (and wait a while till it's up and running)
  
.. Note:: Find more "How to" guide in a Tutorials section.
  
	  
----------------------------	  


|