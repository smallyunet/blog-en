---
layout: post
title: "Just How Messy Is Ethereum’s Technical Architecture?"
date: 2026-09-23 12:31:00
collection: ai
tags: AI Article Library
lang: en
---

If a group of engineers were asked to design a blockchain from scratch today, there is a high probability that they would not design Ethereum today.

It’s not because Ethereum can’t do things.

On the contrary, the biggest problem with Ethereum is:**It can do everything, but a lot of things are added later.**

More than ten years of operating history, security incidents, Gas model modifications, account abstraction, consensus switching, Layer 2 expansion, and countless special logic left over in order to continue to be compatible with the past are all ultimately deposited in the same set of protocols.

The complexity of Ethereum today is not entirely the complexity brought about by "rich functions".

A considerable part of them are **Complications posed by historical debt.**

If you look at Ethereum as a large-scale software that has been running for more than ten years, it becomes more and more like a legacy system that no one dares to truly reconstruct and can only continue to patch.

The most ridiculous thing is that these patches don't disappear.

A lot of what happened ten years ago still exists in the source code of the Ethereum client today.

This is what I said:

**Ethereum's technology is dirty.**

"Dirty" here does not mean that the code level is poor, but that it has accumulated a large number of special rules, exceptions, compatibility layers and protocol patches that can only be explained in conjunction with historical background.

And this kind of dirtiness can be traced all the way back to 2016 to today.

---

## 1. The DAO: A hacker attack was permanently written into the client source code

The most classic “historical fossil” of Ethereum is The DAO in 2016.

After The DAO was attacked, the Ethereum community finally chose to modify the status through Hard Fork and transfer the ETH in a batch of DAO-related accounts to the refund contract.

There has been debate for ten years about whether this matter violates "Code is Law".

But if we don’t discuss philosophy for the moment and just look at the engineering implementation, this matter is actually more interesting.

Because when you open the source code of go-ethereum today, you can still find:

```go
var DAORefundContract = common.HexToAddress(
    "0xbf4ed7b27f1d666546e30d74d50d173d20bca754",
)

func DAODrainList() []common.Address {
    return []common.Address{
        common.HexToAddress("0xd4fe7bc31cedb7bfb8a345f31e668033056b2728"),
        common.HexToAddress("0xb3fb0e5aba0e20e5c49d252dfd30e102b171a425"),
        common.HexToAddress("0x2c19c7f9ae8b751e37aeb2d93a699722395ae18f"),
        ...
    }
}
```

Not pseudocode.

Not a historical document.

Rather **Code that still exists in the go-ethereum master branch in 2026.**

The file is at:

`params/dao.go`

In the current source code,`DAORefundContract` around lines 561–563, while `DAODrainList()` Immediately after starting at line 564. It directly hardcodes a long list of addresses participating in DAO state migration.

Even the comments are very straightforward:

> DAODrainList is the list of accounts whose full balances will be moved into a refund contract

This sentence is very shocking from an engineering perspective.

A blockchain that claims to be universal, decentralized, and whose state transfer is defined by the protocol is permanently stored in the client source code:

**“When a specific historical event occurred in 2016, the money from these specific accounts was transferred to this specific address.”**

This is not an abstract protocol rule.

This is a historical event.

is an if.

It’s just that this if is written into the consensus.

More precisely:

**There is a tombstone in Ethereum’s consensus rules commemorating the 2016 DAO hack.**

All clients that wish to fully execute the historical state of Ethereum starting from the genesis block must understand it.

This is technical debt in its purest form:

> An event that has been over for ten years can never be truly deleted from the code because it cannot destroy the historical consensus.

---

# 2. A client, carrying all Ethereum for more than ten years.

Next open go-ethereum `params/config.go`.

You will see something very spectacular:

```text
HomesteadBlock
DAOForkBlock
EIP150Block
EIP155Block
EIP158Block
ByzantiumBlock
ConstantinopleBlock
PetersburgBlock
IstanbulBlock
MuirGlacierBlock
BerlinBlock
LondonBlock
ArrowGlacierBlock
GrayGlacierBlock
MergeNetsplitBlock

ShanghaiTime
CancunTime
PragueTime
OsakaTime
...
```

This is not an Ethereum history lesson.

This is the running configuration of the Ethereum client.

There is even such a judgment directly in the source code:

```go
func (c *ChainConfig) IsHomestead(num *big.Int) bool
func (c *ChainConfig) IsDAOFork(num *big.Int) bool
func (c *ChainConfig) IsEIP150(num *big.Int) bool
func (c *ChainConfig) IsEIP155(num *big.Int) bool
func (c *ChainConfig) IsByzantium(num *big.Int) bool
func (c *ChainConfig) IsConstantinople(num *big.Int) bool
...
```

