**Abstract**

In the interest of time, and in line with the variability of this paper
in its current state, an abstract is not feasible.

A quick summation: This proposes an upgrade to Monero that would reduce
the effectiveness of 51% hashrate attacks, while also realigning the
incentive structure to better motivate network growth.

**Intro**

At the time of writing, Monero is the target of a 51% attack by a rival
chain (src). This attack, while yet to be successful, has shown that
Nakamura consensus is not enough to prevent an attack. It also shows
that so-called "economic incentives" are insufficient against attackers
with non-economic goals; these are not considered within the context of
Nakamura consensus. Simply put, if an attacker has the capability to
perform an attack against Monero, and they are not profit-driven, there
is little that the current consensus model can do to prevent it.

Outside of this current attack, there are other issues with Nakamura
consensus. While the longest chain generally works as an analog for the
collective will of users, it is utterly blind to their real-world
intentions and usage. There are many participant behaviors that are
clearly incompatible with the goals of the collective (ie "selfish"
mining attacks, needlessly empty or censored blocks) that are entirely
"kosher" within the bounds of Nakamura consensus. A sensible consensus
model needs to have the goals of the network embedded within itself,
rather than projected upon it in such a way that they could be removed
at a moment's notice.

Additionally, the emission model of traditional Nakamura consensus
creates "malincentives", or incentives for bad behavior. A number of
common Nakamura malincentives are either eliminated, as in the case of
ASIC mining, or significantly mitigated, as in the case of emission
decay, in contemporary Monero. However, Monero is not entirely immune of
Nakamura-derived malincentive.

For example, a node of some kind is required to mine, but running a node
is slow, cumbersome, and in some cases an unexpected expense for miners,
which creates a malincentive for miners to join pools and use their
nodes, which inherently centralize the network. As of block 3475761, 638
of the previous 1000 blocks were confirmed to be mined by 3 node
operators, with a significant portion of 198 "unknown" blocks likely
mined by Qubic. A sensible consensus model needs to proactively remove
all potential areas for centralization.

The node malincentives derived from Nakamura consensus run deeper than
mere miner inconvenience and centralization risks. Running a node yields
no rewards from emissions nor transaction fees, despite nodes being a
required part of the Monero network. The blatant discrepancy between the
real value of nodes and the assigned value of nodes has resulted in
severe malincentives, as the ones willing to run nodes are either doing
so out of charity, out of malice, or, in unfortunate cases, out of
coercion or theft of resource. It also inherently puts a damper on the
scalability of the network, as it removes any incentive for a node
runner to upgrade their system to improve the speed of the network. A
sensible consensus model should not only incentivize participants to
"secure" the network, but should also encourage scalability, and should
be proactive in providing fair value to the participants of the network.

This proposed upgrade to Monero, nicknamed "Republic", would transition
the network from the current consensus model of miners validating blocks
directly and sending them to unpaid nodes, to a consensus where miners
instead validate nodes in a quorum, who then, upon reaching a consensus,
will validate transactions. Miners will be able to choose which node to
mine for, with payment method being determined entirely by their node.
Miners can influence the decisions of the nodes through "agenda mining",
where the mining software will automatically adjust its mining target to
whatever node best fits the miner's "agenda", or goals. Nodes in the
quorum reach finality upon 2/3rds consensus. Nodes are routinely, but
semi-randomly, "reselected". The blockchain is split into two parts, a
"temporal" chain that exists entirely within the memory of a node, and a
"permanent" chain proof, which exists on disk. When a "reselection"
occurs, the temporal chain is "rolled up" into the "permanent" chain
proof, and the temporal chain is reset. This allows for an incredibly
small permanent blockchain size

blockchain size and efficient verification of transactions, while also
limiting the non-trivial work of generating a proof to only limited
times, with said work being distributed across all network nodes. Once
implemented, the Monero network will be more scalable, with an incentive
structure more aligned with that of improving infrastructure and network
performance, while minimizing all effects on decentralization and
privacy.

Consensus

The Monero Republic upgrade uses a "quorum" style consensus mechanism,
where a "quorum" of participant nodes are selected to make decisions.
The size of the quorum in this paper is preliminarily chosen to be 1000,
but this number is little more than an educated guess as to the best
number. Whichever number is chosen must not be too low so as to
centralize the network, but must also not be too high so as to cause
network strain or to increase the risk of consensus failure. In addition
to the quorum size, a further 1/6th of nodes are selected as "backups",
that can take the place of the selected nodes in the case of an
emergency. Quorum members are selected at the end of an epoch via a
provable "suitability" score. This score is determined by the length of
the node's private temporary chain multiplied by the "seniority
coefficient". The "seniority coefficient" is a float c\>=1 that is
increased by long-term participation in the network. It grows slowly
with time, and decreases slightly faster if participation halts.
Participants in the "backup" section gain seniority faster than those
who are in the quorum, which is meant to provide a small incentive for
nodes in this section to remain active in case they are needed.

