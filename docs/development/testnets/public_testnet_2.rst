
.. _public-testnet-guide-2:

***********************************
Public Testnet : witness
***********************************


.. contents:: Table of Contents
   :local:
   
-------


 
									  
1. Set up Block Production (Option)
------------------------------------------

If you want to produce blocks, you need to modify the data configulation file ``config.ini``. 

All we put into the configuration file is the ids and the keys for the witnesses so that we can start producing blocks ::

    witness-id = "1.6.1"
    witness-id = "1.6.2"
    witness-id = "1.6.3"
    witness-id = "1.6.4"
    witness-id = "1.6.5"
    witness-id = "1.6.6"
    witness-id = "1.6.7"
    witness-id = "1.6.8"
    witness-id = "1.6.9"
    witness-id = "1.6.10"
    # For each witness, add pubkey and private key:
    private-key = ["TEST6MRyAjQq8ud7hVNYcfnVPJqcVpscN5So8BhtHuGYqET5GDW5CV","5KQwrPbwdL6PhXujxW37FSSQZ1JiwsST4cqQzDeyXtP79zkvFD3"]
    private-key = [<pubkey>,<privkey>]
    private-key = [<pubkey>,<privkey>]
    private-key = [<pubkey>,<privkey>]
    private-key = [<pubkey>,<privkey>]
    private-key = [<pubkey>,<privkey>]
    private-key = [<pubkey>,<privkey>]
    private-key = [<pubkey>,<privkey>]
    private-key = [<pubkey>,<privkey>]
    private-key = [<pubkey>,<privkey>]
    private-key = [<pubkey>,<privkey>]

This authorizes the ``witness_node`` to produce blocks on behalf of the listed **witness-id's**, and specifies the private key needed to sign those blocks.  Normally each witness would be on a different node, but for the purposes of this testnet, we will start out with all witnesses signing blocks on a single node.

|
	  
----------

2. Establish a Committee (Option - Block Production)
----------------------------------------------------


Our network needs a committee. We need to initially create new accounts and let them apply as committee member.

2-1. Creating members
^^^^^^^^^^^^^^^^^^^^^^

* ``create_account_with_brain_key``

::

    create_account_with_brain_key com0 com0 faucet faucet true
    create_account_with_brain_key com1 com1 faucet faucet true
    create_account_with_brain_key com2 com2 faucet faucet true
    create_account_with_brain_key com3 com3 faucet faucet true
    create_account_with_brain_key com4 com4 faucet faucet true
    create_account_with_brain_key com5 com5 faucet faucet true
    create_account_with_brain_key com6 com6 faucet faucet true

2.2 Upgrading members
^^^^^^^^^^^^^^^^^^^^^^^^^

Since only lifetime members can be committee members, we need to fund these accounts ``transfer``  and upgrade ``upgrade_account`` them accordingly:

::

    transfer faucet com0 100000 TEST "some cash" true
    transfer faucet com1 100000 TEST "some cash" true
    transfer faucet com2 100000 TEST "some cash" true
    transfer faucet com3 100000 TEST "some cash" true
    transfer faucet com4 100000 TEST "some cash" true
    transfer faucet com5 100000 TEST "some cash" true
    transfer faucet com6 100000 TEST "some cash" true
    upgrade_account com0 true
    upgrade_account com1 true
    upgrade_account com2 true
    upgrade_account com3 true
    upgrade_account com4 true
    upgrade_account com5 true
    upgrade_account com6 true

	
2.3 Registering as committee member
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

We can apply for committee with create_committee_member:

* ``create_committee_member``

 ::

    create_committee_member com0 "" true
    create_committee_member com1 "" true
    create_committee_member com2 "" true
    create_committee_member com3 "" true
    create_committee_member com4 "" true
    create_committee_member com5 "" true
    create_committee_member com6 "" true


2.4 Voting with faucet account
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

All we need to do know is vote for our own committee members:

* ``vote_for_committee_member``

::

    vote_for_committee_member faucet com0 true true
    vote_for_committee_member faucet com1 true true
    vote_for_committee_member faucet com2 true true
    vote_for_committee_member faucet com3 true true
    vote_for_committee_member faucet com4 true true
    vote_for_committee_member faucet com5 true true
    vote_for_committee_member faucet com6 true true

-------------
	


|

|