A technical detail needs to be clarified here.

You cannot simply say "Ethereum runs all historical versions simultaneously".

It does not execute the three sets of rules of Byzantium, London and Cancun simultaneously in the current block.

The real problem is more subtle:

**Ethereum clients must know which historical period they are dealing with and choose the correct state transition rules for that period.**

If a node syncs from the genesis block:

The millionth block is to be executed as per the EVM of that era.

The 5 millionth block is to be executed according to another era.

The rules of BASEFEE have changed again since London.

The consensus structure before and after Merge is completely different.

Shanghai will have new rules in the future.

Cancun blob again.

That is to say:

**Ethereum is not a set of protocols.**

From a client engineering perspective, it is actually a long list of protocols spliced together according to block height and timestamp:

```text
Ethereum =
Frontier
+ Homestead
+ DAO
+ Tangerine Whistle
+ Spurious Dragon
+ Byzantium
+ Constantinople
+ Petersburg
+ Istanbul
+ Berlin
+ London
+ Merge
+ Shanghai
+ Cancun
+ Prague
+ ...
```

And these things are not README.

They are consensus.

If you delete the wrong historical branch, the node may calculate a different state root.

So Ethereum has a natural problem:

**The longer the history, the less likely the agreement is to be truly clean.**

New rules can be added.

Old rules cannot be deleted casually.

So the client is deposited layer by layer like geological layers.

2015 is one layer.

2016 is another layer.

One more layer in 2021.

In 2022, PoW will directly replace PoS and build another layer.

The 2024 Blob gets another layer of coverage.

The 2025 EOA delegation adds another layer.

All this history ends up being called:

**Ethereum.**

---

# 3. One of the most embarrassing design debts: EOA is too stupid

Ethereum’s early account system was very simple:

One type is called EOA.

One type is called Contract Account.

The permission model of EOA is basically:

```text
private key
        ↓
ECDSA signature
        ↓
transaction
```

Whoever has the private key owns the account.

Simple and even a little primitive.

The problem is that when blockchain really started to be used by ordinary people, people discovered that the capabilities that wallets really need include:

- Multiple signatures
- social recovery
- Session Key
- Permission control
- Bulk transaction
- Gas payment
- ERC-20 payment gas
- Key rotation
- Daily quota
- Passkey
- Different operations use different authorization strategies

EOA natively has almost none of these capabilities.

This is not because Ethereum cannot do it using the account model.

Simply blaming the problem on "useless UTXO" is not rigorous.

The real question is:

**The EOA authorization model originally designed by Ethereum was too thin.**

The address is highly bound to a secp256k1 private key, and the protocol natively only understands a very simple transaction authentication logic.

So Ethereum spent the next ten years trying to find ways to make accounts less like EOA.

This is where Account Abstraction comes in.

---

# 4. ERC-4337: In order not to change the protocol, a "pseudo transaction system" was built on top of the protocol.

4337 is a very beautiful, but also very dirty example of Ethereum technical debt.

The problems it solves are certainly real.

But look how it works out.

Normal Ethereum transactions:

```text
Wallet
  ↓
Transaction
  ↓
Ethereum mempool
  ↓
Block
```

4337: 

```text
Smart Account
     ↓
UserOperation
     ↓
UserOp Mempool
     ↓
Bundler
     ↓
EntryPoint
     ↓
handleOps()
     ↓
Ethereum Transaction
     ↓
Block
```

Ethereum’s official EIP is even very candid in its description of it:

4337 **Deliberately avoid modifying the consensus layer**.

So it doesn't really create Ethereum native transactions.

Instead, it creates a:

**pseudo-transaction object.**

The name is:

`UserOperation`

What the user sent was not even an Ethereum transaction.

But a UserOperation.

Then a new role is needed:

**Bundler**

Bundler then packages several UserOperations, and finally constructs a real Ethereum transaction and calls a special:

`EntryPoint.handleOps()`

Finally getting into Ethereum.

So in order for the account to have the permission model that a modern wallet should have, we get:

```text
EOA Transaction

+

Smart Account

+

UserOperation

+

UserOp Mempool

+

Bundler

+

EntryPoint

+

Paymaster
```

Paymaster is in turn responsible for Gas sponsorship.

Bundler in turn needs its own simulation and verification logic.

UserOperation has its own nonce.

own gas limit.

own validation.

own mempool.

Even its own DoS defense rules.

From a functional perspective, this is very clever engineering.

But from a system design perspective, it is also an extremely typical patch:

> The underlying trading model was difficult to change, so a trading system was built on top of the underlying trading system.

Then tuck the second set of transactions into the first set of transactions.

This is the most typical technical philosophy of Ethereum:

**Try not to overturn the past and build on it.**

---

# 5. Then Ethereum discovered: 4337 is not a native account abstraction after all

Here comes the problem.

Since 4337 is so good, why did EIP-7702 appear later?

Because the biggest problem of 4337 was written in the design goal from the beginning:

**It does not modify the Ethereum consensus layer.**

This is both its advantage and its original sin.

A large amount of existing ETH and assets are still held in ordinary EOA.

You can't have users all over the world suddenly:

"Please redeploy a Smart Account and move the assets there."

Ethereum will eventually have to continue patching EOA.

Hence EIP-7702.

---

# 6. EIP-7702: An EOA can now "point to code"

The design of the 7702 is quite magical.

It allows an EOA to write a special delegation indicator:

```text
0xef0100 || address
```

Then when this EOA is executed, it can delegate its code behavior to the code at another address.

That is to say:

Previously:

```text
EOA = no code
Contract = has code
```

This is the most basic account classification in Ethereum.

Now:

```text
EOA = no code

But

EOA.code = 0xef0100 || contract_address

Then execute the code of contract_address
```

EIP-7702 even needs to specifically specify what operations such as CALL, CALLCODE, DELEGATECALL, STATICCALL, and EXTCODESIZE, CODESIZE should see when they encounter this delegation indicator.

So a once very simple question:

> “Is there a code for this address?”

It's not that simple anymore.

That's the price of compatibility.

Ethereum cannot say:

**EOA is designed wrong, we scrap EOA.**

Because there are hundreds of millions of addresses out there.

Hundreds of billions of dollars in assets are there.

Hundreds of thousands of pieces of software assume EOA is EOA.

So all I can say is:

> EOA is still EOA, but we allow some special code to be put in EOA; this code is not a real code, but a delegation marker; after EVM sees this marker, it will go to another account to find the real code.

This is typical legacy system engineering.

You can’t tear down walls.

So another pipe was buried inside the wall.

---

# 7. It’s not over yet: now comes EIP-8141 again

If what you just said "4E81 / 4E91" refers to what Ethereum is discussing recently **Native Gas sponsorship / Native Account Abstraction**, what you want to say is probably:

**EIP-8141: Frame Transaction.**

It goes one step further than 4337.

8141 Propose a new transaction type directly:

**Frame Transaction.**

A transaction is split into multiple frames, where different frames can be responsible for:

```text
VALIDATION
PAYMENT
EXECUTION
```

In other words:

The logic of previous Ethereum transactions was basically:

```text
signature → sender → sender pays gas → execute
```

4337 said:

```text
We put a UserOperation outside.
```

7702 said:

```text
EOA can delegate code.
```

8141 goes further:

```text
It is better to dismantle the transaction itself.
Who verifies the transaction, who pays, and who executes can be defined separately.
```

EIP-8141 explicitly targets alternative fee payment schemes, key rotation, smart accounts, etc.

Technical capabilities are certainly stronger.

But look back at this evolutionary line:

```text
EOA
 ↓
Smart Contract Wallet
 ↓
ERC-4337
 ↓
UserOperation
 ↓
Bundler
 ↓
EntryPoint
 ↓
Paymaster
 ↓
EIP-7702
 ↓
EOA Delegation
 ↓
EIP-8141
 ↓
Frame Transaction
```

Ten years later, Ethereum is finally getting closer to a reasonable programmable account model.

What's the cost?

**Everything above basically already exists.**

So the new design is not replacing the old design.

Instead:

Continue to coexist.

---

# 8. Blob: For Layer 2, Layer 1 even grew a new data organ.

Another extremely typical engineering compromise in Ethereum is EIP-4844.

Ethereum’s initial scaling story was not:

> L1 is not responsible for executing a large number of transactions, and will mainly provide Data Availability for Rollup in the future.

Ethereum started out as an execution chain itself.

Later, it was found that L1 expansion was too difficult, so the strategy gradually turned to Rollup-centric.

Here comes the problem.

Rollups require publishing large amounts of data to Ethereum.

Previously put calldata.

Expensive.

What to do?

The most intuitive approach is to optimize calldata.

Ethereum’s final choice is:

**Add a completely new data structure to the protocol.**

Blob.

So a very strange transaction appeared on Ethereum:

**Blob-carrying transaction.**

Blob data:

- Bind to the Ethereum block;
- Availability is guaranteed by the consensus layer;
- EVM cannot read directly like calldata;
- What EVM mainly sees is its commitment;
- The data is not required to be permanently stored in the execution layer state.