Emissions are split evenly among all valid quorum participants at the
end of the epoch. This is only paid out after the chain proof has been
completed, to ensure that nodes do not attempt to avoid working on this
calculation. As emissions are paid directly to nodes, it is the
responsibility of nodes to pay their miners fairly. In the standard node
implementation, there will be both a setting for sending all mining
rewards to the node (should only be used for nodes that do all of their
own mining), and for one that takes a set percentage from the reward and
gives the rest to the miners responsible for mined blocks.

The network reaches a "consensus" whenever 2/3rds of the quorum agrees
on the state of the temporal blockchain. Once this agreement is reached,
the transaction is final. This means that there is instant finality on
the chain. As emissions are no longer tied to blocks, the block creation
rate is rapid. In order to prevent issues with timing, empty blocks are
not allowed. Transactions are propagated throughout the network, checked
for validity, and if they are valid, are immediately added to the newest
block. Once the block reaches the limit of the block size (which is
still determined dynamically in the same manner as before, though with
alterations to match the change in timing) or a given "timeout"
condition is reached, the block is submitted for consensus, and once
consensus is reached, it is finalized. If a node has recently received a
transaction but it is not added into the consensus in time, it will be
postponed to the next block.

Mining/Node Selection

At the beginning of each epoch, the hash of the tallest block from each
quorum member is XOR'd against each other in order to generate a
universal random "genesis" code. This code, along with the public key
for the respective node's identifier, is hashed in the "genesis block"
of the respective node's blockchain. This ensures both that the "work"
on the blockchain is not done in advance of the epoch, and that the work
belongs to the correct node (which also allows for the "seniority
coefficient" to be properly calculated).

The individual blockchains mine on a static-difficulty setting. This
setting should be small enough that small participants on low-end
consumer hardware can still mine a block at a minimum of once per 3
hours, but not small enough that re-orgs are common or that the size of
individual blockchains become cumbersome. The exact difficulty setting
should be dynamically updated at the beginning of each epoch.

Miners can choose which nodes they want to mine to, depending on a
number of factors that the miner cares about. While the primary factor
will likely be earnings and how the earnings are paid out (as nodes are
free to determine the payout method, this allows for both normal payout
systems like PPLNS, as well as for abnormal payout systems, such as a
"lottery" style payout like that implemented by XMRvsBEAST), other
factors such as security, node privacy, and governance beliefs can
influence their choice of node. Nodes can also run "initiatives" on
their chains, where nodes propose a question or questions to miners, and
mined blocks can contain a "ballot" object that shows the voting desires
of the block's miner.

The role of miners in this system changes from that of directly choosing
blocks and making consensus decisions to that of directly choosing
representatives that will then choose blocks and make consensus
decisions. This is analogous to the transition of a traditional
government from a direct democracy to a democratic republic. Hence why
this upgrade is referred to as "Republic".

ZK Rollups

NOTE: This section, more than any other section, needs work done, due to
the complexity involved.

Monero Republic incentivizes nodes to be productive and useful, which
will allow for strong and reliable network infrastructure to be
profitable and supported directly by miners. An additional benefit is
that computationally heavy tasks that do not generate emission directly,
despite being useful, can be undertaken. A good example of this in
action is ZK rollups, where a large challenge of implemention comes from
the generation of proofs, which is computationally difficult.

In Monero Republic, ZK proofs (likely using STARKS as opposed to SNARKS)
will be generated and "rolled up" into an overall "chain proof" at the
end of every epoch. The work for these proofs will be split evenly
across all nodes. Any node that fails to complete their share in a
reasonable amount of time will have their payment withheld, and the
payment will be given to an alternate node which completes the share. As
payment is not given to nodes until after the proof is created and
verified, nodes have an exceedingly strong incentive to not attempt to
skip proof generation. Furthermore, as miners are paid by nodes, miners
have a strong incentive to only mine for nodes that will complete the
proof generation.

The resulting proof should be incredibly small (\<10 MB at the absolute
largest), which should improve syncing time for wallets dramatically.

It should be noted that the exact details of the proof system are not
known, and requires further research and discussion from those with more
knowledge about ZK-related technologies.

Expected Results

Monero, once upgraded to this system, should immediately have a
significantly higher transaction throughput than before. In addition,
the hashrate will become much more decentralized, reducing the concern
for 51% hashrate attacks. Miners will experience the most disruption,
but even this large of a change should only have minimal impacts on
miner behavior. Thanks to ZK Rollups, wallet syncing for new users
becomes trivially fast and efficient, especially for users syncing at or
near the beginning of an epoch. Transactions are processed and finalized
quickly (less than 15 seconds), enabling the currency to become viable
for everyday usage. All of this happens, without compromise to privacy
and security. In short, Monero becomes more than a mere "privacy tool"
or crypto curiousity---it becomes a genuinely competitive alternative to
modern payment processors.

Monero Republic vs Qubic

The design of this upgrade borrows heavily from the ideas behind the
cryptocurrency Qubic, with many aspects of it directly inspired from
their implementation and whitepaper. The following is a list of notable
alterations to their design, as well as explanations as to why an
alteration was made:

Quorum Size

The quorum size chosen by Qubic is 676, which was chosen because it was
considered a fair balance between decentralization and compute
efficiency. We have chosen a higher number for reference (1000), to
signify that the quorum size should be larger than Qubic's size in order
to improve decentralization and to account for the currently very high
number of Monero nodes.

Quorum Selection

Qubic's quorum selection uses the raw PoW performance of each node as
the sole determinant of node ranking. This selection mechanism is
passable, but taking into account the "seniority" of node participants
is important to mitigate the risk of attacks by unknown parties. While
seniority should not be an insurmountable hurdle (which could allow for
corrupt "elitist" behavior from older nodes, and would create a
malincentive for attackers to steal the private keys of older nodes), it
should be a barrier to change.

Tick Leader/Block Creation

Qubic's block creation system (a block in Qubic are referred to as a
"tick"), utilizes a pseudorandomly assigned "tick leader" to construct a
given tick, which is then proposed to the rest of the nodes for
consensus approval. While this system has merits (the main one being a
trivial solution to transaction propagation issues), it creates issues
relating to corruption in a tick leader. A tick leader could, for
example, intentionally delay or censor certain transactions, or could
intentionally submit invalid or multiple versions of their tick data.
Qubic mitigates these issues by checking if more than a third of nodes
have voted against the proposed tick. If this occurs, then the tick is
automatically "skipped", and the next tick leader is used. We have
removed the tick leader from the consensus entirely, and have instead
implemented a dynamic blocksize/timeout system, where if a node can
construct a block from their received transactions that meets the
current set blocksize or if there are some transactions and the maximum
block delay has been reached, they will submit the block for consensus.

