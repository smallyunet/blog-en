---
layout: post
title: "The Awkward Parts of Solana’s Technical Design"
date: 2026-09-19 21:58:00
collection: ai
tags: AI Article Library
lang: en
---

If you only look at the results, Solana is very much like blockchain technology has finally caught up with Internet products: blocks are produced quickly, handling fees are low, application responses are close to real-time, and the on-chain transaction experience can even be packaged into a normal app.

But if you have actually written a Solana program, chased transactions, run an indexer, or read its validator implementation, you will have a lingering awkward feeling:**Solana is of course a blockchain, but the way it solves problems is increasingly unlike what people traditionally understand as a blockchain.**

Bitcoin regards low performance as the cost of decentralization; Ethereum regards the ability of all nodes to repeatedly execute and verify state transitions as the bottom line. Solana took another route: it first set the goal of "a single chain must have exchange-level performance", and then designed the system into a real-time database with cryptographic proof that is replicated by high-end machines.

Therefore, Solana’s high performance does not come for free. It just shifts the complexity from "slow chain, expensive transactions" to several other places: validator hardware, network topology, leader scheduling, account model, program permissions, transaction structure, RPC service and off-chain index.

This article does not discuss the price of SOL, nor does it deny that Solana has formed a huge application ecosystem. There is only one problem:**When a public blockchain becomes more and more like a high-performance transaction infrastructure, is it improving the blockchain, or is it bypassing the original most difficult and important constraints of the blockchain?**

## 1. PoH sounds like consensus, but it is actually just a clock for queuing transactions.

Solana's most famous concept is Proof of History. This name can easily make people think that PoH, like PoW and PoS, is a consensus mechanism that determines which chain is valid.

Not really.

The Solana white paper defines PoH very clearly: PoH is a sequence of hashes that can only be calculated sequentially but can be verified in parallel to prove the sequence of events and the passage of time; it needs to be used in conjunction with consensus algorithms such as PoS. What really handles fork selection and validator voting is PoS consensus logic such as Tower BFT. PoH is mainly responsible for providing a cryptographic clock that is verifiable across the entire network.

This design is indeed smart, but it also exposes Solana's first layer of "non-blockchain feeling": traditional blockchains allow decentralized nodes to compete or negotiate the order of transactions, while Solana first designates a leader, who sorts and executes transactions in its own slot, and then broadcasts the results to other validators for recalculation and voting.