EIP-4844 himself wrote it very clearly:

Its direct goal is to provide cheap Data Availability for Rollup.

So now Ethereum is not just one:

**A blockchain that executes smart contracts.**

It also serves as:

**Rollup data publishing layer.**

For this role, L1 specifically adds:

```text
Blob Transaction
Blob Gas
Blob Base Fee
Blob Commitment
KZG
Point Evaluation
Blob Sidecar
```

What is this?

This is the organ left behind by the underlying protocol after the architectural route is changed.

If Rollup-centric roadmap is Ethereum's success, then Blob is brilliant design.

But if you look at it from another perspective - especially from the perspective of "L1 itself should keep its structure simple" - you will get the exact opposite conclusion:

**In order to save an increasingly complex L2 expansion system, Ethereum wrote the special requirements of L2 directly into L1.**

Rollup was originally supposed to be an application on Ethereum.

Eventually the Ethereum protocol in turn modified itself for Rollup.

The tail starts wagging the dog.

---

# 9. The more successful Layer 2 is, the less Ethereum’s overall system will look like “a chain”

This is also what I think is the most ridiculous thing about the Rollup route.

One of the most powerful narratives Ethereum has ever had is called:

**Composability.**

All contracts are in the same state machine.

Uniswap can adjust Aave.

Aave can adjust Maker.

Completed within one transaction.

This is called true composability.

What does Rollup-centric Ethereum do?

Cut the entire Ethereum ecosystem into:

```text
Ethereum L1

Arbitrum
Optimism
Base
zkSync
Scroll
Linea
Starknet
...

Then for each chain:
own sequencer
own bridge
own status
Your own withdrawal rules
own fee
own explorer
own RPC
own failure mode
```

The problems Ethereum originally solved were:

> Don't leave it to everyone to maintain their own databases and trust systems.

The Rollup era finally becomes:

> Each Rollup maintains its own state machine.

Then everyone will study:

- shared sequencing
- intents
- chain abstraction
- interoperability
- based rollups
- canonical bridges
- liquidity aggregation

First take apart a unified system.

Spend another five years figuring out how to put it back together.

It’s hard not to doubt:

**Are you solving the capacity expansion problem or creating the next generation infrastructure problem?**

What’s even more ironic is that in order to maintain this architecture, Ethereum L1 needs to be constantly modified.

Blobs are the most obvious example.

---

# 10. Difficulty Bomb: a “temporary mechanism” that has been postponed again and again

There is also a very classic design in the history of Ethereum:

**Difficulty Bomb.**

In order to force the network to eventually switch from PoW to PoS, Ethereum designed the Difficulty Bomb in the protocol, which ultimately increased the mining difficulty exponentially.

Beautiful in theory:

> At that time, PoW will become more and more difficult, forcing everyone to upgrade to PoS.

What actually happened?

PoS didn’t go as planned.

So:

postponed.

Then it was postponed.

Then continue to postpone.

Difficulty Bomb delay has been included in multiple upgrades in Ethereum history.

For example:

```text
Byzantium
Constantinople
Muir Glacier
London
Arrow Glacier
Gray Glacier
```

Therefore, the protocol mechanism originally used to force the migration of technical routes on time eventually turned into a historical debt that required repeated maintenance.

This is almost a microcosm of the engineering history of Ethereum:

**Design a mechanism to solve future problems.**

The future did not happen as expected.

So I wrote another EIP and modified the previous mechanism.

---

# 11. The Gas rule itself is also an archeology

The Ethereum EVM looks like a pinned virtual machine.

Not really.

The gas cost of many opcodes has changed historically.

Why?

Because someone found that the original gas pricing was unreasonable and could cause DoS.

So the price was repriced.

Typical example:

EIP-150.

EIP-1884.

EIP-2929.

Re-evaluate again and again:

```text
How much should SLOAD cost?

How much should BALANCE cost?

What about EXTCODESIZE?

Should Cold Access and Warm Access be different?
```

Berlin later even introduced:

```text
cold access
warm access
access list
```

So how much Gas is required to execute an opcode depends not only on the opcode.

Also depends on:

**What has this transaction previously visited.**

There are certainly good security reasons for this.

But from the perspective of VM elegance:

This can hardly be called beautiful.

This is a scar left by a real system that constantly recalibrates its cost model after encountering attacks and performance bottlenecks.

---

# 12. SELFDESTRUCT: An opcode is alive and the semantics have been changed.

There’s something else that’s particularly illustrative of Ethereum’s problems:

`SELFDESTRUCT`