It can be argued that the "tick leader" approach still has some benefits
over this proposed change. In the case that a "tick leader" design is
implemented, it is strongly suggested that the selection process for the
tick leader be modified, as Qubic's design allows for a bad node to know
when their assigned ticks are, which could allow them to coordinate with
other attackers to create "blackout" periods.

PoW

Qubic uses a custom PoW algorithm designed to produce "useful" outputs,
referred to as "UPoW". While this is an intriguing concept, it is one
that is directly tied to their own goals, and only serves to add
complexity and uncertainty to Monero's mining if implemented. The PoW in
our upgrade used by miners will remain as RandomX. This paper should
note that RandomX itself is not required to be the PoW method. Any PoW
algorithm used in Monero should be as close of an analog to individuals
in a collective voting. This includes mitigations against Sybil attacks,
as well as lowering the barrier to entry and reducing the effectiveness
of efficiency techniques (such as ASICs or other systems that reduce the
capability for normal users to compete).

Arbitrator

Qubic's design utilizes an "Arbitrator", which is a special network
participant which can remove and replace nodes that are deemed
problematic or subpar. While their implementation includes a method to
replace a rogue arbitrator with a 2/3rds supermajority, the power of the
arbitrator is still too powerful, with a myriad of different scenarios
and situations where its presence poses a threat to the entire
governance model. In our upgrade, the task of removing and replacing
faulty nodes is ran automatically via "node gossip", with nodes being
replaced by the "alternate" nodes. This ensures a per-epoch fault
tolerance of 1/6 at the smallest (the percentage amount of alternate
nodes), and a tolerance of \~½ at the largest (the percentage amount of
alternative nodes, plus the smallest number of node failures required to
prevent consensus).

