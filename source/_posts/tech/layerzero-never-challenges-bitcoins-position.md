---
title: LayerZero Never Challenges Bitcoin's Position
tags: Blockchain
date: 2026-02-12 22:16:07
draft_date: 2026-02-12 21:16:49
---

### Zero Network

On February 10, LayerZero announced the technical design of its Layer 1 network [Zero](https://layerzero.network/zero). Two interesting metrics stand out:

1. EVM compatibility
2. 2 million TPS

Yes, it claims throughput of up to 2 million transactions per second. That is 200 times Monad, the parallel EVM chain, 30 times Solana, 100,000 times Ethereum mainnet, and 200,000 times Bitcoin.

LayerZero's magic lies in an optimized version of [Jolt](https://jolt.a16zcrypto.com/intro.html), a zkEVM based on the RISC-V instruction set, running on GPU clusters and generating ZK proofs extremely quickly. In other words, once users submit a large number of transactions to Zero's Block Producers, the Zero network can generate proofs for block data very quickly thanks to GPU-cluster compute power.

The hardware requirement for producing blocks on Zero is roughly a 96-core GPU setup and 768 GB of RAM. That is obviously data-center-grade infrastructure. So would that not make the entire network highly centralized and effectively controlled by a handful of top Wall Street firms?

Because of the properties of ZK, Zero offers another feature: verifying ZK proofs requires only very lightweight hardware. A home computer is enough to validate block data.

That is an interesting point. To increase TPS, Zero centralizes the power to produce blocks while pushing the power to verify blocks to the extreme edge of decentralization. So is Zero centralized or decentralized?

### Bitcoin Network

The only true benchmark for a decentralized network is the Bitcoin network. Although Bitcoin is decentralized at the protocol level and all nodes are full nodes, in practice the hash power for block production is largely monopolized by a few major mining pools. Is the Bitcoin network still decentralized?

If you analyze only the concentration of block production power, it is genuinely hard to say which is better. That is also why the arrival of Zero feels confusing.

We trust Bitcoin's decentralization because every node has the full ledger. Thanks to the blockchain design in which blocks are hashed one after another, we can replay the entire transaction history and use that to determine whether newly produced block data is valid. PoW consensus is the so-called one-CPU-one-vote model: finding a valid hash is hard, but verification is easy, and that is how block production rights are validated. So overall, what we trust in Bitcoin is cryptography.

And for the Zero network, what we trust is also cryptography, specifically ZK cryptography. We are expected to trust that Block Producers generate proofs according to the circuit, and then thousands upon thousands of Verifiers can validate whether the block data is legitimate. That entire process is also guaranteed by cryptography.

You could argue that distributing Zero's source code may still introduce risk. For example, there could be a bug in the ZK proving circuit, or a vulnerability in the proof verification logic that gives attackers an opening. But every blockchain faces the same source-code risk, including Bitcoin. You cannot guarantee that Bitcoin's source code is absolutely secure either. So from that angle, you cannot really distinguish the two.

Returning to decentralization: Bitcoin guarantees the reliability of block data through transaction replay. Zero, by contrast, concentrates block production power in a small number of nodes, but with ZK technology it can push the power to verify blocks down to any lightweight device. That is a kind of decentralization that even Bitcoin cannot match. If Zero's Block Producers misbehave, all users can know it and feed that back to the settlement layer. If enough Verifiers disagree, the Block Producers will lose their right to produce blocks.

When you compare them from these angles, does it not almost feel as if the Zero network could stand on the same stage as Bitcoin, and that the rise of ZK technology might even give Zero a path to surpass Bitcoin?

### Consensus

Of course, I deliberately softened the core issue just now. When you run into this kind of confusion about blockchain architecture and do not immediately see a way through it, it helps to go back to the bible of blockchain technology, the [Bitcoin whitepaper](https://bitcoin.org/bitcoin.pdf), for inspiration.

The abstract of the Bitcoin whitepaper immediately identifies the core problem Bitcoin is trying to solve: double spending.

Zero does use ZK technology to solve the problem of blockchain data being tamper-resistant, but how does it solve double spending? The answer is that Zero uses PoS consensus and introduces validators. No matter what flavor of PoS it is, it is still PoS.

Using Ethereum's architecture as a reference, Ethereum has two layers: the execution layer, which executes transactions, and the consensus layer, which verifies which nodes have the right to produce blocks.

Zero's architecture is somewhat similar. It is divided into a block production layer and a settlement layer. The block production layer is the layer where large-scale GPU infrastructure executes blocks and generates ZK proofs, while the settlement layer is where PoS staking happens and where the system determines whether a node has the right to produce blocks.

You may feel like the thread got lost. Were we not talking about double spending? Why are we discussing block production rights again? Because if control over block production can be captured or abused, then block data, meaning transaction history, can be rewritten. One second you see a transaction succeed, and the next second you discover it has been rolled back. So once block production rights are properly guaranteed, the double-spending problem can also be addressed.

Now the picture is clearer. Zero introduces ZK technology and raises TPS by two orders of magnitude. That is genuine progress. But on the consensus side, it still uses PoS. For some Bitcoin purists who are faithful to Bitcoin's original mission, PoS is not even a serious censorship-resistant mechanism. If that premise fails, then no matter how powerful ZK is, it no longer matters.

### EVM

So Zero was never competing with Bitcoin in the first place, and it will not shake Bitcoin's position at all. Go back to Zero's [Manifesto](https://layerzero.network/blog/zero-the-decentralized-multi-core-world-computer). The title, Multi-Core World Computer, already makes it clear that Zero is aimed at Ethereum. The Manifesto also directly compares Ethereum and Solana on performance issues. Bitcoin is naturally absent.

And in practice, Zero will not shake Ethereum mainnet either. Ethereum's degree of decentralization is second only to Bitcoin, and running full nodes on consumer-grade hardware has always been Vitalik's vision: If the Foundation disappeared, would Ethereum still exist?

Taking it one step further, Zero will not shake Solana's position either, because Solana has its own distinctive ecosystem, as well as narratives tied to celebrities and politics, and those things are not easily replaced.

Overall, Zero can compete only within the EVM ecosystem, and it may directly weaken the outlook for Monad, the recently launched parallel EVM network. Any larger ambition than that is clearly still out of reach.