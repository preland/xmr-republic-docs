# Monero Republic vs Qubic

The design of this upgrade borrows heavily from the ideas behind the cryptocurrency Qubic, with many aspects of it directly inspired from their implementation and whitepaper. The following is a list of notable alterations to their design, as well as explanations as to why an alteration was made:

## Quorum Size

The quorum size chosen by Qubic is 676, which was chosen because it was considered a fair balance between decentralization and compute efficiency. We have chosen a higher number for reference (1000), to signify that the quorum size should be larger than Qubic's size in order to improve decentralization and to account for the currently very high number of Monero nodes.

## Quorum Selection

Qubic's quorum selection uses the raw PoW performance of each node as the sole determinant of node ranking. This selection mechanism is passable, but taking into account the "seniority" of node participants is important to mitigate the risk of attacks by unknown parties. While seniority should not be an insurmountable hurdle (which could allow for corrupt "elitist" behavior from older nodes, and would create a malincentive for attackers to steal the private keys of older nodes), it should be a barrier to change.

## Tick Leader/Block Creation

Qubic's block creation system (a block in Qubic are referred to as a "tick"), utilizes a pseudorandomly assigned "tick leader" to construct a given tick, which is then proposed to the rest of the nodes for consensus approval. While this system has merits (the main one being a trivial solution to transaction propagation issues), it creates issues relating to corruption in a tick leader. A tick leader could, for example, intentionally delay or censor certain transactions, or could intentionally submit invalid or multiple versions of their tick data. Qubic mitigates these issues by checking if more than a third of nodes have voted against the proposed tick. If this occurs, then the tick is automatically "skipped", and the next tick leader is used. We have removed the tick leader from the consensus entirely, and have instead implemented a dynamic blocksize/timeout system, where if a node can construct a block from their received transactions that meets the current set blocksize or if there are some transactions and the maximum block delay has been reached, they will submit the block for consensus.

It can be argued that the "tick leader" approach still has some benefits over this proposed change. In the case that a "tick leader" design is implemented, it is strongly suggested that the selection process for the tick leader be modified, as Qubic's design allows for a bad node to know when their assigned ticks are, which could allow them to coordinate with other attackers to create "blackout" periods.

## PoW

Qubic uses a custom PoW algorithm designed to produce "useful" outputs, referred to as "UPoW". While this is an intriguing concept, it is one that is directly tied to their own goals, and only serves to add complexity and uncertainty to Monero's mining if implemented. The PoW in our upgrade used by miners will remain as RandomX. This paper should note that RandomX itself is not required to be the PoW method. Any PoW algorithm used in Monero should be as close of an analog to individuals in a collective voting. This includes mitigations against Sybil attacks, as well as lowering the barrier to entry and reducing the effectiveness of efficiency techniques (such as ASICs or other systems that reduce the capability for normal users to compete).

## Arbitrator

Qubic's design utilizes an "Arbitrator", which is a special network participant which can remove and replace nodes that are deemed problematic or subpar. While their implementation includes a method to replace a rogue arbitrator with a 2/3rds supermajority, the power of the arbitrator is still too powerful, with a myriad of different scenarios and situations where its presence poses a threat to the entire governance model. In our upgrade, the task of removing and replacing faulty nodes is ran automatically via "node gossip", with nodes being replaced by the "alternate" nodes. This ensures a per-epoch fault tolerance of 1/6 at the smallest (the percentage amount of alternate nodes), and a tolerance of ~½ at the largest (the percentage amount of alternative nodes, plus the smallest number of node failures required to prevent consensus).

It may be possible to address and mitigate the issues with Qubic's arbitrator design. If this is proven feasible, implementing an arbitrator-like participant may be desirable, as such a participant, if proven to be acting in the interests of the network, would be a significant safeguard against latent threats against the network.

## Fee-less Transactions

Transactions in Qubic do not have any associated fees. While Monero Republic could implement this, it would be a significant change to the "tokenomics" of the network. Additional details about this change can be found in the relevant section in Additional Add-Ons.