It may be possible to address and mitigate the issues with Qubic's
arbitrator design. If this is proven feasible, implementing an
arbitrator-like participant may be desirable, as such a participant, if
proven to be acting in the interests of the network, would be a
significant safeguard against latent threats against the network.

*Fee-less Transactions*

Transactions in Qubic do not have any associated fees. While Monero
Republic could implement this, it would be a significant change to the
"tokenomics" of the network. Additional details about this change can be
found in the relevant section in Additional Add-Ons.

Known Limitations and Issues

The following is a list of known limitations and potential issues with
Monero Republic. While this paper is in its early stages, this area will
be in flux, and will likely see a large amount of iteration.

Sybil

The main challenge when attempting to move away from Nakamura consensus
is the issue of so-called "Sybil" attacks, where an attacker uses
multiple identities in order to obtain undue priviledge or advantage.
Due to the design of Monero Republic, this is a significant design
challenge, with a clear and strong malincentive for participants to run
multiple nodes. This is unfortunately an issue that Republic inherits
from Qubic, which too suffers from Sybil-related risks. "Naive" attempts
to resolve this issue, such as subnet-based filtering, do not fully
solve the issue and will usually cause more harm to honest participants
than good (for example, any form of naive heuristic filtering will
inherently necessitate the removal of nodes that use techniques to
anonymize their location, such as a darknet or proxy, regardless of
Sybil intentions).

Thankfully, there are some upsides to Republic that make it, in the
worst case scenario, only mildly worse than the equivalent PoW scenario.
Because participants are ultimately chosen by collective hashrate, any
Sybil attack could at most only take down the network for nine days
(this is assuming that manual intervention is not made by the remaining
honest participants to "override" the attackers and restart the network,
which is a potential mitigation strategy) before participants could
"vote" them out at the next epoch. Additionally, the hashrate required
to perform such an attack would be roughly equivalent to that of a 51%
attack in a traditional Nakamura consensus system, while still
maintaining higher-level "safeguards" against adverse network behavior.

Centralization

I am not aware of the current number of active Monero nodes, but I am
nearly certain that the count is higher than the chosen consensus size
of 1000. This is partially by design, as there should be a healthy
amount of competition between nodes, and having too large a consensus
size risks there being "consensus vacancies" that could be filled by
adversaries.

That being said, there are still centralization risks that this design
introduces to the network. A major challenge is that if an adversary has
the funds to pay miners above-emission rate, they will have an
insurmountable edge against all other participants. This means that the
network can be liable to attacks of a similar nature to the current
methods of Qubic, though the attack would be less effective in scope of
risks, and would require a larger investment in server infrastructure.

34% attack

If an attacker is able to amass a 34% "minority" of quorum members, they
will be able to temporarily prevent consensus, effectively "halting" the
network until the issue is resolved. In the case of a slowly increasing
"bad" minority, members of the group can be marked by other nodes in the
network as being "bad", and they can be replaced with nodes from the
"alternate" pool. As the alternate pool is limited in size (1/6th),
there is a limit to how many nodes can be "replaced". When this limit is
reached, any further failing nodes, alternative or otherwise, will still
be considered full members of the consensus. If the number of "bad"
nodes exceeds 50% (1/3 + 1/6), consensus is completely halted, and will
not be able to fix itself until the next epoch. While this scenario is
not perfect, it is preferable to the equivalent scenario in Nakamura
consensus.

Executive-Legislative Discrepancy

It should be noted that, similar to real world gubernatorial systems,
there is a gap between the "legislative" decisions made through
consensus and the "executive" actions taken by those who are tasked with
satisfying those decisions. This limits the real-world capability of
using the on-chain consensus for larger decisions, as it is entirely
possible for an arbitrary consensus to be reached that is incompatible
with reality. A severe example of this would be voting to set the value
of a universal constant (such as pi) to an arbitrary value (literally
impossible to execute, creating a disconnect), with a more mild example
being demanding upgrades or fixes that, while technically possible, are
outside of the current capability of the development team (functionally
impossible to execute, creating a disconnect). Republic is not directly
affected by these issues, as the consensus decisions described in this
paper are entirely isolated to within the confines of the network
itself, but the limits of this consensus design need to be understood to
prevent future developers from misconstruing the framework to be a
skeleton upon which a distributed autonomous authority, or DAO, can be
built: this is not the case, and attempting to do so will cause more
harm to Monero than good.

Implementation/Transfer Guide

Note: This plan is not yet complete; no decisions should be made that
rely on this plan until important sections (like the Upgrade/Failsafe
sections) are finished.

