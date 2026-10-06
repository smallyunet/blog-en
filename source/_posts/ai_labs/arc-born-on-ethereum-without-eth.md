---
layout: post
title: "Arc: Born on Ethereum, Without Its Dirtiest Component (ETH)"
date: 2026-09-25 21:23:00
collection: ai
tags: AI Article Library
lang: en
---

In the previous two articles, I discussed two issues about Ethereum respectively.

One is:

**How dirty is Ethereum’s technical architecture?**

Another one is:

**How Ethereum leaks its complexity to users.**

The historical special logic of The DAO.

More than a decade of Hard Fork deposition.

EOA.

WETH.

Approve.

Nonce.

Gas Token.

ERC-4337.

Bundler.

Paymaster.

EIP-7702.

Blob.

Rollup.

Bridge.

MEV.

All of this can be explained individually.

But when stacked together, they form an increasingly larger system that is increasingly difficult to escape from history.

So here is a very interesting question:

**If we were to rebuild a chain today, could we only use what Ethereum has proven to be useful, instead of Ethereum itself?**

Circle’s Arc gives an interesting answer.

My judgment is:

**The most noteworthy thing about Arc is not how many new technologies it invented.**

Quite the opposite.

What’s really smart about it is that it knows what things don’t necessarily need to be reinvented.

EVM?

Bring it.

Solidity?

Bring it.

Ethereum development toolchain?

Bring it.

Mature wallet system?

Bring it.

The development paradigm accumulated by the smart contract ecosystem?

Bring them all.

However:

ETH?

Don't.

Ethereum consensus?

Don't.

Economic security of Ethereum?

Don't.

Rollup route for Ethereum?

No inheritance is required.

The historical politics of Ethereum after more than ten years?

It has nothing to do with me.

Ethereum’s economic model where “you must have a volatile native token to use the blockchain”?

Just throw it away.

This is where Arc gets really interesting.

**It was born in the technical world of Ethereum, but does not need to live in the economic world of Ethereum.**

---

# 1. Arc’s cleanest move: There is no need to create an “ARC Coin” for this chain.

Almost all public blockchains have a very deep-rooted logic:

I want to build a chain.

So I want to send a coin.

Ethereum has ETH.

Solana has SOL.

Avalanche has AVAX.

BNB Chain has BNB.

Then this coin bears:

Gas.

Pledge.

Security budget.

Ecological incentives.

Governance.

Speculation.

Asset pricing.

So a person who originally just wanted to use the blockchain was forced to participate in an asset speculation first.

You just want to transfer $1000.

The system first asked:

> Have you bought our Coin?

This is one of the most common and strange things that has happened in Crypto for more than ten years.

Arc just cuts this layer off.

**Arc’s Gas uses USDC.**

Not:

```text
ARC
```

Not:

```text
ARC Gas Token
```

Not:

```text
First buy some Arc Coin on the exchange,
Then transfer it to your wallet to pay the handling fee.
```

That is:

```text
USDC
```

Circle describes Arc’s design very clearly:

> Gas in dollars.

Transaction fees are paid directly by USDC, eliminating the need to hold volatile native tokens.

On the surface this thing is just:

**Changed Gas Token.**

It's actually far more important than that.

Because it separates two concepts that have long been tied together in the blockchain world very cleanly for the first time:

```text
use network
≠
Investment Network Token
```

This is a very important change in thinking.

---

# 2. On Ethereum, "using Ethereum" and "holding ETH" have never been truly separated.

This is also where I always think Ethereum is extremely unclean.

Suppose a company says:

> I just want to settle USD.

On Ethereum it still has to face a completely unrelated problem:

> How much is ETH today?

Because in the end Gas is ETH.

So a strange asset will appear in the corporate financial system:

```text
USDC
```

This is the money I want to use.

and:

```text
ETH
```

This is how I use the money,
Extra money that had to be held.

Then the finance department has to answer:

How much ETH should I keep?

What should I do if the price drops?

What is the dollar cost of gas after the price increase?

When should I replenish gas?

How much ETH are needed for each address?

Is Treasury allowed to hold volatile crypto assets?

How to record this asset?

This is not a business requirement.

This is a requirement that the protocol imposes on the business.

In fact, when Circle originally released Arc, it directly wrote out the company’s feedback:

Businesses told Circle:

> “Our Treasury team cannot hold volatile Crypto Assets in order to pay for gas.”

