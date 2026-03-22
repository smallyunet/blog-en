---
title: Why Decentralized Cross-Chain Bridges Are Impossible
date: 2026-01-13 12:05:36
tags: Cross-Chain Bridge
---

To be precise, trustless cross-chain bridges are impossible. People generally equate decentralization with trustlessness, although in reality decentralization means trusting a permissionless group.

There is no need to get stuck on the terminology. All current cross-chain bridge schemes, and all future ones as well, will fail to achieve a form of cross-chain communication across physically isolated, asynchronous, heterogeneous chains that is truly pure, tool-like, mathematically provable, lightweight to operate, and decentralized.

Let us first list some existing cross-chain bridge approaches, using cross-chain transfer from BTC to ETH as an example:

1. The notary model, which is the centralized model. You send BTC to a centralized institution, and the institution then sends ETH to the address you specify. Its security depends entirely on that centralized institution, so there is not much more to say. Most cross-chain bridges today still use this traditional model, at most with an internal multisig setup.

2. TSS key sharding and multisig, which is relatively decentralized. You transfer BTC to a third-party chain such as Thorchain. On Thorchain, multiple nodes each hold key shares for assets on the ETH side. Each node independently scans and verifies BTC transaction information. Once a threshold is reached, the multisig becomes valid and assets are released on ETH.

    This model decentralizes the notary role by having multiple nodes jointly verify whether the source transaction is correct. At present, this is a relatively credible model that relies on community game theory. Because Thorchain itself is a blockchain, becoming a node that holds key shares requires staking tokens and meeting other conditions. TSS signature algorithms consume substantial computational resources, so key shares cannot be distributed to too many nodes at once, which means the number of share-holding nodes stays within a certain range. Like all PoS chains, it appears decentralized on the surface, but in reality it is permissioned; the only difference is how high the access threshold is.

    TSS does decentralize the bridge, but it introduces a third chain beyond BTC and ETH, and the security of that third chain depends on its own economic game. That is itself a source of risk. So this model is reliable, but not pure enough.

3. Merged mining, specifically in cases where the source asset comes from a PoW chain. The idea is to have BTC miners include some kind of proof information for the cross-chain bridge while mining, so that the bridge-related information is mined directly into BTC block data. In that case, the BTC block data itself would contain the cross-chain information. This approach would be highly reliable, but it is not practical, because miners will not cooperate with a cross-chain bridge in this way.

4. HTLC. At its core, this is a bilateral asset swap, and it has high real-time coordination requirements. Both sides must be online at the same time on BTC and ETH to exchange secrets. HTLC can only achieve asset exchange, not true cross-chain transfer, and it also involves exchange-rate issues.

5. A fully verifying light client, meaning running a BTC light client on ETH. For example, if every Geth node could synchronize and verify all BTC block data, then Geth itself would effectively become a lightweight BTC client, and could naturally relay BTC transaction information to smart contracts on ETH in real time and without third-party trust. But the cost of this is obviously too high. It would amount to a single client being compatible with two blockchains at once.

6. ZK cross-chain bridges. The idea is to run a BTC light client, generate proof circuits for all block information, and deploy a Verifier on ETH. Then ETH only needs to verify a very small proof to trust that the source information is correct. So where is the problem with the ZK approach? ZK is not magic. The issue is that you still need to deploy a Verifier on ETH. In other words, you do not need to care who generated the proof or how they generated it, but you still must trust the Verifier on ETH. Then the Verifier code itself, the developers behind it, and whether its origin is trustworthy all become problems again.

7. TEE hardware signing. For example, you synchronize BTC block data on an Intel server, then use the private key built into the TEE to sign it, proving that the information definitely came from that device. Then on ETH, you only need to verify that the signature came from that hardware device in order to treat the information as trustworthy. The biggest problem with the TEE approach is, naturally, its single point of failure.

When you compare all these approaches, it seems there is simply no satisfying answer. Why is that?

1. The Two Generals' Problem in distributed systems. This was the first problem in the history of computer science to be formally proven unsolvable. The exact definition is not important here. What matters is that science already proved it has no general solution.

2. The Oracle Problem in computation theory. Every blockchain is itself a deterministic state machine. It is closed, verifiable, and reproducible. If it wants to communicate with the outside world, it inevitably has to introduce a trust model, economic game theory, cryptographic proofs, or other such mechanisms. Once that happens, the cross-chain solution is no longer pure.

So the problem of decentralized cross-chain bridges already touches the theoretical boundary of computer science. It is not that the industry is incapable. It is that the problem itself has no solution.

3. Interestingly, Vitalik already expressed a [view](https://x.com/VitalikButerin/status/1479501366192132099) in 2022: the future of blockchain is multi-chain, not cross-chain. Because even if a reliable cross-chain tool solved the communication problem, ETH would still need to trust BTC's consensus mechanism. If the BTC network forked, ETH could not follow BTC's fork and reassign funds accordingly. That too is an unsolvable problem.

The technical boundary exposed by cross-chain bridges can teach us two things:

1. There is no trustless technical solution, only trust transfer and trust minimization.
2. Engineering architecture can never be perfect. Most of the time, it requires trade-offs.