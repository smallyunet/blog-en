---
layout: post
title: "How Hard Is Ethereum to Use?"
date: 2026-09-23 21:51:00
collection: ai
tags: AI Article Library
lang: en
---

Many people say that Ethereum user experience is not good.

This is too kind a statement.

The really ridiculous thing about Ethereum is not that "there are a lot of steps", but rather:

**It requires a person who just wants to transfer money, buy coins, and use applications to understand something that only protocol engineers should understand.**

Nonce.

Gas Limit.

Base Fee.

Priority Fee.

Allowance.

Calldata.

EOA.

Contract Account.

Signature.

Typed Data.

MEV.

Slippage.

RPC.

Chain ID.

Bridge.

Finality.

UserOperation.

Bundler.

Paymaster.

You can't even simply say:

> I have ETH.

Because someone may ask next:

> Native ETH or WETH?

This has not been a steep learning curve.

This is a set of infrastructure that spits all its internal implementation details in the user's face, and then tells the user:

**Please study on your own.**

What’s even more outrageous is that after using Crypto for a few years, Crypto users started to think that all this was normal.

So in this article I don’t want to discuss the already bad issue of “Ethereum Gas is expensive”.

We discuss some of the more ridiculous stuff.

---

# 1. One of the most absurd facts about Ethereum: ETH is not even a normal Ethereum Token

Ethereum has one of the most important token standards:

ERC-20.

USDC is ERC-20.

USDT is ERC-20.

DAI is an ERC-20.

UNI is an ERC-20.

Almost the entire Ethereum DeFi world works around ERC-20.

Then:

**Is ETH ERC-20?**

No.

Ethereum's own native currency actually does not conform to Ethereum's later most important Token Interface.

The reason is also very simple:

**ETH was born too early.**

The official documentation itself admits that ETH predates ERC-20 and therefore does not comply with the ERC-20 standard; in order for ETH to be processed by DeFi applications like ordinary ERC-20, people later created WETH.

As a result, Ethereum emerged with a very characteristic picture of the times:

```text
Ethereum native currency: ETH

Ethereum Token Standard: ERC-20

Question:
ETH does not comply with the Ethereum Token standard

Solution:
Write a contract and deposit ETH into it,
Mint another ERC-20 Token representing ETH.

Name:
Wrapped ETH
```

That is to say:

**In order for Ethereum to allow its native currency to enter its own Token ecosystem normally, it needs to first package its native currency into a "fake self".**

This thing has been going on for so long that no one even thinks it’s funny.

You have 10 ETH.

Get into some DeFi protocols:

> Please Wrap first.

So:

```text
ETH
 ↓
WETH
```

Run out:

```text
WETH
 ↓
ETH
```

The most exciting thing is:

You can own WETH worth tens of thousands of dollars,

But if Native ETH happens to be 0,

You still may not be able to send transactions that require gas.

Ethereum officials even specifically reminded:

> When wrapping ETH, remember to leave some Native ETH to pay for gas.

In other words:

**You have ETH.**

But not the ETH that can pay for gas.

If someone who comes into contact with Crypto for the first time feels confused about this kind of thing, it’s not because he doesn’t understand blockchain.

But because it is inherently inexplicable.

---

# 2. Ethereum has a very magical way to "cancel a transaction": send another transaction

What follows is what I consider to be one of the most comedic pieces of design in the history of Ethereum user experience.

Let's say you send a transaction.

Gas is low.

It's stuck in mempool.

You regret it.

You click:

> Cancel Transaction

What does a normal person understand by "cancellation"?

Withdraw the transaction just now.

What is the cancellation of Ethereum?

**Send a new transaction.**

And also:

- Use exactly the same nonce as the original transaction;
- Give higher handling fees;
- Usually send yourself 0 ETH;
- Let new transactions be packaged before old transactions;
- Rely on the rule that "the same nonce can only be executed once" to squeeze out old transactions.

This is not a joke.

MetaMask’s official manual cancellation method for advanced users is:

**Send yourself a 0 ETH transaction, use the same nonce as the old transaction, and increase the gas.**

So in Ethereum:

```text
Cancel
```

The real semantics are actually:

```text
with another, more expensive deal,
Compete with the original transaction for the same serial number,
Try to consume this serial number first.
```

Even "cancellation" is not a real operation in the agreement.

It's a race.

Your old transaction:

> I want to execute.

Your cancellation transaction:

> No, don't execute it, let me execute it first.

Then the user has to pay extra, so the verifier should be given priority:

**"Please don't execute the transaction I just made."**

This is no longer a bad UX.

This is a person manipulating the database transaction log.

---

# 3. The most exciting thing is: if one transaction is stuck, it can block all your subsequent transactions.

Why did the trick just now work?

Because the transaction nonce of Ethereum EOA is strictly increasing.

For example, your account has been executed to:

```text
nonce = 100
```

Next:

```text
101
102
103
104
```

Must be in order.

The official documentation defines nonce as a counter that is sequentially incremented when an account sends a transaction. The same nonce cannot be executed twice.

This means a particularly ridiculous user experience:

You sent nonce 101.

Gas is too low.

101 stuck.

Then you send:

102.

103.

104.

Most likely all waiting there together.

Because:

**101 is not over yet.**

MetaMask's official troubleshooting document even requires users to start processing from the oldest pending nonce, and clearly states that nonce 10 cannot be canceled first and nonce 9 left.

Imagine a banking system:

You sent a transfer of 10 yuan this morning.

It's stuck.

So PM all your other payments:

coffee.

Take a taxi.

Lunch.

Online shopping.

All are prohibited.

The bank tells you:

> Because Payment Order No. 71 has not yet been executed.

> Please deal with No. 71 first.

You will think this bank is crazy.

In Ethereum:

This is called **nonce management**.

There are even developers who specifically write:

```text
Nonce Manager
```

to manage this matter.

A person who just wants to transfer money finally needs to understand the ordered sequence number in a distributed system.

Then everyone said:

> Web3 requires user education.

Educate a ghost.

**This is because the internal details of the database come out and require manual maintenance by the user.**

---

# 4. Ethereum even implements "accelerated transactions" into "resending transactions"

Same thing with "Speed Up."

You see this in your wallet:

> Speed Up

It looks like the original transaction was modified.

What do you usually do?

**Construct another replacement transaction with the same nonce but with a higher bid.**

MetaMask clearly states that the acceleration operation will resubmit the transaction and reuse the nonce of the original transaction, increasing the Gas to make it more likely to be packaged first.

Ethereum has even submitted EIP-2831 specifically to standardize this kind of transaction replacement notification, because the replacement mechanism will cause tracking problems for dapp developers.

So the two buttons in the Ethereum wallet:

```text
Speed Up
Cancel
```

The real implementations behind them are:

```text
With the same nonce, send another identical transaction that is more expensive.
```

and:

```text
Same as nonce, send another more expensive empty transaction
```

If a normal user had to know this, he would feel like the entire system was written in the 1990s.

We just cover it up with beautiful UI.

---

# 5. Ethereum has also invented a consumer experience: if you don’t buy something, the money will still be withheld.

Suppose you go to a convenience store to buy something.

Payment failed.

Didn't get anything.

The cashier said:

> The transaction failed.

You:

> OK.

Cashier:

> But we still deducted 20 yuan from you.

You:

> Why?

Cashier:

> Because we just tried to process your payment, we incurred computational costs.

This is Ethereum.

Smart Contract is executed halfway:

```text
REVERT
```

Status changes are rolled back.

Tokens are not exchanged.

The NFT was not purchased.

The loan was not lent out.

But the gas consumed:

**I won’t refund you.**

The official Gas documentation clearly states: If Gas is exhausted during transaction execution, the status modification will be rolled back, but the Gas used to perform the work will still be consumed;`REVERT` Although all remaining Gas will not be burned like the early failure mechanism, the cost of calculations that have been executed still exists.

Of course it makes sense in terms of engineering.

The CPU has figured it out.

The node has been executed.

So collect money.

The question is:

**Why should users bear the cost of application execution path prediction failure?**

The server in Web2 has a bug:

The company pays the server fee.

Ethereum: 

There is a bug in the smart contract, or the state changes causing the transaction to revert:

**Users pay server fees.**

This is a particularly great business model for Crypto:

Program execution failed,

The bill is sent to the person who called the program.

---

# 6. Ethereum’s handling fee is not a “handling fee”, but a real-time computing resource bidding

Let’s look at Gas again.

Average users want to see:

```text
Handling fee: $2
```

What the bottom layer of Ethereum gives him is:

```text
gasLimit
maxFeePerGas
maxPriorityFeePerGas
baseFeePerGas
```

After EIP-1559, a Type-2 transaction even explicitly contains:

```text
max_priority_fee_per_gas
max_fee_per_gas
```

The Base Fee changes dynamically based on block utilization.

