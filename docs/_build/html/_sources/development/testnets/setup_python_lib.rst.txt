
.. _how-to-setup-python-lib:

How to Set up Python Library
===================================


.. contents:: Table of Contents
   :local:
   
-------

1. Requirements
--------------------------------

Install pip3 which will deal with the packages dependencies::

    sudo apt-get install python3-pip

2. Installation
-------------------------------------

Afterwards, we can fetch the python libraries from github and install it: 

.. code-block:: sh

    git clone https://github.com/xeroc/python-graphenelib cd python-graphenelib/

    pip3 install –user -r requirements.txt python3 setup.py install –user

3. Usage
----------------------------

The usage of this library is well documented on

* https://readthedocs.org/projects/python-graphenelib/

----------------------


Create MPAs/UIAs
---------------------

We now create some Market Pegged assets and construct the price feed.

* Create Market Pegged Assets: 

  - https://github.com/BitSharesEurope/testnet-pythonscripts/blob/master/create_mpa.py
   
* Fund init* witnessses: 

  - https://github.com/BitSharesEurope/testnet-pythonscripts/blob/master/fund-inits.py
   
* Price feed scripts:

  - https://github.com/BitSharesEurope/testnet-pythonscripts/blob/master/feed.last.py
  - https://github.com/BitSharesEurope/testnet-pythonscripts/blob/master/feed.parity.py
  - https://github.com/BitSharesEurope/testnet-pythonscripts/blob/master/feed.random.py

We can use cron to runt he price feed script on a regular basis::

    */15 * * * * /usr/bin/python3 /home/gph/testnet-pythonscripts/feed.last.py
    */15 * * * * /usr/bin/python3 /home/gph/testnet-pythonscripts/feed.random.py
    0    * * * * /usr/bin/python3 /home/gph/testnet-pythonscripts/feed.parity.py

|

|