Transitioning from current Monero to this proposed change would be a
significant undertaking. This is a guide of a potential transition plan.
This does not plan for every contingency, and should not be taken as a
complete plan. Any transition made should have a significant amount of
planning and consideration taken into account, both in advance
conceptually and in practice after the programs for the implementation
have been finalized. This is not intended to replace any pre-existing
upgrade protocols utilized in Monero. This plan, while not explicitly
stated, is created under the presumption that the FCMP upgrade is fully
integrated.

1\. All of the code for nodes, wallets, and miners will need to be
completed in full, so that they are theoretically ready to be used in
the upgrade.

2\. A thorough auditing process, in addition to any audits done
beforehand, should occur, to make sure that both the code and concepts
behind the code are sound.

3\. Once this is completed, a "from-scratch" testnet and subsequent
stagenet with the upgraded code should be ran and tested, to ensure that
no issues still exist. If issues are found, they should be fixed, and
the relevant net should be ran again.

5\. If the "from-scratch" updates were successful, and all issues were
resolved, the relevant code for nodes, wallets, and miners should be
released for usage on testnet and stagenet.

4\. Once this is completed, testnet should be attempted to be upgraded
"live", using the upgrade instructions below. If it fails, make sure
that the "failsafe" in the upgrade works, address the failing issue, and
continue. If it works properly, the upgrade should be redone multiple
times until it "fails" in some way, in order to both ensure that any
latent issues are addressed and that the network can recover from any
upgrade failure.

5\. Once testnet has been upgraded, repeat step 4 with stagenet.

6\. Once stagenet has been upgraded, distribute the code for nodes,
wallets, and miners to all users. Work with 3^rd^ party developers of
Monero software to make sure that their code is ready for the upgrade.
Once this is done, announce a block for the upgrade to occur on.

7\. Once the block for the upgrade begins, attempt the upgrade. Keep
communication channels live and well-informed during this upgrade. If
the upgrade fails, ensure that the "failsafe" works properly, and assess
and regroup. If the upgrade succeeds, then the upgrade has been
successful.

Upgrade instructions:

In the interest of time, this has not been finished. If there is
increased interest in this section, this will be completed.

**Additional Add-Ons**

The following is a list of potential avenues for future development that
are opened up by Monero Republic, but are either too divisive,
experimental, or complex to be formally introduced as a part of this
upgrade. While this paper is in its early stages, this area will be in
flux, and will likely see a large amount of iteration.

Fee-less Transactions (and changes to emission)

In Qubic, normal on-chain activity is completely fee-less, with the only
on-chain fees being those associated with Smart Contracts. Transitioning
Monero to a fee-less transaction system may be a viable option after
this upgrade, as the overall cost of creating a transaction in this
system becomes much less significant. Changing to this system would
necessitate a modification to emission, as the overall percentage of
value given to nodes decreases over time, which reduces the capability
of honest participants to compete against malicious nodes.

The change proposed in this section would 1. remove all transaction fees
in the network, and 2. change the "tail emission" from a constant
157,680 XMR added per year to a constant annual percentage yield, likely
set no lower than 1% and no higher than 4%. The raw emission would
increase with each epoch, but following such that it would increase by
the given "emission percentage".

There are a number of arguments both for and against this proposed
change. A common opposition is that the removal of all transaction
"penalty" increases the chance for "spamming" behavior. Another point is
that the current valuation of Monero is made under the presumption that
the currency is, while technically infinite in supply, still linearly
capped in emission rate; changing to a percentage model would "inflate"
the currency at a much more rapid rate (even a small APY like 1% would
still result in the supply doubling once every \~70 years, with a higher
APY like 4% resulting in the supply doubling once every \~18 years).

The main argument for this change is that it would create strong
incentives for spending, while reducing the incentive for "hoarding"
behavior. Using Monero for spending is arguably its main use case, so
removing as many barriers to doing so would make sense. It should also
be noted that transaction fees, while currently considered to be low by
many, will likely increase naturally in order to "make up" for the
amount lost from emission decay. Transaction fees are a determinant
factor in both driving users away from those with too high of fees and
towards currencies with little to none. Removing transaction fees would
increase the amount of Monero users, which would also improve the
privacy of users.

With all of this said, much of the economic implications for different
emission models, as well as how the burden of network cost is split
between holders, users, and infrastructure, needs further research, and
should be looked into. As this is mostly out of scope for the needs of
this paper, that work will be left to future endeavors.