So the answer given by Arc is surprisingly simple:

**Then don't hold it.**

This is called real problem solving.

Not:

Design a Paymaster.

Not:

Design Gas Station.

Not:

Account abstraction on Ethereum.

Not:

The background helps users Swap ETH.

Not:

Sponsor Gas.

Instead:

**Gas is originally dollars.**

Cut away the entire problem in one fell swoop.

---

# 3. This is the difference between "clean architecture" and "patch architecture"

How does Ethereum solve the problem of users not wanting to hold ETH to pay Gas?

The answer is increasingly complex.

ERC-4337.

Paymaster.

Smart Account.

Bundler.

EntryPoint.

Push native account abstraction even further.

All this technology is certainly very clever.

But looking back from the product issues, you will find an absurdity:

The user’s problem is actually just:

> I obviously have USDC, why can’t I use USDC to pay transaction fees?

The answer given by Ethereum gradually became:

```text
Smart Account
    ↓
UserOperation
    ↓
Bundler
    ↓
Paymaster
    ↓
EntryPoint
    ↓
ETH
```

Arc: 

```text
USDC
 ↓
Gas
```

It's over.

This is what I call:

**Clean.**

When advanced technology is most impressive, it is not when it creates five more layers of abstraction.

Instead, it was discovered that four of the layers did not need to exist at all.

---

# 4. The smartest thing about Arc: it uses EVM but does not turn itself into Ethereum

It is important to distinguish two concepts that are often conflated in the crypto community:

```text
EVM
```

and:

```text
Ethereum
```

They are not the same thing.

EVM is the execution environment.

Ethereum is a complete set of:

- consensus;
- network;
- historical status;
- ETH; 
- validator economy; 
- fee market; 
- community governance;
- upgrade route;
- Historically compatible;

Arc is EVM-compatible.

So Solidity developers can continue to use the tools and development paradigms they are familiar with. Circle has clearly emphasized Arc’s EVM compatibility from the beginning.

But Arc is my own **Layer 1**.

It is not:

```text
Ethereum L2
```

Nor:

```text
Ethereum Sidechain
```

Not even:

```text
Submit the transaction to Ethereum last for security.
```

It has its own validator set and its own consensus.

The official mainnet version in 2026 will use permissioned validator set and provide deterministic and sub-second finality. Circle positions itself as an independent L1 for financial markets.

So more accurately:

> **Arc uses the virtual machine standard invented and popularized by Ethereum, but does not purchase Ethereum's security.**

This matter is very critical.

---

# 5. This may be EVM’s ultimate biggest victory: Ethereum can lose, but EVM can still win.

Many people naturally think:

EVM ecosystem grows,

This means that the value of Ethereum increases.

I increasingly don't think these two things are bound to each other.

Look at Arc.

Developers can continue:

```text
Solidity
Foundry
Hardhat
Viem
MetaMask
EVM ABI
0x Address
ERC-20
```

But the entire economic system can be:

```text
USDC
    ↓
Arc
```

There is no need to go through:

```text
ETH
```

This means a very interesting possibility:

**The EVM may one day be as successful as Linux, but Ethereum itself does not monopolize all economic value.**

The Linux kernel is very successful.

It does not mean that all revenue from software built on Linux will go to a "Linux Token".

TCP/IP was extremely successful.

There is no TCP Token that continues to appreciate in value due to increased Internet traffic.

HTML becomes a world standard.

There is also no HTML Coin taxed every time a web page is opened.

So we have to start distinguishing:

> Ethereum created the EVM.

and:

> All economic activities using EVM should assign a value to ETH.

Totally two different things.

Arc is one of the most beautiful examples of this logic.

---

# 6. Arc can make extensive use of Ethereum’s legacy while preventing ETH from capturing these values.

Assume that Arc appears in the future:

$100 billion stablecoin settlement.

Tokenized Treasury.

foreign exchange.

securities.

RWA.

AI Agent Payment.

Corporate Treasury.

On-chain Lending.

DEX.

These applications may still use:

Solidity.

ERC-20.

ERC-721.

ERC-4626.

EVM.

Even a lot of the infrastructure invented in the Ethereum era.

But these transactions:

**No ETH required.**

There is no need to buy ETH to pay for gas.

No Ethereum validator is required.

No Ethereum blob required.

No Ethereum settlement is required.

There’s no need to wait for Ethereum Finality either.

This creates a state that was rarely seen before:

> **Ethereum’s technical standards are preserved, but ETH’s value capture is cut off.**

From an ETH investor perspective, this is not necessarily a good thing.

From the perspective of the entire industry, I think it is very healthy.

Because:

**Technical standards should be able to exist independently of the assets that create them.**

Otherwise it is not a standard.

It's just a walled garden.

---

# 7. Arc’s value scale is not even “Crypto”, but US dollars

This is one of the most fundamental differences between Arc and traditional public blockchains.

Ethereum’s economic scale is ultimately:

```text
ETH
```

Gas uses ETH.

Validator receives ETH.

Security budget is measured in ETH.

The network economy is ultimately built on the asset ETH.

Arc turns the lowest unit of the entire system into:

```text
USDC
```

The target unit of USDC is:

```text
1 USDC ≈ 1 USD
```

Circle says USDC is backed 1:1 by cash and highly liquid cash equivalents and is designed to be redeemable at one dollar.

So Arc did something very bold:

**Directly move real-world currency units into the bottom layer of the blockchain.**

Not:

```text
Gas = 0.000034 ARC
```

Then the user opens CoinGecko:

> Right now the ARC is $17.42, so how much is it?

Instead:

```text
Gas ≈ $0.00x
```

This makes sense for Consumer UX.

But it means more to the business.

Because the company:

Revenue.

Cost.

Treasury.

Accounting.

Budget.

Risk.

All are:

**Fiat-denominated.**

Blockchain finally stops requiring companies to enter a parallel currency system first.

---

# 8. I even think that “no speculative token of its own” is one of Arc’s most powerful product designs

The Crypto industry has developed a very strange reflex:

A new chain is released.

Everyone’s first words:

> What is Token?

What about Tokenomics?

How much FDV?

How much is the airdrop?

When is TGE?

How much is the Validator APR?

What does Unlock Schedule look like?

The most interesting thing about Arc is exactly this:

**The entire discussion could have been non-existent.**

What the network needs is:

Money.

It uses USDC directly.

So users don’t need to think simultaneously:

```text
Is this chain good?
```

and:

```text
Will the currency of this chain fall by 80%?
```

The two issues were finally separated.

It's extremely clean.

---

# 9. One of the biggest problems of Ethereum is that it always mixes "network usage value" and "Token investment value"

There has been a very strange discussion in the Ethereum community for a long time:

Internet usage increases,

Should ETH rise?

Blob charges increased,

Is ETH deflationary?

Is L2 sucking away ETH value?

Is Burn Enough?

How about Validator Yield?

Ultrasound Money?

These things can be discussed from both a technical perspective and an investment perspective.

But for a real payment system:

**Why should users care about this?**

Visa users don’t research:

> Every time a payment is completed on the Visa network, how much V stock is destroyed?

Bank customers don’t research:

> What is the recent inflation rate of SWIFT’s settlement token?

Enterprises only hope:

```text
Send $1,000,000
```

Then:

```text
The other party received $1,000,000
```

The cost is clear.

Time is clear.

Finality is clear.

Arc has been closer to this idea since day one.

---

# 10. The Arc never had to pretend to be an anarchist experiment

This is also where I think Arc is more honest than many traditional Crypto projects.

A very important part of Ethereum’s historical narrative is:

Decentralization.

Censorship Resistance.

Permissionless Validation.

Trustlessness.

These certainly have value.

But the Crypto industry has a very serious problem:

**Consider "more decentralization" as the only correct optimization direction for all scenarios by default.**

Actually not.

Bank liquidation is not.

The stock market is not.

Corporate Treasury is not.

Cross-border payments don’t necessarily have to be either.

What these institutions care more about are usually:

Who is responsible?

Who regulates?

Finality OK?

Who to call if something goes wrong?

Who are the participants?

Where are the boundaries of responsibility?

Does the system meet regulatory requirements?

Arc explicitly selects permissioned validators.

Circle even uses this as a selling point to banks:

Known, screened validators.

Clear governance responsibilities.

Finality.

Circle believes this helps banks with risk management and aligns with Basel and financial market infrastructure principles.

Crypto fundamentalists may see:

```text
Permissioned Validator
```

Then immediately said:

> Not decentralized enough.

My question is:

**So what?**

---

# 11. Centralization never automatically means bad things

Coinbase is a great example.

Coinbase is a company.

Someone manages it.

Regulated.

Government orders can be received.

