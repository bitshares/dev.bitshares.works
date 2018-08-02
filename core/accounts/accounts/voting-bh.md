## Voting

#### Table of Contents:
- Vote for…
  - [Witnesses](../accounts/accounts/voting-bh.md#witnesses)
  - [Committee](../accounts/accounts/voting-bh.md#committee-members)
  - [Workers](../accounts/accounts/voting-bh.md#workers)
  - [Proxy](../accounts/accounts/voting-bh.md#proxy)
- Voting by using BitShares UI
  - [Setting a proxy](../accounts/accounts/voting-bh.md#setting-a-proxy)
  - [Voting for Witness, Committee Member or Worker](../accounts/accounts/voting-bh.md#voting-for-witness-committee-member-or-worker)

***

If you hold some BTS tokens, you are considered a BTS holder of the BitShares and thus have a say in where it should be heading in future. 

Voting is essential in Bitshares in the same way it is essential to the community in which you live. The weight of your vote is directly correlated to the number of BTS you own. If you aren't heavily involved in the community, you are encouraged to choose a proxy who represents your interests. As a BTS holder you can vote for three different entities within the network:


### Witnesses

In BitShares, witnesses (Block producers) are entities that work for the blockchain by constructing new blocks. The witnesses collect transactions, bundle them into a block, sign the block and broadcast it to the network.  Every transaction made in the network is required to be validated by all witnesses.

BTS holders elect any number of witnesses to generate blocks. A block is a group of transactions which update the state of the database. Each account is allowed one vote per share per witness, a process known as approval voting. The top witnesses by total approval are selected. 

For each successfully constructed block, a witness is paid for their services. Their pay rate is set by the BTS holders via the (unpaid) committee. If a witness fails to produce a block, then they are not paid, and may even be voted out.

They essentially are the block producers for the underlying consensus mechanism.  The slate of active witnesses is updated once every maintenance interval (1 day) when the votes are tallied. The witnesses are then shuffled, and each witness is given a turn to produce a block at a fixed schedule of one block every 3 seconds. After all witnesses have had a turn, they are shuffled again. If a witness does not produce a block in their time slot, then that time slot is skipped, and the next witness produces the next block. 

### Committee Members

Since Bitcoin struggled to reach a consensus about the size of their blocks, the people in the cryptocurrency space realized that the governance of a DAC should not be ignored. Hence, BitShares offers tools to reach on-chain consensus about business management decisions.

The BitShares blockchain has a set of parameters available that are subject to BTS holders approval. BTS holders can define their preferred set of parameters and thereby create a so-called committee member or alternatively vote for an existing committee member. The BitShares committee consists of several active committee members.

The BitShares ecosystem has a set of parameters available that are approved by the BTS holders and a set policy for the Bitshares blockchain including:

- Transaction and trading fees
- Blockchain parameters, such as block size, block interval
- Referral and vesting parameters such as cash back percentage and vesting periods

### Workers

Workers are proposals to perform a service in return for a salary from the blockchain. A worker proposal contains at a minimum the following information:

- A start and end date
- A daily pay
- A maximum total pay
- A link to a webpage where the worker proposal is explained

*Worker Lifecycle*

|           |                                                                                                 |
|----|----|
| Proposed  | These worker proposals have been submitted to the blockchain and are being actively voted on.  <br/>  In order to become active, they must exceed the refund400k worker in total votes.               |
| Active    | These worker proposals have exceeded the threshold and are being actively paid. Active workers   can be defunded if their vote threshold is reduced below the refund400k worker level.           |
| Expired   | These worker proposals are displayed for historical purposes. You will find proposals that  have ended based upon their end date.                                     |


### Proxy
	
You may choose to not be active in the governance of Bitshares. If this is the case, it's important that you choose someone in the Bitshares community who you identify with and set that entity as your **proxy**. This gives them to the power to vote on your behalf with your BTS shares backing their vote. This is similar to electing a representative.

***

### Voting by using BitShares UI

You can cast your vote or set your proxy by BitShares User interface.

Open the side dropdown menu and select **[Voting]**

![Voting](../../core/imgs/voting1.png)

#### Setting a proxy

The picture below shows how to set your trusted proxy:

1. Type a proxy name
2. Click [SAVE]
3. Login to a wallet if you have not logged in
4. Confirm the Transaction


![voting](https://cedar-book.github.io/core.dev/core/imgs/voting7.png) 
		
#### Voting for Witness, Committee Member or Worker

If you have not set a proxy, you can cast your own vote for witnesses, committee members or workers.

1. Select a Witnesses, Committee, or Workers tab
2. Click and check **TOGGLE VOTE**
3. Click [SAVE] 
4. Login to a wallet if you have not logged in
5. (vote for each entity)


![voting](https://cedar-book.github.io/core.dev/core/imgs/voting2b.png)

![voting](https://cedar-book.github.io/core.dev/core/imgs/voting3.png) 



