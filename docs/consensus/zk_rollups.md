# ZK Rollups

NOTE: This section, more than any other section, needs work done, due to the complexity involved.

Monero Republic incentivizes nodes to be productive and useful, which will allow for strong and reliable network infrastructure to be profitable and supported directly by miners. An additional benefit is that computationally heavy tasks that do not generate emission directly, despite being useful, can be undertaken. A good example of this in action is ZK rollups, where a large challenge of implemention comes from the generation of proofs, which is computationally difficult.

In Monero Republic, ZK proofs (likely using STARKS as opposed to SNARKS) will be generated and "rolled up" into an overall "chain proof" at the end of every epoch. The work for these proofs will be split evenly across all nodes. Any node that fails to complete their share in a reasonable amount of time will have their payment withheld, and the payment will be given to an alternate node which completes the share. As payment is not given to nodes until after the proof is created and verified, nodes have an exceedingly strong incentive to not attempt to skip proof generation. Furthermore, as miners are paid by nodes, miners have a strong incentive to only mine for nodes that will complete the proof generation.

The resulting proof should be incredibly small (<10 MB at the absolute largest), which should improve syncing time for wallets dramatically.

It should be noted that the exact details of the proof system are not known, and requires further research and discussion from those with more knowledge about ZK-related technologies.