The Solana white paper even describes it straightforwardly: a leader generates a PoH sequence at any time; the leader sorts user messages, executes transactions on the current state in RAM, and then publishes the transaction and final state signature to the verifier; other nodes repeat the execution and confirm the results. [Solana White Paper](https://solana.com/solana-whitepaper.pdf)

The engineering nature of this structure can be easily summarized:

> In each short time window, the entire network hands over the sorting power to a pre-selected server, and other servers then verify it.

This is still a Byzantine fault-tolerant replication state machine, but the product form is very close to "rotating master database + multi-copy confirmation". Part of Solana's throughput advantage comes from the fact that it does not require each transaction to compete for a long time in a network-wide public mempool, but instead sends the transaction to the current or upcoming leader as quickly as possible.

The price also appears: the leader is predictable, and the transaction flow is naturally concentrated on a few nodes that are about to produce blocks; low-latency routing, direct connection to the leader, professional RPC, block engine and private order flow will become more and more important. The chain is still open at the protocol layer, but high-quality transaction entrances begin to show the characteristics of exchange computer rooms - people who are closer to the matching engine are naturally faster.

## 2. The premise of the so-called parallel execution is that the developer first reports the database reading and writing.

Solana often touts Sealevel's ability to execute trades in parallel. There's no mystery why it can be parallelized: each transaction must pre-list the accounts it will read from and write to, and mark which accounts are signers and which are writable. The scheduler dares to execute in parallel only when it sees that the writable accounts of the two transactions do not conflict.

In other words, Solana does not make any smart contract magically automatically parallel; it requires developers to explicitly tell the runtime the "read set and write set" of this state access before the transaction is submitted.

This is very similar to the concurrency control of a high-performance database, and also very different from the calling experience of traditional smart contracts.

In the EVM, the caller usually gives the target contract, calldata and gas, and the contract reads the required storage by itself at runtime. When it comes to Solana, the caller must not only know "which program to call", but also "which accounts the program will touch this time" and insert these accounts into the instruction in the correct order. Cross-program calls will also be subject to the same set of account boundaries.

This design passes the cost of parallelism onto the SDK, front-end, and application developers:

- The client needs to understand the data layout inside the contract, not just the ABI;
- Missing account transfers, incorrect order, and incorrect permission markings may cause transactions to fail before and after execution;
- Addresses such as PDA, ATA, mint, token account, vault, authority, etc. need to be derived in advance;
- A complex operation often requires first querying the on-chain status, then assembling the account list, then simulating the transaction, and finally sending it;
- Once the protocol upgrade changes account requirements, the old client may immediately become invalid.

This is why Anchor has almost become the de facto standard for Solana development: it not only improves efficiency, but also shields developers from the friction of the native account model. A virtual machine that requires a large framework to restore a normal development experience, it is difficult to argue that the underlying abstraction itself is natural.

More importantly, parallelism does not automatically eliminate hot spots. As long as a large number of transactions need to be written to the same pool, the same order book, the same global counter, or the same state account, they must still be serialized. Solana’s official transaction pipeline documentation directly lists `AccountInUse`, `WouldExceedMaxAccountCostLimit` and `TooManyAccountLocks` Wait for scheduling errors. [Solana Transaction Pipeline](https://solana.com/docs/core/transactions/transaction-pipeline)

So Solana’s real capability is not “any business can be parallelized”, but:**Only businesses that are split into non-conflicting state shards by developers in advance can be parallelized.**

The blockchain does not eliminate the problem of database sharding. It just renames the shard key to account and leaves the sharding design to the contract developers.

## 3. Solana’s “contract” is actually a stateless program, and the state is a bunch of scattered accounts.

Solana's official documentation clearly states: Program is an executable account that stores sBPF bytecode and is stateless; all variable states are placed in other data accounts and explicitly passed in by instruction. [Solana Programs](https://solana.com/docs/core/programs)

This goes against many developers’ intuition about “smart contracts.” In EVM, a contract address usually represents code, storage, and identity at the same time; in Solana, the program is more like a stateless processing function, and the on-chain state is like a set of external records addressed by a public key.

The result is that instead of "calling a stateful contract," Solana applications are doing the following:

1. Find the right program;
2. Derive or query a batch of data accounts;
3. Indicate the read and write permissions of each account;
4. Give the account and parameters to the program;
5. It is up to the runtime to check whether the program has permission to modify these accounts.

Program Derived Address (PDA) further strengthens this sense of database. The PDA does not have a private key, the program derives the address via seeds and bumps, and gains the ability to "sign" it at runtime. It's very practical, but it also means that Solana development revolves heavily around address derivation, account ownership, and serialization layout, rather than around the business state itself.

Even if a user "holds a certain token", the bottom layer often does not have a balance field under the wallet address, but the wallet has a token account managed by the Token Program; the Associated Token Account just agrees on a standard derived address between owner and mint. For application developers, this means that creating accounts, closing accounts, rent-exempt balances, authorities, and delegates all become routine business logic.

Solana's official account document shows that each account contains fields such as lamports, data, owner, executable, rent_epoch, etc.; only the owner program can modify its data or deduct its lamports, and the account must hold a minimum balance related to the data size to be resident on the chain. [Solana Accounts](https://solana.com/docs/core/accounts)

This model is not wrong, and is even very suitable for optimizing parallelism. The problem is that it splits the "smart contract platform" into a program loader, an account database, a permission system and a client address orchestrator. What developers face is not a simple on-chain computing abstraction, but a set of system programming interfaces for scheduler services.

## 4. A transaction is not an expression of intent, but a list of resources in a 1,232-byte box.

Solana's legacy and v0 transactions have long been subject to a 1,232-byte cap. This number is not an economic parameter, but the packet size obtained by subtracting the network header from the IPv6 minimum MTU of 1,280 bytes. The official document also lists: the old format locks up to 64 accounts, each Ed25519 signature occupies 64 bytes, and the recent blockhash is valid for 150 slots. [Solana Transactions](https://solana.com/docs/core/transactions)

This reflects the design ethos of Solana: the on-chain transaction format is first subject to network packets and pipeline throughput, rather than to the convenience of developers expressing complex operations.

Therefore, developers need to use Address Lookup Table to compress the 32-byte public key into a lookup table index; they need to reduce signers as much as possible; they need to split large operations into multiple transactions; they need to simulate compute units; they need to handle blockhash expiration, re-signing, retry and confirmation levels. The new version of the transaction format continues to improve these limitations, but the historical baggage will not disappear. Instead, it will create compatibility costs for the coexistence of legacy, v0 and new formats.

Computing is also strictly budgeted. The default value given by Solana's official documentation is 200,000 CU per non-built-in instruction and a maximum of 1.4 million CU per transaction; the default heap of sBPF is only 32 KiB, with an adjustable upper limit of 256 KiB, and the upper limit of the traditional CPI instruction stack depth is 5. [Compute Budget](https://solana.com/docs/core/fees/compute-budget) [Program Limits](https://solana.com/docs/core/programs)

These limitations help maintain determinism and high throughput, but also illustrate that Solana's so-called "general-purpose computing" is a tightly segmented system for real-time tasks. You are not simply paying for computation, but you are requesting limited CPU, memory, account locks, and package space from a high-performance scheduler.

What’s even more awkward is that the priority fee is calculated based on the applied CU limit rather than the actual usage; if the application is too high, you will pay for the unused computing budget. The official documentation even directly reminds developers not to pay for unused CUs. [Solana Fee Structure](https://solana.com/docs/core/fees/fee-structure)

This is not like a ledger that only cares about whether the state transition is valid, but more like a multi-tenant server that requires callers to fill in a resource application form in advance.

## 5. The program can be upgraded by default: "Code is the law" is downgraded to "Permissions are the law"

One of the most attractive narratives of traditional public blockchains is that once deployed, the code cannot be tampered with. However, Solana's loader-v3 program can be upgraded as long as the upgrade authority is retained; only by actively revoking the upgrade authority will the program become permanently immutable. When upgrading, new bytecode is first written into the buffer account and then replaces the contents in the ProgramData account. [Solana Program Deployment](https://solana.com/docs/core/programs/program-deployment)

Solana's own loader interface document even clearly reminds: Upgradable programs allow the authority to update the program at any time, which will break the code-is-law convention that "the code is immutable once it is uploaded to the chain." You should be cautious when calling programs that still retain upgrade permissions. [solana-loader-v3-interface](https://docs.rs/solana-loader-v3-interface/latest/solana_loader_v3_interface/)

Of course, Ethereum also uses proxy contracts extensively, and multi-signature and governance upgrades have long been the norm in the industry. The difference is that the scalability of EVM is usually built additionally by the application layer through the proxy mode; Solana makes scalability a first-class capability of the program deployment system.

This makes Solana more suitable for quick fixes and iterations, but also shifts the trust boundary from public code to upgrade authority:

- Is authority a single signature, multi-signature or governance contract?
- Set timelock?
- Can users log out before upgrading?
- Does the source code displayed on the front end correspond to the current bytecode?
- Can an upgrade immediately change the asset control logic?

Users who think they are interacting with an immutable contract may actually be calling a backend service that can be hot-updated at any time by a small number of keys. On-chain execution does not eliminate SaaS-style administrator rights, but only replaces the administrator account with an upgrade authority.

## 6. Low fees have not eliminated congestion, but have turned congestion into account-level bidding and competition for transaction entrances.

Solana’s fees have long been low, which is a real advantage. But a low base fee does not mean there are no scarce resources.

In Solana, scarce resources include: the current leader’s ingress bandwidth, block calculation budget, writable locks of hotspot accounts, transaction package space, and low-latency forwarding capabilities. Therefore, when everyone competes for the same new currency, the same AMM pool, or the same liquidation opportunity, competition will not disappear, but will only change from the "global gas price" to:

- local competition for the target writable account;
- priority fee bidding;
- Jito tip and block engine sorting;
- Better RPC, private lines, and transaction reissue strategies;
- A network location closer to the leader.

This is why the trading robots on Solana are not like ordinary on-chain scripts, but more and more like high-frequency trading infrastructure. To grab the first transaction, it is not enough to just be able to write contracts. You also need to understand slot, leader schedule, QUIC, stake-weighted QoS, block engine, bundle, account lock and fee estimation.

Solana makes a single transfer very cheap for ordinary users, but pushes high-value sorting competition to a professional level. The result is not that MEV disappears, but that MEV’s technical threshold is higher, the infrastructure is more centralized, and it is more difficult for ordinary developers to see the complete order flow.

## 7. High-performance nodes are not nodes for ordinary people, but data center servers.

Whether a chain is decentralized cannot just count the number of validators, but also depends on whether an ordinary participant can independently verify it.

The recommended configuration for validators officially given by Agave includes: at least 12 cores and 24 threads, 256 GB of memory, and high-endurance NVMe for accounts and ledger respectively; pledge nodes require at least 2 Gbit/s symmetric network, and 10 Gbit/s is recommended. 512 GB memory recommended for RPC nodes. The documentation also makes it clear that running mainnet validators in Docker is not recommended and that cloud environments also require significantly higher operational capabilities. [Agave Validator Requirements](https://docs.anza.xyz/operations/requirements)

This is not a network that "can be verified even if I have an old computer at home." It requires professional hardware, professional network and continuous operation and maintenance.

Solana supporters will say that the hardware will get cheaper and cheaper, which is a reasonable counterargument. However, the history of blockchain does not only move in the direction of hardware price reduction: the state continues to expand, throughput continues to increase, bandwidth continues to increase, and node requirements will also increase simultaneously. Moore's Law may not automatically offset the ambitions of the chain itself.

The deeper problem is that Solana bases its scaling on the assumption that all validators are catching up with the latest hardware. The higher the performance, the more like a data center the full node is; the more like a data center the full node is, the easier it is for verification rights to be concentrated in professional operators, hosting service providers and a few high-quality network areas.

This is a very clear route:**Instead of adapting the chain to normal nodes, nodes are upgraded enough to catch up with the chain.**

## 8. There is a lot of data on the chain, but developers still cannot do without commercial RPC and off-chain indexing

Solana is a set of public ledgers in theory, but in practice it is difficult to complete production-level applications with just a cheap public RPC.

High throughput means massive slots, transactions, account changes, and logs. If an application requires historical events, address behavior, token holdings, program logs or real-time monitoring, it usually must rely on professional RPC, Geyser data flow, Yellowstone gRPC, self-built database or third-party indexing service.

Solana's official recommended memory for RPC nodes reaches 512 GB, which has already shown that "reading the chain" itself is a heavy infrastructure work. At the same time, the Solana program does not automatically provide the business indexes that developers want like traditional databases; the program log is not a reliable, permanent, and arbitrarily queryable event database. Developers still ultimately have to extract on-chain data into PostgreSQL, ClickHouse, Kafka, or a dedicated indexing system.

So a very counter-intuitive reality emerged: Solana made execution extremely fast, but made "knowing what just happened" an independent business.

The chain is responsible for fast writes, and commercial RPC and indexers are responsible for usable reads. For ordinary teams, the truly usable Solana is not just an open protocol, but also includes several paid data services. The so-called permissionless often only exists at the transaction verification layer; at the product layer, developers are still surrounded by API packages, rate limits, historical data retention, and vendor compatibility.

## 9. What is exposed by shutdown accidents is not “too many bugs”, but that the system margin is too small.

Any software will have bugs, and it is not rigorous to deny the entire network based on one incident. What's really concerning is that Solana incidents can often propagate throughout the cluster along highly coupled data paths.

In February 2023, abnormally large blocks and third-party block-forwarding services jointly triggered the propagation of duplicate data. Turbine's deduplication logic was overwhelmed, and the network entered vote-only mode, which was eventually manually restarted by the verifier and downgraded to a stable version. Solana’s official incident report records that normal block production did not resume until the next day. [2023-02-25 Outage Report](https://solana.com/news/02-25-23-solana-mainnet-beta-outage-report)

February 2024, Program Caching `LoadedPrograms` An implementation flaw caused the mainnet to stop finalization. The engineering team released v1.17.20, and the verifiers jointly selected the recovery slot, prepared snapshots, and restarted the cluster. The incident lasted about five hours. [2024-02-06 Outage Report](https://solana.com/news/02-06-24-solana-mainnet-beta-outage-report)

What is worth noting is not the cheap conclusion that "Solana once stopped", but the recovery method: releasing a unified repair version, coordinating validators, selecting a mutually recognized slot, and restarting from snapshot.

This is completely pragmatic in terms of engineering, but it again shows how organized Solana is. In the face of extreme failures, it is not like a network that relies on slow, heterogeneous nodes to naturally converge, but more like a global cluster jointly maintained by a core development team and professional operation and maintenance providers.

Solana's speed is built on tight collaboration, high levels of optimization, and small safety margins. When the system is normal, these features bring amazing throughput; when the system is abnormal, the same coupling will quickly turn local problems into network-wide problems.

## 10. Multiple clients are making up lessons, but it just proves that the previous risks are real.

Today’s Solana is no longer just the original Solana Labs client. The original implementation was forked by Anza and maintained as Agave, and independent clients such as Firedancer are also advancing. This is one of Solana's most important engineering improvements: different teams and different code bases can reduce the risk of a single implementation bug causing the entire network to fail at the same time.

But this incident also proves the problem from the side: when the protocol complexity is high, the performance path is extremely optimized, and the verifiers run highly similar clients for a long time, "normative decentralization" cannot automatically become "fault independence." If the vast majority of nodes execute the same flawed logic, no matter how many nodes there are, they are just copies of the same bug.

Multi-client will increase resilience without removing the fundamental constraints of the Solana route. New clients still have to keep up with the same high-throughput chain and be compatible with the same set of account locks, transaction formats, PoH, Turbine, and state growth. What it reduces is the risk of implementation concentration, not the complexity of the system itself.

## 11. Solana’s real innovation is also its most dangerous bet

It must be admitted that many of Solana's designs are not stupid mistakes, but very coherent engineering choices:

- PoH reduces the communication overhead of nodes in time and sequence;
- The leader pipeline allows transactions to be continuously input and propagated;
- Explicit account lists provide a deterministic set of reads and writes for parallel scheduling;
- Stateless programs and independent data accounts facilitate runtime control of permissions;
- sBPF, compute budget, and resource caps ensure predictable execution;
- A high number of validators are exchanged for high throughput in the global state of a single chain;
- Upgradeable procedures allow applications to quickly fix vulnerabilities.

Taken individually, each item is self-evident. What's really disturbing is that they all point in the same direction:**Maintain a nominally permissionless global state machine using more centralized systems engineering methods.**

Solana didn't solve the "impossible triangle", it just made an extremely aggressive bet:

1. Hardware progress can always keep up with status and traffic growth;
2. Professional validators are still sufficiently decentralized;
3. The leader’s centralized, instantaneous ordering power will not evolve into long-term infrastructure power;
4. Commercial centralization of RPC, indexers, block engines, and transaction portals will not erode protocol openness;
5. Highly optimized complex implementations are able to maintain sufficiently low systemic failure rates over the long term.

As long as these five conditions are met at the same time, Solana will appear to be an era ahead of the traditional blockchain.

But as soon as any one of them fails, the market will suddenly discover: everyone thinks they are buying a faster public blockchain, but they actually rely on a financial operating system that requires a core development team, data center-level validators, commercial RPC, professional indexers and low-latency transaction channels to jointly maintain it.

## Conclusion: Solana is not unlike the blockchain, it is forcing the blockchain to admit that it wants to become a server

Solana's biggest technical achievement is to prove that a public blockchain can achieve a response speed close to that of Internet applications. Solana’s biggest technical question comes from the same place: How much of what the blockchain is meant to protect does it retain in order to achieve this kind of speed?

When transactions are ordered by a predictable leader, when parallel execution requires clients to declare read-write accounts in advance, when contracts are stateless programs that can be hot-updated, when nodes require 256 GB of memory and multi-Gbit/s networks, when historical data queries rely on commercial RPC, when network-wide failures require unified versions, snapshots and manual coordinated recovery - Solana certainly still meets the technical definition of blockchain, but it has moved away from the original imagination of "any ordinary person can participate independently, verify at low costs, and do not need to trust intermediate infrastructure".

Therefore, the most alarming thing about Solana’s technical route is not a bug or an outage, but that its success is too tempting: low fees and high TPS will make the market ignore who provides these performances, what hardware provides them, which centralized entrances provide them, and who the system ultimately returns trust to.

**Solana looks like the future of blockchain, and it may just be the most mature project implementation since centralized financial infrastructure puts on the cloak of blockchain.**

---

*Note: This article discusses technical routes and trade-offs, and does not constitute a deterministic conclusion on network security, nor does it constitute investment advice. The restrictions and parameters in this article are subject to the official documents of Solana and Agave accessible on September 19, 2026; they may change after the protocol is upgraded. *

## main information

1. [Solana Whitepaper](https://solana.com/solana-whitepaper.pdf)
2. [Solana Accounts](https://solana.com/docs/core/accounts)
3. [Solana Programs](https://solana.com/docs/core/programs)
4. [Solana Transactions](https://solana.com/docs/core/transactions)
5. [Solana Transaction Pipeline](https://solana.com/docs/core/transactions/transaction-pipeline)
6. [Solana Compute Budget](https://solana.com/docs/core/fees/compute-budget)
7. [Solana Fee Structure](https://solana.com/docs/core/fees/fee-structure)
8. [Solana Program Deployment](https://solana.com/docs/core/programs/program-deployment)
9. [Agave Validator Requirements](https://docs.anza.xyz/operations/requirements)
10. [2023-02-25 Solana Mainnet Beta Outage Report](https://solana.com/news/02-25-23-solana-mainnet-beta-outage-report)
11. [2024-02-06 Solana Mainnet Beta Outage Report](https://solana.com/news/02-06-24-solana-mainnet-beta-outage-report)