Translated into adult language:

You are not paying a processing fee.

You are saying:

> What is the most I am willing to pay for a unit of computing;

> The maximum amount of money one is willing to give as a tip to the blocker;

> The protocol will also generate a dynamic base price based on the current congestion level;

> If the price is right, please help me execute this procedure.

A user simply wants to:

> Swap 100 USDC.

It turns out that the bottom layer of the wallet is actually helping him participate:

**Real-time auction of globally distributed computing resources.**

Then everyone praised:

> The wallet will now automatically estimate Gas, so users don’t need to understand it.

Very good.

The sentence itself is like saying:

> The engine start of our car requires manual adjustment of the fuel injection ratio, but now the instrument panel has automatically adjusted it for the user, so the experience is not bad.

Really normal UX is:

**This knob should never exist in the user's cognitive world in the first place.**

---

# 7. Approve is an anti-human permission model

Ethereum DeFi Another classic design:

```solidity
approve(spender, amount)
```

I want to Swap 100 USDC.

Semantics for normal people:

> Spend my 100 USDC.

The classic semantics of ERC-20 are:

> I authorize this address to actively withdraw up to N USDC from my account in the future.

These two concepts are not the same thing at all.

In the early years, in order to avoid having to Approve again every time they were used, a large number of applications simply allowed users to:

```text
Approve Unlimited
```

So:

I just wanted to buy something once.

The final authorization is:

> This smart contract can continue to move my Token in the future.

Even more exciting is that the ERC-20 standard itself is about `approve()` The description left a historical scar:

When modifying non-zero allowance, the client should consider **First set allowance to 0, then set the new value**, to reduce known attack vectors; but the standard cannot directly force this because it must be compatible with old contracts that have been deployed.

Read this paragraph carefully.

This is almost a microcosm of the user version of the previous article "How Dirty Is Ethereum Technology":

```text
There is something wrong with the permissions model.

↓

Old standards cannot be modified.

↓

Let the client UI help avoid this.

↓

Because it has to be compatible with previous contracts.
```

Who will bear this historical debt in the end?

User.

So you will see some Tokens modify the allowance:

First:

```text
Approve 0
```

Again:

```text
Approve 100
```

A normal "modification of the authorization limit" may turn into two on-chain status modifications.

This is not financial innovation.

**This is like operating a production environment where the database migration has never been completed.**

---

# 8. One of the most ridiculous security suggestions: Don’t trust the Token’s name, check the 40-digit hexadecimal address

Ordinary financial systems identify an asset:

```text
USD
```

Crypto: 

```text
USDC
```

Very good.

Then discover that anyone can deploy a Token:

```text
name = USD Coin
symbol = USDC
logo = looks the same
```

What to do?

Old Crypto users will tell newcomers very seriously:

> Don't look at the name.

> Be sure to confirm the Contract Address.

So to identify a kind of "dollar", you ultimately need to identify:

```text
0xA0b86991c6218b36c1d19d4a2e9eb0ce3606eb48
```

This kind of string.

We even include:

> Will check Contract Address

As a kind of Crypto literacy.

No.

This is not user literacy.

This is **The naming system basically has no survival skills that will be generated later.**

Normal system faces:

```text
Microsoft.com
```

Does not tell users:

> Domain names can be easily spoofed, so remember the server's IPv6 address for security reasons.

This is pretty much what Web3 does.

---

# 9. Ethereum’s “signature” may be the most dangerous confirmation button on the entire consumer internet

Common products:

```text
Confirm
```

Usually means:

Confirm the matter in front of you.

Ethereum wallet:

```text
Sign
```

May mean:

Prove you control this address.

Probably login.

Probably an order.

Probably Permit.

Maybe authorization.

Probably Typed Data.

It may eventually allow another protocol to have some kind of asset operation rights.

The common actions seen by users are:

**Sign.**

What’s really outrageous is:

Ethereum's security model is based on very reliable cryptographic signatures.

But user security is often based on:

> I hope users can understand what they are signing.

Cryptography can prove:

**It was indeed you who signed it.**

Cryptography cannot prove:

**Do you know what you signed?**

Between these two things lies the entire Crypto Scam industry.

---

# 10. Then EIP-7702 pushed this matter to a new level: once authorized, EOA can start executing other people's code

Ethereum found EOA too weak.

What to do?

EIP-7702.

Now EOA can delegate the account execution logic to a piece of contract code through authorization.

This allows for very powerful wallet capabilities indeed.