Archival Nodes

In the interest of time, this will be shortened for brevity's sake. It
may be desired (or in the case of ZK rollups, required) to maintain a
full record of transactions. In order to prevent a scenario where nodes
centralize this record in order to save costs, the record needs to be
saved in a "unique" manner, where each node's record is unique to
themselves, but the underlying information is consistent across all
nodes. The algorithm used for this needs to be easy to verify, and fast
enough in creation to not pose a challenge during range proof
generation, while not being fast enough that nodes can generate this
proof on-the-fly.

"Threadable" Transactions

The mixture of irreversibility and untraceability creates a strong
malincentive for Monero receivers to commit fraud and other undesirable
behavior. This puts senders at an elevated risk in a transaction, as
whatever they receive is more likely to be fraudulent than the payment
they send. Furthermore, the inability to properly "chargeback" incorrect
or mistaken transactions is a strong disadvantage when comparing
cryptocurrencies to contemporary currencies. This is a significant
blocking factor for a number of potential users.

Unfortunately, implementing a full "mediation" system (similar to the
Haveno protocol) within the currency would add significant complexity,
only to result in a sub-par solution, as the system would inherently be
reliant on "oracles" to deliver accurate real world info for
arbitration. Additionally, this process would circumvent local
jurisdiction on fraud, which may be undesirable.

The solution proposed is a new category of optionally traceable
currency, referred to as "threadable". This currency type would allow
for spenders to trace the coinbase movement of the relevant spend if an
issue arises before a "timeout" deadline. The exact timeout number is up
for debate, but I recommend somewhere from 30-90 days. The timeout could
also be variable, if desired, though this would create usability
challenges, similar to Monero's old "lockout" functionality.

Threadable currency can be "minted" at the time of a transaction by the
sender. Notably, wallets can delineate between threadable and
non-threadable currency, and each wallet has a special subaddress that
is only used for threadable currency (default addresses/subaddresses
will only accept non-threadable currency). This creates a barrier to
accidentally sending the wrong type of currency.

In a nominal threadable transaction, the transaction is entirely
private. Every time a threadable coinbase is transacted, its "timeout"
is reset. Once the timeout period has passed, the threadable coinbase
becomes a normal coinbase, and can be traded normally.

If a sender decides that the transaction is problematic within the
timeout period, they can submit a special request to the blockchain that
demands for any transaction data for the coinbase be privately sent to
the sender. The coinbase is frozen until the receiver agrees.

If the coinbase has been sent to another address, that address too is
demanded in the same way. This is done recursively for all addresses
that possess the coinbase.

The blockchain is "blind" to any external mediation process that occurs
with regard to the traced transaction. The trace can be stopped either
by the original sender or by both the sender and receiver. In the case
of the sender closing it, they will pay a small fee, as this implies
that the sender was incorrect in the trace. If it is closed by both, the
recipient pays the fee, as this implies that they were the one in the
wrong. Note that this fee is not intended as a direct or full penalty
for poor conduct, and should only be high enough to mitigate abuse of
the feature and undue stress on the network. The penalty is not intended
as a replacement or supplement for jurisdictional legal proceedings.
Also note that this system does not directly handle chargebacks. This is
intentional, as chargebacks should be handled by the parties themselves,
instead of the network. This allows for better jurisdictional compliance
and for better flexibility for the parties involved.

There are a number of potential negative effects of this system. It
increases the complexity of the currency, which could result in users
making improper decisions under the presumption of privacy or of the
opposite. Its existence could also lead to downstream effects in
restricted jurisdictions, where private transactions are outright
banned. In the case that a large proportion of transactions are made
utilizing this feature, the overall anonymity set of the network could
be significantly exposed. While the fee decreases the effectiveness of
spam attacks, a "black marble" attack would become more effective. There
are also some neutral effects, such as an arbitrage opportunity for
users willing to buy Threadable currency and wait for it to timeout to
be traded privately.

In my opinion, these tradeoffs are worth the potential UX improvements
of this feature; this can allow a potentially wronged party to fully
trace their transactions (preventing potential on-chain "mixers" from
obfuscating its location) without the party needing to obtain the view
key of their wallet and of unassuming recipients, or to coordinate the
obtaining of all individual transaction view keys involved. In addition,
it does this with no risk of unwanted 3rd parties viewing the
information without the consent of the sender and recipient, and with
minimal risk to the anonymity of non-Threadable transactions. Instead of
assuming that users won't want or need transaction traceability, it
minimizes the amount of traceability only to what is required.