In early Ethereum, contracts could SELFDESTRUCT:

Remove code.

Clean storage.

Send ETH.

It was later discovered that this introduced a lot of complexity and hindered future state structures like Verkle Trees.

What to do?

Delete opcode?

Cannot be deleted.

Because historical contracts may rely on it.

So EIP-6780 did a very Ethereum thing:

**Keep the SELFDESTRUCT name and opcode, but change its semantics.**

Now, unless SELFDESTRUCT occurs in the same transaction as the contract creation, it basically no longer actually "destroy" the account's code and storage.

So:

```text
SELFDESTRUCT
```

This opcode:

**It is no longer necessary to self destruct.**

If a language design goes as far as:

> The function name cannot be changed, nor can the function be deleted, but we can change the function behavior.

That's basically one of the most standard symptoms of legacy compatibility.

---

# 13. Ethereum today is no longer a set of elegant designs, but a city

At this point someone may object:

Isn’t Windows compatible with software from decades ago?

Doesn’t the Linux Kernel also have historical baggage?

Doesn’t TCP/IP also have a lot of historical problems?

That's right.

This just illustrates the problem.

One of Ethereum’s greatest achievements today may also be its greatest technical flaw:

**It can no longer be reinvented.**

It is no longer an experimental blockchain project.

It is already a city.

A city will not bulldoze and rebuild its entire city just because the road planning is poor.

Only:

Build an overpass.

Build the subway.

Add traffic lights.

Add overhead.

Modify one-way streets.

Dig another tunnel.

Then if you look at the map a few decades later, you will find:

Why is this road so strange?

Because there was a building here 20 years ago.

Why does the subway take a detour?

Because the demolition was not discussed 15 years ago.

Why is there suddenly a three-story interchange here?

Because there were too many cars later.

This is Ethereum.

---

# 14. The real problem of Ethereum is not complexity, but "historical complexity cannot be recovered"

Complexity itself is not a sin.

Solana is also complex.

Bitcoin is also historically compatible.

Operating systems are more complex.

The really uncomfortable thing about Ethereum is:

**A lot of its complexity comes from historical choices, and that complexity is difficult to reclaim.**

It’s been ten years since The DAO.

The DAO fork code is still there.

PoW is gone.

Clients still have to understand the chain in the PoW era.

EOA is no longer enough.

Cannot be deleted.

So add 4337.

4337 is not native enough.

So add 7702.

7702 is still just a transition.

So study 8141.

L1 expansion is difficult.

So L2 was developed.

L2 needs data.

So L1 adds Blob.

Blob has its own Fee Market again.

Continue with PeerDAS, Danksharding later.

Look at each step individually:

**There are reasons.**

This is the most dangerous place.

Bad systems usually don't happen because someone made a stupid decision one day.

Instead:

**Every local decision seems perfectly reasonable.**

Then ten years later, when all the "reasonable decisions" are stacked together, you get a system that no one can completely fit into their head.

---

# 15. This is the “dirtiest” thing about Ethereum

So when I say:

**Ethereum’s technical architecture is dirty.**

What I don't mean is:

Ethereum developers are poor.

Quite the opposite.

Ethereum may have the strongest group of protocol engineers in the entire blockchain industry.

Here's the real irony:

**The need for so many top engineers is largely due to the fact that the system has become so complex that it must be maintained by top engineers.**

DAO Fork.

A dozen generations of Hard Fork.

Gas repricing.

EIP-1559.

PoW → PoS.

Execution Layer + Consensus Layer.

Smart Contract Wallet.

ERC-4337.

UserOperation.

Bundler.

EntryPoint.

Paymaster.

EIP-7702.

EOA delegation.

EIP-8141.

Frame Transaction.

Rollup.

Blob.

Blob Gas.

KZG.

Data Availability.

Taken alone, all of this can write a beautiful technical paper.

But put them all together, and what you see is no longer "elegance."

Instead:

**The entire historical cost paid for a system that can never be shut down or reinvented.**

The most worthy of study of Ethereum may no longer be:

> How to design a blockchain?

Instead:

> What will a blockchain become after running for more than ten years and carrying hundreds of billions of dollars in assets?

The answer given by Ethereum is:

It will become a huge state machine in which almost nothing can be deleted, no history can be forgotten, and any mistakes can only be forward-compatible.

Each generation of engineers feels like they've only added one feature that makes sense.

In the end, the entire agreement turned into a technological archaeological site.

You can even open a file called `dao.go` files,

Then see:

The address left behind by the hacker attack in 2016,

Still lying there quietly.

**This is how dirty Ethereum’s technical architecture really is.**