But EIP-7702’s own security chapter said something extremely interesting:

**Applications should not directly give users an interface to allow users to sign delegation authorization at will.**

Why?

Because authorized code can have great control over the account, ordinary users do not have the ability to audit what code they have been given by the delegate.

The original EIP even clearly states:

> There is no secure universal interface that simply solves this problem; few users have the expertise to properly scrutinize delegation code.

Please appreciate this technological evolution process:

First generation:

```text
The private key controls the account.
```

Everyone thinks the function is too weak.

So the next generation:

```text
Users can sign,
Let your account execute the code of another address.
```

The protocol designer must then alert the wallet:

**Never let users sign this casually.**

This is already very Ethereum.

Add an ability.

Then add a whole set of security infrastructure,

Prevent users from actually using this ability directly.

---

# 11. Public mempool: You even have to tell the world publicly how you plan to trade first, and then pray that no one makes money from you.

This is where I think the Ethereum user experience is the most “cyberpunk”.

You want to buy a Token on a DEX.

So the transaction is broadcast.

The transaction has not been uploaded to the chain yet.

But searchers can see:

> This person wants to buy it immediately.

This can then be sorted around your transactions.

For example the most notorious:

**Sandwich Attack.**

Attacker:

Buy before you do.

Your trade drives the price higher.

Then sell it behind you.

Where does the profit come from?

Because you get a worse closing price.

Ethereum’s official MEV documentation is very direct in admitting that sandwich trading will cause users to suffer larger slippage and worse transaction execution.

That is to say:

When a user is ready to trade on Ethereum, the default model has long been approximately:

```text
First broadcast your trading intentions to the world

↓

Tell everyone:
what do i want to buy
How much to buy
How much slippage are you willing to accept?

↓

wait for others to decide
Is there a way to make money by plugging things in before and after your trades?
```

What does this look like?

You walk into the stock exchange and shout:

> I want to buy this stock at market price for $5 million!

Then all high-frequency traders hear it first.

After the transaction, you ask again:

> Why is the transaction price so bad?

Others answered:

> This is MEV.

What does an ordinary person need to know?

```text
Private RPC
MEV Protection
Slippage
Sandwich
Builder
Searcher
```

Only in this way can an exchange be completed more safely.

We actually call this system:

**Permissionless Finance.**

Permissionless indeed.

Even the people who steal your slippage are permissionless.

---

# 12. Slippage itself is a very ridiculous user input item

DEX also has a magical setting:

```text
Slippage Tolerance
0.5%
1%
5%
```

What ordinary users want to express is:

> Help me buy ETH with 1000 USDC.

Ethereum DeFi asked him to answer:

> When the trade is actually executed, if the market state changes, how much worse than the current Quote would you allow the final result to be?

This essentially lets the user configure it themselves:

**Transaction execution protection parameters.**

Set too low:

Transaction revert.

Gas may be wasted.

Set too high:

You may encounter poor prices and even expand the profit margin of the sandwich.

What user experience is this?

Users need to:

```text
Transaction failure risk
```

and:

```text
Risk of value being extracted by others
```

Manually adjust a slider between them.

Then the UI tells you:

> 0.5% is recommended.

Very modern.

---

# 13. The most Ethereum product logic: the system creates problems first, and then lets the wallet invent a "smart mode" to cover it up

Nonce is difficult to use?

Wallet Plus:

```text
Speed Up
Cancel
```

Gas is difficult to understand?

Wallets are automatically estimated.

Have a problem with MEV?

Add:

```text
MEV Protection
```

Approve Danger?

Wallet does:

```text
Allowance Warning
```

Can't read the signature?

The wallet starts a simulated transaction.

Too many L2s?

Wallet hidden network.

Account too weak?

4337.

Gas Token trouble?

Paymaster.

EOA too weak?

7702.

You will find an amazing rule of Ethereum:

**Every time the bottom layer leaves an abstraction that is not suitable for ordinary people, the upper layer needs to grow a whole set of products to hide this abstraction.**

We then call the new infrastructure resulting from hidden complexity:

**Innovation.**

---

# 14. ERC-4337 is more like evidence: Ethereum’s original transaction model is no longer suitable for modern users.

Let’s take a look at what 4337 is solving:

Gas Sponsorship.

Batch Calls.

Smart Account.

Custom authentication.

Account recovery.

ERC-20 pays for Gas.

Sounds good.

But these needs actually prove one thing:

**Raw Ethereum Transactions are simply not enough anymore.**

