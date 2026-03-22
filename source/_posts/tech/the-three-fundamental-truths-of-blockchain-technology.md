---
title: The Three Fundamental Truths of Blockchain Technology
date: 2025-11-03 15:37:35
tags: Blockchain
---

There are two facts:

1. Yin Wang is an expert in programming language theory and theoretical computer science.
2. In blockchain theory, there does not seem to be anyone at that level yet, especially someone willing to speak publicly, teach courses, and systematically share knowledge.

There are reasons behind this:

1. Computer science has developed over several decades, while blockchain has existed for only about a decade and a half.
2. Blockchain itself, from the very beginning, was a product of engineering integration rather than theoretical innovation.

This leads to very different phenomena:

1. Once you master the foundations of computer science, such as lambda calculus, Turing machines, and models of computation, and understand the essence of computation, that knowledge remains reusable for the long term. It does not become obsolete. No matter how higher-level languages and frameworks change, the core of computation does not change.
2. Blockchain engineering seems to lack this kind of foundational theory. There is little that feels permanent, and little that demands long-term accumulation. Practitioners tend to be younger, the field gets many newcomers, and the work often centers on system integration and wiring up SDKs.

So in the technical world of blockchain, are there any theoretical truths that are enduring, reusable, and immune to changes in upper-layer frameworks?

The three fundamental truths of blockchain technology are:

1. Consensus. How to solve the Byzantine Generals Problem.
2. Cryptography. Tamper-resistance and verification grounded in mathematics.
3. Incentives. A sociological game engine that keeps consensus running over time.

If you master these three areas, then no matter how blockchain keeps reinventing itself on the surface, and no matter how industry trends change, you do not need to worry, because blockchain is fundamentally about solving these problems.

What does it mean to have truly mastered a truth? If I can read it and understand it, does that mean I know it? Does that mean I have mastered it?

The standard for mastering a truth is this: you can use that truth to reconstruct knowledge from scratch.

In computer science, if the world were destroyed and someone gave you only paper and a pen, and you could rebuild lambda calculus, data structures, an interpreter, a programming language, and even more, without depending on textbooks, frameworks, or APIs, then that is what it means to have mastered truth.

The point of truth is that it lets you understand why knowledge must exist in the form it does. That is also what Yin Wang's courses are trying to teach. Yin Wang does not teach knowledge; he teaches Yin Wang-style truths. That is why I have always believed his courses are excellent and highly valuable.

Likewise, in the blockchain world, if you can start from scripts and work your way up to consensus, cryptography, and incentives, you do not necessarily need to rebuild every detail, but you do need to understand why existing systems are designed the way they are. At that point, you are close.

It is worth noting that smart contract programming languages are not part of the truth layer. Whether it is Bitcoin Script, Solidity, Move, or Cairo, they are all just DSLs for expressing transaction logic. They are different ways of defining the rules by which blockchains execute transactions. They are important, but they are not yet at the truth layer.

If you insist on describing the truth layer of smart contracts, perhaps it can be expressed as a deterministic state transition function. No matter how languages change, this truth remains:

```
State_t+1 = f(State_t, Transaction)
```

The next state comes from the previous state plus the state changes caused by some transaction. Simple, right? But this article is focused on Yin Wang-style truths in the blockchain world, so the three fundamental truths remain the same: consensus, cryptography, and incentives.