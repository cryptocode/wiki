RaiBlocks has a number of mechanisms built in to protect from a range of possible attacks on the system.  Here we go over all attacks there could be on the system and what safeguards are in place.  

### Block gap synchronization - Low risk, network amplify, denial of service
Description: Each block has a link to its previous block.  If a new block arrives where we can't find the previous block, this leaves the node deciding whether it's out of sync or if someone is sending junk data.  If a node is out of sync, synchronizing involves a TCP connection to a node that offers bootstrapping which is much more traffic than sending a single UDP packet containing a block.  
Defense: For blocks with no previous link, nodes will wait until a certain threshold of votes have been observed before initiating a connection to a bootstrap node to synchronize.  If a block doesn't receive enough votes it can be assumed to be junk data.  

### Transaction flooding - Moderate risk, high io
Description: Transaction flooding is simply sending as many valid transactions as possible in order to saturate the network.  Usually transactions will be sent to other accounts the attacker owns so it can be continued indefinitely.  
Defense: Each block has a small amount of work associated with it, around 5 seconds to generate and 1 microsecond to validate.  This work difference causes an attacker to dedicate a large amount of resources to wastes a small amount of resources by everyone else.  Nodes that are not full historical nodes are able to prune old transactions from their chain, this clamps the storage usage from this type of attack for almost all users.  

### Sybil attack to change ledger entries - No risk, completely destructive
Description: A Sybil attack is a person creating a lot of nodes on the network, possibly thousands on a single machine, in order to get a disproportionate vote on networks where each node gets an equal vote.  
Defense: The RaiBlocks voting system is weighted based on account balance.  Adding extra nodes in to the network will not gain an attacker extra votes.  

### Penny-spend attack - Moderate risk, large ledger
Description: A penny-spend attack is where an attacker spends infinitesimal quantities to a large number of accounts.  
Defense: Blocks publishing is rate-limited by work so this limits accounts to a certain extent.  

### >50% attack - Low risk, completely destructive
Description: The metric of consensus for RaiBlocks is a balance weighted voting system.  If an attacker is able to gain over 50% of the voting strength, they can cause the network to flip their decision making.  An attacker must have at least some value tied up in the network as a balance which they're willing to forfeit as an expense to performing this type of attack.  An attacker is able to lower the amount of balance they must forfeit by preventing good nodes from voting through a network DDOS and switching who they're attacking leaving some nodes unsynchronized.
Defense:
* The primary defense against this type of attack is voting weight being tied to investment in the system; attempting to flip the ledger would be destructive to the system as a whole which would destroy their investment.  
* The second defense against this attack is the cost of this attack is proportional to the market cap of all of RaiBlocks.  In proof of work systems, technology can be invented that gives control disproportionate to monetary investment and if the attack is successful, this technology could be repurposed after the attack is complete.  With RaiBlocks the cost of attacking the system by voting scales with the system and if an attack were to be successful the cost of the attack can't be recovered.
* In order to maintain the maximum quorum of voters, the next line of defense is representative voting.  Account holders who are unable to reliably participate in voting can name a representative who can vote with the weight of their balance.
* Forks in RaiBlocks are never accidental so nodes can make policy decisions on how to interact with forked blocks.  The only time non-attacker accounts are vulnerable to block forks is if they receive a balance from an attacking account.  Accounts wanting to be secure from block forks can wait a little or a lot longer before receiving from an account who generated forks or opt to never receive at all.  Receivers could also generate separate accounts for receiving from dubious accounts in order to protect the rest of their balance.
* A final line of defense that has not yet been implemented is block cementing.  RaiBlocks goes to great efforts to get block forks to settle quickly via voting.  Nodes could be configured to cement blocks after a certain period of time which would prevent them from being rolled back.  More research has to be done to figure out of this policy would be beneficial and what type of parameters would be the best.

The most sophisticated version of a >50% attack is detailed in the diagram below.  "Offline" is the percentage of representatives who have been named but are not online to vote.  "Stake" is the amount of investment the attacker is voting with and will be lost if they successfully attack the system.  "Active" are representatives that are online and voting according to the protocol.  An attacker can offset the amount of stake they must forfeit by knocking other voters offline via a network denial of service attack.  If this attack can be sustained, the representatives being attacked will become unsynchronized and this is demonstrated by "Unsynced".  Finally, an attacker can gain a short burst in relative voting strength by switching their denial of service attack to a new set of representatives while the old set is resynchronizing their ledger, this is demonstrated by "Attacked".

![Voting attack](https://raw.githubusercontent.com/clemahieu/raiblocks/master/images/attack.png)

If an attacker is able to cause Stake > Active by a combination of these circumstances, they would be able to successfully flip votes on the ledger at the expense of their stake.  We can estimate how much this type of attack could cost by examining the market cap of other systems.  If we estimate 33% of representatives are offline or attacked via denial of service, an attacker would need to purchase 33% of the market cap in order to attack the system via voting.

Voting attack cost:
* Euro - M1 in 2014 ~6 trillion, attack amount 2 trillion
* USD - M0 in 2014 ~4 trillion, attack amount 1.2 trillion
* UK pound sterling - M0 in 2007 ~1.5 trillion, attack amount 500 billion
* BitCoin - Market cap 2014 ~3 billion, attack amount 1 billion