So 4337 did something extremely Ethereum.

Don't change the bottom layer.

On the underlying trading system:

**Re-implement a transaction-like system.**

It even deliberately doesn't call its object Transaction.

And call:

```text
UserOperation
```

Then add:

```text
UserOperation
Bundler
EntryPoint
Paymaster
Factory
Aggregator
UserOp Mempool
```

The official specification of ERC-4337 clearly states that it uses an additional mempool,`UserOperation`, Bundler and `EntryPoint`, avoid modifying the Ethereum consensus layer.

It's like the process model of an operating system is really difficult to use.

But the ABI cannot be changed.

What to do?

Recreate it in user mode:

**Bare operating system.**

And then finally achieved:

> Ordinary users don't need to understand Gas.

How touching.

In order for users to finally not have to understand Ethereum,

Ethereum first implements another layer of Ethereum on top of Ethereum.

---

# 15. Even 4337’s “users don’t need to worry about Gas” requires an entire shadow financial system behind it.

Users see:

> Gas Sponsored

Very simple.

What's behind the scenes?

Paymaster.

Paymaster needs to deposit ETH to EntryPoint first.

Bundler needs to determine whether the Paymaster is willing to pay.

UserOperation also needs to be mocked.

Validation logic needs to be checked.

Paymaster may cause DoS.

Bundler even needs to impose reputation/staking constraints on certain entities.

That is to say, in order to implement an ordinary function in the Web2 world:

> Merchants help users pay operating costs.

What Ethereum creates is:

```text
User
 ↓
Smart Account
 ↓
UserOperation
 ↓
Bundler
 ↓
Paymaster Validation
 ↓
EntryPoint
 ↓
Ethereum Transaction
 ↓
Block
```

User last saw:

```text
Fee: $0
```

Then everyone said:

> See, Web3 UX is already the same as Web2.

Yes.

**Just bury seven layers of infrastructure beneath the button.**

---

# 16. The most absurd thing about Layer 2 is not the “liquidity split”, but that Ethereum even makes the concept of “balance” no longer complete.

L2 liquidity fragmentation has been talked about badly.

What’s really worth complaining about is:

**The Ethereum ecosystem turns “how much money do I have” from a scalar into a vector.**

Previously:

```text
balance = 10,000 USDC
```

Now it's actually closer:

```text
balance = {
  Ethereum: 2000,
  Base: 3500,
  Arbitrum: 4000,
  Optimism: 500
}
```

And that doesn’t even include:

native USDC.

bridged USDC.

Representation of other bridges.

So the wallet shows:

> Total Balance: $10,000

This is actually a UI illusion.

Because of this $10,000:

**Availability is not always the same at all times.**

The total assets are the same,

But the state space is different.

Can you use it right now?

Depends on:

Which Rollup.

Which Token Contract.

Is there any Gas?

Where the application is deployed.

Is there any bridge liquidity?

This is L2’s biggest UX failure.

**Ethereum turns "balance", a concept that humans have understood very clearly for thousands of years, into a distributed system problem.**

---

# 17. Smart accounts even make “what is my address” complicated again?

EOA has great benefits:

The same private key can get the same address in a large number of EVM networks.

Then we started embracing Smart Account.

The problem arises:

Smart accounts are contracts.

The contract address depends on the deployment mechanism, factory, salt, init code and other conditions.

In order for the same smart account to have a consistent address in different chains, the ecosystem even needs to rely specifically on `CREATE2`, deterministic deployment factory, counterfactual deployment and other mechanisms.

The motivation of EIP-7955 even states directly:

**Letting contracts have the same address and code in multiple chains is a difficult problem in itself.**

So we took a very strange route:

```text
EOA: 
The function is too simple,
But the address is easy to understand.

↓

Smart Account: 
The functionality is finally enriched.

↓

Wait,
Now I have to figure out how to open my account
It is still "the same account" in different chains.
```

Many advances in Ethereum have this flavor:

**Solve an old problem and create a new, more advanced problem.**

---

# 18. The ultimate absurdity of Ethereum: the most advanced cryptography system, but in the end it relies on the wallet to guess what the user wants to do.

Today’s wallets are increasingly emphasizing:

Transaction Simulation.

Why?

Because users simply can’t understand:

```text
to
value
data
```

I also don’t understand what state changes will ultimately result from the contract call.

So the wallet starts before actually sending:

**Run the simulation first.**

Then tell the user:

> Expect to lose 100 USDC.