Laws need to be followed.

But this does not mean:

Coinbase has no value.

Quite the opposite.

For a large number of ordinary users:

"Someone is in charge"

Even advantages.

Because the real world financial system is originally built on:

```text
Responsibility
Accountability
Jurisdiction
Regulation
```

above.

Decentralization solves a very important problem:

> How to reduce the need to trust a subject?

But it's not free.

The price it pays is:

Governance is slower.

Agreements are more difficult to amend.

Historical compatibility is becoming more and more serious.

Upgrading requires the coordination of a large number of independent actors.

Complexity is hard to force clean.

Once a faulty design is widely deployed, it is almost impossible to roll it back.

Arc has a huge advantage that Ethereum does not have:

**It can make decisions.**

---

# 12. “Being able to make decisions” is actually an engineering ability that is seriously underestimated by Crypto.

What if Ethereum said today:

> We have deleted all old EOAs.

Impossible.

> ERC-20 redesign.

Impossible.

> All historical Fork code is deleted.

Impossible.

> Change account structure.

Extremely difficult.

> Forced migration of all users.

Even more impossible.

Because Ethereum is a public protocol that has been running for more than ten years.

No CEO can push buttons:

```text
Migrate
```

Arc is different.

At least at this stage of development, it's a very clean sheet of paper.

What's wrong with the agreement?

Change.

Gas model unreasonable?

Change.

Do financial institutions need new privacy mechanisms?

do.

Validator structure needs to be adjusted?

Governance.

This of course means more trust.

But it also means:

**It will not naturally inherit Ethereum’s “no history can be deleted” curse.**

The younger a protocol is, the greater this advantage is.

---

# 13. Why I say Arc is “taking EVM from the corpse of Ethereum”

This sentence may be difficult to hear.

But that's how technology always evolves.

The smartest thing about the new system is not to reject everything from the previous generation.

Instead:

**Inherit the standard, discard the implementation.**

Unix leaves POSIX.

The browser leaves JavaScript behind.

SQL spans decades of databases.

x86 software even spans completely different generations of CPUs.

Arc’s smartest attitude towards Ethereum is also:

Ethereum spent ten years doing the most difficult thing for the entire industry:

Build:

```text
EVM
Solidity
ABI
ERC
Wallet
Tooling
Developer Mindshare
```

Why don't you want these things?

Of course.

However:

```text
ETH monetary system
PoS economics
historical state
old forks
Ethereum governance
Rollup roadmap
L1 fee history
```

Why must they be inherited together?

**Totally unnecessary.**

So I think more and more:

Arc is not an Ethereum Killer.

This word is too vulgar.

It's more like:

**Ethereum Unbundling.**

Open Ethereum.

Leave valuable components behind.

Get rid of unnecessary components.

---

# 14. This is where Arc is more interesting than Base

Base was successful.

And it's very easy to use.

But Base still belongs to the Ethereum world from a technical and economic perspective.

It is an Ethereum L2.

The final settlement and its long-term security architecture are structurally tied to Ethereum.

Arc is different.

Arc is L1.

It is not:

> A better usable entry to Ethereum.

Instead:

> **An independent network using Ethereum development standards but with its own financial system.**

The difference is very big.

The development of Base can continue to expand Ethereum’s Rollup territory to a certain extent.

The development of Arc proves another thing:

**The ecological expansion of EVM is not equal to the expansion of Ethereum itself.**

In a sense, this is a more dangerous route to the ETH Value Capture Thesis.

---

# 15. Arc’s real moat is not even the blockchain, but the money Circle already owns

This is another place that many “new public blockchains” cannot copy.

Ordinary public blockchains face a chicken-and-egg problem when they are launched:

No one uses it.

So there is no liquidity.

There is no liquidity.

So no one deploys the application.

No one deploys the application.

So there are fewer users.

In the end we can only:

Send Token.

Mining.

subsidies.

Airdrop.

To TVL.

Use money to buy ecology.

Arc started from a completely different place.

It already has:

**USDC.**

As of Arc’s mainnet launch in September 2026, Circle stated that USDC circulation has exceeded **$74 billion**.

USDC was natively deployed on 30 blockchains by the end of 2025; Circle's CCTP covered 18 chains at that time, and processed approximately $31 billion in cross-chain transfers in the third quarter of 2025.

This means Arc doesn't need to be invented from scratch:

"Money."

Circle already has money.

Arc does:

