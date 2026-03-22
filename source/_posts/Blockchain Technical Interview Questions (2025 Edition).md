---
title: Blockchain Technical Interview Questions (2025 Edition)
tags:
  - Blockchain
  - Interview Questions
date: 2025-07-06 18:38:03
draft_date: 2025-06-30 14:26:03
---

Compared to the 2023 version of [Blockchain Technical Interview Questions](/2023/07/12/Blockchain Technology Interview Questions/), which focused more on a macro perspective, this edition leans slightly more toward engineering practice and includes more technical details. These two versions complement each other; they are not upgrades. The questions here are purely based on my personal experience, just like many interviewers who only ask what they themselves know and will not ask about things they do not understand. So the scope and depth of these questions are inevitably limited by my own level of knowledge:

1. Why is the Ethereum client divided into execution layer and consensus layer?
2. How does Ethereum's PoS process work, and how do you initialize a PoS network?
3. What are the soft fork and recovery mechanisms in Ethereum PoS? Is Cardano's PoS the same as Ethereum's?
4. What types of Ethereum nodes exist, and what are their use cases?
5. Why is EVM execution single-threaded? Why has no team in the world succeeded in building a “parallel EVM”?
6. Does the Solidity language have garbage collection (GC)? How does it handle dynamic memory allocation?
7. In what scenarios is inline assembly necessary in Solidity?
8. Are you familiar with the PBFT consensus? What’s its general process?
9. What is the formula for PBFT fault tolerance, and why that number specifically?
10. Why does PBFT require a second round of voting?
11. What is Solana’s consensus mechanism like? Is TowerBFT voting on blocks?
12. Why can Solana’s smart contracts be executed in parallel while Ethereum’s cannot?
13. What is the upgrade process for Cosmos nodes? How is it different from Ethereum’s? What are the risks of this model?
14. What is the general flow of an Op Rollup? Which part does ZK Rollup optimize based on Op Rollup’s model?
15. How does asset bridging work between Ethereum L2s? Technically, how does it differ from bridging assets across entirely different networks?
16. Ethereum recently had a major upgrade introducing EIP-7702—what is it for? How is it related to Account Abstraction (AA) wallets?
17. What interesting blockchain-related technical topics have you personally explored?

That’s all the questions I can think of at the moment. For more in-depth, engineering-oriented topics, I only have a general idea of the direction—I haven’t personally worked on them. Still, my experiences over the past couple of years have been relatively rich. Comparing the two versions of the question list reveals a lot of changes. I hope I can continue to improve and not lose my way.

If you’re a job seeker in the blockchain industry—especially if you’re a relatively inexperienced engineer—don’t be intimidated by the questions above. In real interviews, you’ll rarely encounter such deep and thought-provoking questions. More commonly asked questions include things like: “What are the common fields in an Ethereum transaction?”, “How do you cancel a transaction that’s already been sent?”, “What is a reentrancy attack in Solidity?”, “What components make up the Op Stack?”, “What is `create2` in Ethereum contracts?”, etc. Go confidently into job hunting—there aren't that many people who truly understand the technology deeply.

There’s currently an issue in the blockchain industry: there is no systematic theoretical framework, only some fragmented, frontier engineering efforts. For example, compare this with the field of programming languages—from Church and Turing’s models of computation, to functional programming languages, compilers, type systems, and so on—after decades of development in academia and industry, there’s both highly abstract theoretical support and practical industrial applications. It’s relatively mature. But blockchain is quite new—it originated in 2008 and started entering the public eye around 2013. In just a few years, there hasn’t been enough time to build a solid academic system. Instead, each project team goes its own way, creating their own standards and defining their