> You can expect to get 0.03 ETH.

This is certainly a huge UX improvement.

But it is also an extremely ironic fact:

Ethereum’s original transaction format provides information to users,

**It is no longer enough for an ordinary person to know what will happen after he signs.**

So the wallet needs to run EVM.

Internal calls need to be traced.

Need to parse Token Transfer.

Need to identify malicious contracts.

Then translate all this into human language.

We ended up with an amazing architecture:

```text
people
 ↓
wallet
 ↓
trading simulator
 ↓
RPC
 ↓
EVM
 ↓
smart contract
 ↓
Back to the wallet
 ↓
Tell humans:
"This is probably what you wanted to do just now."
```

Behind a confirmation button,

Running half a blockchain analysis system.

---

# 19. The biggest problem with Ethereum is no longer "not simple enough", but that it has almost no ability to become simple again.

If Ethereum were a startup internal system:

Refactor.

Delete EOA.

Unified account model.

Change the Token standard.

Redesign Gas.

Deprecated Legacy Transaction.

Redesign permissions.

Even redesign the execution model.

But Ethereum can't do that.

The reason is precisely that it succeeded.

Hundreds of billions of dollars in assets.

More than ten years of history.

Countless contracts.

Countless wallets.

Countless assumptions.

All must continue to work.

So:

ETH is not ERC-20?

Can't do it again.

Pack a WETH.

EOA too weak?

Cannot be deleted.

Add 4337.

4337 Not native enough?

Add 7702 more.

The account has no gas?

Add Paymaster.

Public mempool is MEV?

Engage in Private Orderflow.

Bad experience with Nonce?

The wallet does transaction replacement.

Approve Danger?

The wallet does reminders, simulations, and revokes.

Too much L2 too broken?

Engage in Chain Abstraction.

You will find:

**Ethereum has rarely truly eliminated complexity.**

What it is better at is:

**Wrap up the old complexity and add another layer of abstraction.**

---

# 20. So the really scary thing about Ethereum is not that it’s difficult to use

"Ethereum is difficult to use" is actually speaking for it.

Because "difficult to use" sounds like:

The UI still needs to be optimized.

The buttons also need adjusting.

The novice tutorial still needs to be improved.

No.

A more accurate statement would be:

**Ethereum massively outsources the complexity that a distributed computer should bear on its own to wallets, application developers, and ultimately, inevitably, users.**

So an ordinary person just thinks:

> Take USDC and buy ETH.

There may be something behind it:

```text
Is your ETH WETH?

Which chain are you on?

Do you have Native ETH?

Is Allowance enough?

Do you want Approve?

Approve How much?

How much gas?

How much is the base fee?

How much is the Priority Fee?

What is the Nonce?

Is there any Pending Transaction in front?

How much does Slippage set?

Will it be Sandwiched?

Will the transaction be Revert?

Are wallet simulation results trustworthy?

Go Public RPC or Protected RPC?

Is this a Transaction or a UserOperation?

Who is Bundler?

Is there a Paymaster?
```

Then the industry tells you:

> Mass Adoption is coming.

---

# Conclusion: A truly successful financial protocol should make users more and more stupid

"Stupid" here is not a derogatory term.

It is precisely the highest state of technical success.

When you swipe your credit card,

Understanding ISO 8583 is not required.

When you use HTTPS,

No need to check TLS handshake.

When you send an email,

No need to know about SMTP relay.

When you turn on your phone,

No need to know the virtual memory page table.

The more mature the technology,

The less the user needs to know.

Ethereum has been doing exactly the opposite for a long time.

The more deeply you use it, the

The more there is to understand.

What is the last so-called "Crypto Native User" actually?

Not an ordinary consumer.

But a trained one:

**Part-time wallet security engineer + Gas trader + RPC operation and maintenance + smart contract auditor + cross-chain liquidator.**

He knows how to change RPC.

Know how to change the nonce.

Know how to replace a transaction.

Know how to revoke allowance.

Know the difference between ETH and WETH.

Know how to read Contract Address.

Know how to avoid a sandwich.

Understand the circumstances under which transaction revert will burn gas.

Know when to use bridge.

Even know what Bundler and Paymaster are.

Then the man turned to the newcomer and said:

> Ethereum is actually not difficult, you will get used to it after using it a few times.

The sentence itself,

Perhaps this is the best epitaph for the failure of Ethereum user experience.

**Not that Ethereum is finally simple.**

**It was you who were finally trained as a protocol engineer.**