**Use the money to build your own operating system.**

This is a completely different starting point.

---

# 16. Circle used to be a tenant on someone else’s blockchain, but now it has become its own landlord.

This is the easiest way to understand Arc.

Previously:

USDC runs on Ethereum.

Circle brings huge transaction demand to Ethereum.

But users pay ETH Gas.

Ethereum Validator gets fees.

USDC’s adoption growth helps the Ethereum ecosystem.

Then USDC ran to:

Solana.

Base.

Arbitrum.

Avalanche.

Polygon.

Many chains.

Circle is:

**Asset issuer.**

The chain is:

**Infrastructure owners.**

After the advent of Arc, this relationship changed.

For the first time, Circle has:

```text
Money
+
Settlement Layer
+
Developer Infrastructure
+
Interoperability
+
Payments Network
+
FX
```

Circle even directly described its product system as the Internet Financial Platform in 2026, and Arc is the underlying Economic OS.

This is where Arc’s truly huge potential lies.

It is not an additional EVM Chain with higher TPS.

**It is Circle's move from "issuing money on other people's operating systems" to "owning its own operating system."**

---

# 17. Even cross-chain is a completely different problem for Arc

The cross-chain issues in the Ethereum L2 world are:

Assets are broken.

What to do?

So it appears:

Bridge.

Liquidity Provider.

Solver.

Intent.

Canonical Bridge.

Message Passing.

Arc’s route has a natural advantage:

**Circle itself is the issuer of USDC.**

This means that it does not necessarily need to understand "cross-chain USDC" as:

> Lock up a packaging token on chain A, and then cast a mapping token on chain B.

CCTP can do:

```text
Burn on chain A

↓

Circle attestation

↓

Mint native USDC on chain B
```

Circle is also using Gateway to abstract USDC liquidity on multiple chains into a unified balance. Its 2026 data clearly positions Arc as a high-speed settlement environment where cross-chain USDC can converge.

This means:

Many public blockchains need to solve problems through complex financial engineering.

Circle can be used directly in:

**currency issuance layer**

solved.

This is a huge structural advantage.

---

# 18. The endgame of Arc may not be the DeFi Chain at all, but the Internet execution layer of the US dollar

If you just understand Arc as:

“Circle made an EVM Chain.”

I think it's grossly underestimated.

Circle’s own goals are already very clear:

Payments.

FX.

Capital Markets.

Tokenized Assets.

Treasury.

AI Agents.

Machine-to-machine Payments.

When the mainnet was released, Arc was even directly called:

**Economic Operating System for the Internet.**

There is certainly a marketing component to this statement.

But the direction deserves careful attention.

Because the truly huge market is never:

Crypto Degens Swap multiple times.

Instead:

The flow of money that occurs every day between companies around the world.

Securities Clearing.

Cross-border payments.

Money management.

Foreign exchange settlement.

Tokenized Treasury.

AI Agent automates payments.

If these things do get onto the chain, what they need is not necessarily:

> The world's most decentralized smart contract platform.

They may need more:

```text
USD denominated
Finality
Clear governance
Regulatory Compatibility
Privacy
Programmable
Global 24/7
Standardized development tools
```

And that’s exactly what Arc has been targeting from day one.

---

# 19. Arc’s “centralization” may even be its biggest weapon in competing against Ethereum

Many people will think:

Ethereum: 

```text
decentralized
```

Arc: 

```text
permissioned validators
```

So Ethereum Advanced.

Arc is low level.

Instead, I think this judgment is too simplistic.

If the goal is:

**Censorship-resistant sovereign money**

Ethereum’s model clearly has huge advantages.

If the goal is:

**Let global banks settle $1 billion on-chain**

The situation may not be the same.

A bank is more likely to ask:

> Who runs Validator?

> Who is responsible if there is a problem with the network?

> What is the legal significance of Finality?

> Can you be sure that the transaction will not be reorg?

> Does it meet regulatory requirements?

> Can sensitive transaction information be protected?

At this time:

```text
Known Validators
```

Maybe not even a flaw.

Instead:

**Feature.**

Circle is clearly leveraging this structure to court the banking and institutional scene, rather than trying to pretend that Arc does the exact same thing as Ethereum.

---

# 20. Of course, Arc’s “cleanliness” is not free.

The other side must be made clear here.

Otherwise, the article will be written as an advertisement.

Arc has no historical debt to Ethereum.

