

.. _cli-wallet-setpwd-unlock:


Setting a password and Unlock a Cli Wallet 
*************************************************

This section provides instructions on how to set up a password and unlock a Cli Wallet. 
If the wallet file does not exist, it will prompt ``new >>>`` to setup a password. Otherwise, it will prompt ``locked >>>``. 


.. contents:: Table of Contents
   :local:
  

 
.. Note:: New features have been added to the commands ``set_password`` and ``unlock``.

          - These new features will prompt ``Enter password:`` and will **not** show the input. 
          - These features work **on Linux and MacOS**.
          - These features also work on Windows, however not MSVC build.


  
-------


Set a Password
==================================

Originally, ``set_password`` command worked only with a parameter (i.e., password). New feature, this command works without a parameter.  This section provides the differences and the steps.


Use ``set_password`` with a parameter (password)
--------------------------------------------------

If cli_wallet starts successfully without a wallet file, it will prompt ``new >>>`` to set up a password

::

	Please use the set_password method to initialize a new wallet before continuing
	new >>> 

Type ``set_password`` and followed by a password and hit [enter] 

::

    new >>> set_password supersecretpassphrase
	
	
If the password  was saved successfully, it will show ``locked``. The Cli Wallet has been locked, no one can access to the cli wallet without the password.

::
	
    null
    locked >>>

|
	
Use ``set_password`` without a parameter (**Linux and MacOS**)
-----------------------------------------------------------------------

If cli_wallet starts without a wallet file, it will prompt ``new >>>`` to set up a password.
	
::

	Please use the set_password method to initialize a new wallet before continuing
	new >>> 

	
Type ``set_password`` and [enter]
  
::

    new >>> set_password 
	
If successful, it will prompt ``Enter password:``.  Type a password and hit [enter]. While typing the password, it should be invisible.   
	
::
	
    Enter password:
    
	
If the password has been saved successfully, it will prompt ``unlock``. The Cli Wallet has been locked, no one can access to the cli wallet without the password.

::
	
    locked >>>
	
|

--------------------


Unlock a Cli Wallet 
==================================

Originally, ``unlock`` command worked only with a parameter (i.e., password). New feature the command works without a parameter. This section provides the differences and the steps.

Use ``unlock`` with a parameter (password)
-----------------------------------------------------

Type ``unlock`` and followed by a password and hit [enter] 

::

    locked >>> unlock supersecretpassphrase
	
	
If successful, it will prompt ``unlocked``.  The Cli Wallet has been unlocked, if there are any funds, they are accessible. Be cautious. 

::
	
    null
    unlocked >>>
	
	
|

Use ``unlock`` without a parameter (**Linux and MacOS**)
---------------------------------------------------------------

Type ``unlock`` and hit [enter] 
  
::	

    locked >>> unlock

	
If successful, it will prompt ``Enter password:``. Type a password and hit [enter]. While typing the password, it should be invisible.   

   
::
  
    Enter password:
    
	
If the password was correct, it will prompt ``unlocked``.  The Cli Wallet has been unlocked, if there are any funds in the wallet, they are accessible. Be cautious. 

::
	
    unlocked >>>

|

--------------------

Lock a Cli Wallet
==============================================

After Cli Wallet has been unlocked, if any funds in the Cli Wallet, they are accessible. In genera, ``lock`` the cli wallet and only ``unlock`` when it's needed.

Type ``lock`` and hit [enter]


::

    unlocked >>> lock
	
	
If it's locked successfully, it will prompt ``locked`` 	
	
::
	
    null 
    locked >>> 
	

|

--------------------


Re-Set a password
==============================================

If the current password needs to be changed, unlocked the Cli Wallet and use ``set_password`` to do so. 

	
Type ``set_password`` and [enter]
  
::

    unlocked >>> set_password 		

	
|

		
.. Note:: After this point, you can issue any command available to the cli-wallet (Wallet APIs) or construct your own transaction manually.

|

--------------------

Get Help
==============================================


- ``gethelp``

You can get more detailed information by issuing ``gethelp``. Detailed explanations for most calls are available. For example, 

::

      unlocked >> gethelp "list_account_balances"

	  

	  
|