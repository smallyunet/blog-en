---
title: Designing an MCP Blockchain Based on AI Semantic Execution
tags:
  - AI
  - Blockchain
date: 2025-10-13 17:22:58
draft_date: 2025-10-13 15:31:03
---

### MCP blockchain

MCP (Modal Content Protocol) is a protocol specification used by AI agents to interact with external tools, and an MCP Server is the concrete component that carries out that external interaction.

The idea of an MCP blockchain is this: first, it is a blockchain, and second, every node natively supports MCP-protocol RPC interfaces so that it can directly accept requests from AI agents. Every MCP request is a transaction, specifically a smart contract call. That means the blockchain records the full history of all MCP interactions.

The MCP execution engine inside each node has two parts. For internal interactions, which is basically what the EVM does, the node only needs to maintain internal state transitions and write the result into a local KV database. For external interactions, it only records the request that should be executed, without making the external call immediately. In practice, every request still changes local state, so these transactions are replayable, which means the integrity of node data can be verified by hash.

As for those external execution requests, they can be handled by external executor nodes, one possible role in the system. Each external request contains detailed parameters, such as how many executors are required and how the result should be verified. Part of the transaction fee can be used as an execution reward for those executor nodes. If an executor behaves maliciously, there should naturally be a corresponding punishment mechanism.

For external calls, the key issue is how to verify the result of external execution. That part can be defined by the caller. For example, if the goal is to create a GitHub repository, the verification method could simply be to query the API and confirm that the repository exists.

That is the general idea of an MCP blockchain.

### Where the idea comes from

Let me explain where this idea comes from. The goal of an MCP blockchain is not to decentralize the MCP Server. The goal is to give blockchains the ability to interact with AI. Those are two completely different motivations.

The logic behind the idea is actually simple. Bitcoin already has a scripting system, but it is expressed as rigid opcodes. Ethereum did something remarkable: it added a compiler on top of opcodes, allowing developers to express opcodes through programming languages. What makes today's AI remarkable is that it has opened a path from natural language to programming languages. In other words, future blockchains may be able to let natural language interact directly with the state machine, without going through the path of natural language -> programming language -> state machine. Programming languages are clearly a middle layer, and the vision of an MCP blockchain is to remove that middle layer.

Another important boundary is that it is unrealistic to have a blockchain execute purely according to the intent of natural language. Even human beings still need things like written contracts. So code itself will not disappear, and state transitions will not disappear either. At the moment, the technical approach that can enable direct interaction between natural language and a state machine is MCP.

### The next technical trend in blockchain

Lately I have been thinking about the question: what is the next technical trend in blockchain? A few things are already clear:

1. People no longer doubt that cryptocurrency is a means of payment.
2. The path of launching tokens by developing yet another blockchain no longer has any narrative space left.
3. The next technical trend will definitely not be an extension of the Ethereum roadmap.

Directions that have already been proposed within the industry also do not seem to have much chance left. DeAI, ZK, Layer2, DeFi, cross-chain, RWA, GameFi, BaaS, NFT, the metaverse, DAO, DID, and so on have all become tired cliches.

So from the perspective of technical trends, blockchain needs a new primitive-level narrative.