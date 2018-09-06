
.. _witness-backup-server:


Witness - Backup Server
====================================

Backup Server
---------------

To stay a reliable block producer it is recommend you have a ‘hot swappable’ backup server with same specs as the live server running an instance of witness_node. 

.. Attention:: IT IS IMPORTANT THAT THIS BACKUP SHOULD _NOT_ HAVE THE SAME SIGNING KEY PAIR in the ``config.ini`` as your main node!



**How it works:**

1. Your ‘live’ witness node is signing blocks with the private key which is stated in the ``config.ini``.
2. Your ‘backup’ witness node is running a copy of the software with another private key in the `config.ini` (generate a new public/private keypair with cli_wallet command: suggest_brain_key).
3. On a third server you monitor your ‘live’ node on regular intervals with an automated script (e.g.: https://github.com/roelandp/Bitshares-Witness-Monitor).
4. As soon as your ‘live’ node is starting to fail producing blocks the ‘missing blocks’ parameter increases and you can issue a command to update_witness to your backup’s ‘Public Key’.
5. Investigate the issue with your ‘live’ node and stay happy.



|

|

