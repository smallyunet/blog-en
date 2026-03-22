---
title: What Is the Fatal Flaw of Ethereum AA Wallets
tags: Blockchain
date: 2025-10-24 00:55:34
draft_date: 2025-10-23 23:28:32
---

So far, the latest AA (Account Abstraction) wallet standard is still implemented on top of EIP-4337.

As for the problems with AA wallets, I will not spend much time on surface-level issues such as cumbersome operations, Bundler centralization, low usability, incomplete ecosystem support, and high smart contract security risk.

In one sentence, what AA wallets are doing is separating these two actions: authorizing the use of account funds and broadcasting the transaction on-chain.

### What AA wallets do

In concrete transaction terms, the function of an AA wallet is this: without an AA wallet, you must send the transaction yourself. With an AA wallet, you can sign only and let someone else submit the transaction on-chain for you.

Why can AA wallets do this? Because an AA wallet is essentially a smart contract. That is why you will find that most of the advantages of AA wallets are actually features that smart contracts already have, such as social recovery and batch operations. The only feature that creates a truly distinct experience is gas fee sponsorship.

So why can AA wallets support gas sponsorship? Because all AA wallet operations are not actual transactions, but UserOperations. An off-chain Bundler program packages these user operations and submits them to the chain in batches through transactions.

### Why Ethereum needs AA wallets

Why does Ethereum need AA wallets? Because Ethereum's consensus-layer protocol requires every transaction to be initiated by an EOA address. That EOA address is the `from` field in the transaction structure, and Ethereum nodes use that `from` address to calculate fees, deduct fees, verify transaction validity, and so on.

Contracts do not have private keys, so they cannot initiate transactions. Under this rule, every on-chain action on Ethereum must ultimately be initiated by some EOA address.

You might feel that this is not quite right. You could deploy a contract and let the contract verify the signature data carried in `data`. The signature data inside `data` does not necessarily need to match the address that sent the transaction.

That is correct. In fact, the development path of AA has been: meta-transactions -> EIP-2771 -> EIP-4337.

All of these schemes are fundamentally solving the same problem: how to separate the authority to use funds from the act of initiating an on-chain transaction.

And the root cause behind this whole chain of complex protocols is Ethereum's rule that transactions must be initiated by an EOA address.

### Why Ethereum has this rule

Why does Ethereum have the rule that transactions must be initiated by an EOA address?

Because Ethereum uses an account-balance model. The protocol must know where to deduct the transaction fee from.

### Bitcoin does not have this problem

Bitcoin's PSBT transaction format can support native multisig. Multiple wallets only need to sign, and another wallet can broadcast the transaction in the end.

A multisig transaction is a typical example of separating authorization over funds from the act of broadcasting a transaction.

Why does Bitcoin not have Ethereum's problem here? Because Bitcoin uses the UTXO model. Transactions do not have a `from` address at all. Instead, they have multiple input scripts. Nodes only need to verify whether the transaction satisfies the unlocking rules of those scripts, without worrying about where the fee is deducted from.

### The positioning of AA wallets

Let us sort out the chain of logic: Ethereum uses an account-balance model -> transactions must be initiated by an EOA address -> AA wallets become necessary.

What AA wallets are actually doing is patching Ethereum's account model. AA wallets exist as a workaround created to compensate for the shortcomings of the account-balance model compared with the UTXO model, without requiring changes to the Ethereum protocol itself.

In terms of status, AA wallets occupy a position in Ethereum similar to the position of inscriptions / runes / RGB in Bitcoin. In the Bitcoin ecosystem, because Bitcoin does not have Turing-complete scripting, people created inscriptions / runes / RGB as workaround schemes without changing the Bitcoin protocol itself.

AA wallets require off-chain bundlers to submit transactions. Runes require off-chain indexers to maintain rune state. Is that not fundamentally the same idea, both relying heavily on off-chain programs?

And in reality, we all know that the styles of play in the Bitcoin ecosystem still have not been widely accepted by mainstream society.

If AA wallets are ever broadly accepted by the public in the future, that would mean workaround-based solutions are viable in the blockchain world. That would significantly change the narrative of the entire ecosystem.

### The future of AA wallets

In summary, the conclusion we can draw is that Ethereum can never support a native AA wallet at the protocol layer.

### Summary

The practical guidance these conclusions offer to technical people is:

1. Stop idealizing AA wallets. Do not assume they are advanced or superior technology.
2. You can learn, use, and study AA wallets, but do not truly believe in the technical philosophy behind them.