But it carries its own concentration risks.

Arc uses permissioned validator set.

It's highly tied to Circle's infrastructure.

USDC’s credit comes from Circle’s reserves, custody, and U.S. dollar banking system.

So Arc is not:

**Trustless Ethereum, but better.**

Not at all.

What it does is another trade-off:

Ethereum: 

```text
Greater decentralization
Stronger censorship resistance
more difficult to change
More historical debt
complex economic system
```

Arc: 

```text
Clearer control boundaries
Stronger institutional attributes
easier to govern
Easier to do product optimization
But relies more on Circle,
validator governance
and the dollar system
```

So the real question is not:

Who is absolutely right?

Instead:

**What kind of thing does the financial world need?**

My judgment is:

Both will exist.

But in the past, the Crypto industry has seriously overestimated the first demand,

At the same time, the second demand is seriously underestimated.

---

# 21. The scariest thing about Arc is that it does not need to prove that ETH has no value

I think this is really something to be wary of about Ethereum.

Arc doesn't require at all:

Attack Ethereum.

Kill Ethereum.

Replace Ethereum.

Prove that ETH returns to zero.

Neither is needed.

It only needs to prove one thing:

> **A huge on-chain financial system that can use EVM but does not require ETH at all.**

If this proposition is true,

Its meaning is far greater than the so-called:

```text
Ethereum Killer
```

Much bigger.

Because one of Ethereum’s biggest moats in the past was:

Developer.

Toolchain.

Solidity.

EVM.

Wallet ecology.

standard.

But Arc hints at a future:

All of these things can continue to exist.

Meanwhile:

**Ethereum itself is not necessarily at the center of the value stream.**

---

# 22. This may be the real competition that Ethereum finally faces.

Ethereum has been worried about:

Solana.

Faster chain.

Cheaper chain.

Higher TPS.

I feel more and more that this may not be the real thing to worry about.

The most dangerous contender is not:

> I redesigned a VM that was better than the EVM.

Instead:

> **Thank you for helping the entire industry spend ten years to mature EVM, Solidity, wallets and development tools.**

> **I'll take them all.**

> **I don’t want the rest.**

ETH doesn’t.

No historical debt.

Gas Token is not for speculation.

Fork for more than ten years is not necessary.

Rollup has no complexity.

No need for ideological baggage.

This is where the Arc is at its sharpest.

---

# Conclusion: Ethereum invented the world, Arc only takes away the useful parts

I don't think Arc is a technological revolution.

In a sense it's quite the opposite:

**It could be a technological disenchantment.**

It recognizes:

EVM is good.

Solidity is good.

ERC is good.

Smart contracts are great.

Publicly programmable ledgers are great.

However:

This does not mean that all of Ethereum's design must be inherited together.

It does not mean:

**In order to use blockchain, the world must first accept a volatile Crypto Asset as the economic base.**

What Arc does is actually very simple:

```text
Keep EVM
Keep Solidity
Reserve ERC
Keep smart contracts

Remove ETH dependency
Delete native speculation token
Delete Ethereum settlement
Delete Ethereum historical state
Remove Ethereum Rollup Baggage

Join USDC Gas
Add finality
Add known Validators
Join institutional governance
Join native financial infrastructure
```

That's why I feel like Arc is so clean.

Ethereum is like an old city with more than ten years of history.

Countless roads cannot be demolished.

Countless old buildings cannot be moved.

There are pipelines of different ages buried underground.

Every modernization

We can only continue to build on the past.

Arc is different.

It's a new drawing.

But it's not like starting over from the Stone Age.

It directly brings the best industrial achievements of this old city for more than ten years:

EVM.

Solidity.

ERC.

Wallet.

Tooling.

Then re-plan the city.

So if you have to sum up Arc in one sentence:

**It's not abandoning Ethereum.**

Quite the opposite.

**It understands Ethereum to the extent that it knows which things in Ethereum are worth inheriting and which things are not worth inheriting at all.**

Ethereum created the EVM.

But EVM may not always belong to Ethereum.

USDC was born and grew up in the Ethereum world.

But dollars may not always need ETH to flow on the chain.

The most interesting thing about Arc is here:

**It was born in the technological era of Ethereum, but it is the first serious attempt to build an EVM financial world that does not require Ethereum.**

if it succeeds,

Ethereum leaves the greatest legacy to the next generation,

Probably not ETH after all.

Instead:

**EVM.**
