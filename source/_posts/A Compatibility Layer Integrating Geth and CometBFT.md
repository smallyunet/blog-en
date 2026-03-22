---
title: A Compatibility Layer Integrating Geth and CometBFT
date: 2025-09-06 00:30:00
tags: Plans
---

### Project Motivation

While analyzing the [Arc project](https://paragraph.com/@smallyu/%E5%AF%B9-arc-%E9%A1%B9%E7%9B%AE%E7%9A%84%E5%88%86%E6%9E%90), I discovered something quite interesting: Arc first developed a Rust-based implementation of the Tendermint consensus algorithm called malachite, and then built a compatibility layer called malaketh-layered to interface between Reth and malachite. In other words, Arc’s blockchain architecture looks like this:

```text
Reth -> malaketh-layered -> malachite
```

The result is a fully Ethereum-equivalent PBFT blockchain.

So, is there a similar architecture that directly combines Geth and CometBFT? Yes—[Berachain](https://paragraph.com/@smallyu/%E5%AF%B9-berachain-%E9%A1%B9%E7%9B%AE%E7%9A%84%E5%88%86%E6%9E%90) has developed something called [beacon-kit](https://github.com/berachain/beacon-kit), which does exactly that. In fact, Berachain’s mainnet was launched using this architecture.

However, beacon-kit has a problem: the code is overly “complex.” It introduces its own slot concept and includes Berachain’s economic model designs and LST staking mechanisms. So while beacon-kit is a viable engineering example of a Geth+CometBFT integration, it’s not built as a neutral tool—it comes bundled with opinionated features.

That’s why I believe a general-purpose, tool-focused compatibility layer is needed. I’m currently calling this project **EthBFT**. The goal of EthBFT is to provide a clean, open, minimal, tool-oriented architecture that integrates Geth and CometBFT. The architecture of the entire blockchain network would look like this:

```text
Geth -> EthBFT -> CometBFT
```

EthBFT focuses on two main tasks:
1. Obtaining block data from the Ethereum execution layer via the Engine API.
2. Submitting block data to CometBFT through the ABCI interface.

Although Geth is used here as an example, EthBFT should be compatible with all Ethereum execution clients, since Ethereum’s execution and consensus clients are inherently interoperable via RPC. Therefore, EthBFT is expected to support all Ethereum execution clients.

EthBFT is designed to avoid code-level coupling with either Geth or CometBFT. It runs as an independent process and can be launched separately. Geth and CometBFT can also be started independently. All three components communicate solely through RPC interfaces, and the relevant RPC endpoint configurations are specified in EthBFT’s config files.

This ensures complete decoupling between the three components.

### Confidence in PBFT

I used to think blockchain development would trend toward novelty and decentralization, but that doesn’t seem to be the case.

From Celestia adopting PBFT in recent years, to Hyperliquid improving upon it, and now Arc implementing its own PBFT consensus, it's clear that PBFT is still very much alive, especially in high-TPS scenarios.

PoW and PoS offer high decentralization but fall short on TPS and finality—areas where PBFT excels. In enterprise applications, decentralization isn’t always a priority.

You might ask: if decentralization isn’t a concern, why not just use a centralized server? But even when decentralization is compromised, blockchain still offers transparency and traceability of data—benefits that centralized systems often lack.

So, even as a nearly 30-year-old algorithm, PBFT continues to shine. There’s no issue at all in building a project around it.

### Project Outlook

EthBFT probably won’t attract much market attention, because people mostly care about whether a chain can issue tokens or offer arbitrage opportunities—not its technical architecture.

EthBFT is just a tool. If a developer wants a fully Ethereum-compatible chain with high TPS, without EthBFT, what are the options? I won’t go into all the comparisons and setups here, but I believe EthBFT fills this gap, and in a non-intrusive way. The world needs a tool like this.

### Skepticism Toward AI Technology

The [smallyunet/EthBFT](https://github.com/smallyunet/ethbft) project already has a basic framework and can run a minimal version, archived as [v0.0.1](https://github.com/smallyunet/ethbft/tree/v0.0.1). At this stage, Geth’s block height increases steadily, CometBFT is producing blocks normally, and the two are synchronized in block height. Of course, it's still very early, and given limited development time, there are definitely features that need improvement.

Previously, I said that [those who hype Cursor lack technical competence](/2025/04/12/People Who Hype Cursor Usually Lack Technical Skills/), because while AI can amplify your abilities, it cannot replace your understanding. All the code for EthBFT v0.0.1 was written by AI, yes, entirely, but this project has very clear goals. Try it yourself: without knowledge of Ethereum, Cosmos, or even general tech skills, can you have AI alone build a running project like EthBFT?

If your own understanding of the tech is unclear or flawed, AI won’t correct your mistakes—because it doesn’t know what “correct” means to you. AI will obediently follow your instructions, but if you’re vague, it will veer off-course, often leaving hidden traps in the code. You might think it's done, but it either leaves a TODO or writes a bunch of unnecessary logic based on its own assumptions.

So getting AI to write correct code isn’t easy. First, **you** need to understand it, and then you need to monitor it closely. AI is, and always will be, just an assistant.
