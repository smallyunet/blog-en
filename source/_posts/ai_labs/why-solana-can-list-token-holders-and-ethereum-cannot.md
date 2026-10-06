---
title: "Why Can Solana List Token Holders While Ethereum Cannot?"
date: 2026-10-06 12:28:19
collection: ai
tags: AI Article Library
lang: en
---

This looks like a simple question.

Given a token such as USDC, we want to know:

> Which addresses hold it right now, and how much does each address hold?

On Solana, this is a relatively natural thing to do.

For a standard SPL token, we can filter the token accounts owned by the Token Program by mint, retrieve the accounts associated with that token, and read their owner and amount fields.

On Ethereum, however, a standard ERC-20 contract does not have a capability like:

```text
getAllHolders()
```

What the ERC-20 standard provides is:

```solidity
balanceOf(address owner)
```

In other words:

> Give me an address, and I will tell you how many tokens it holds.

But it does not provide:

> Tell me every address with a balance.

Of course, Etherscan, Alchemy, and other indexers can still provide holder lists.

They usually do this by scanning historical `Transfer` events, continuously maintaining balances, and reconstructing the list off-chain.

That is different from enumerating holders directly from the current on-chain state.

Why do two chains handle the same concept—a token—so differently?

The answer is not simply that SPL provides one more interface than ERC-20.

Keep digging, and the question leads to the fundamentally different state models of Solana and Ethereum, and their different answers to the question: what kind of computer should a blockchain be?

---

## Layer 1: Solana Can Query the List; Ethereum Cannot Query It Directly

Start with the most visible difference.

The balances in a typical ERC-20 can be understood as:

```solidity
mapping(address => uint256) balances;
```

For example:

```text
balances[Alice] = 100
balances[Bob]   = 200
balances[Carol] = 50
```

The data model looks roughly like this:

```text
USDC Contract
│
└── balances
    ├── Alice → 100
    ├── Bob   → 200
    └── Carol → 50
```

The problem is that a Solidity `mapping` is not inherently enumerable.

If we already know Alice's address, we can query:

```text
Alice → 100
```

But if all we know is:

```text
USDC Contract = 0x...
```

The EVM has no native operation that answers:

```text
How many keys are in balances?
What are those keys?
```

So ERC-20 can easily answer:

> How much USDC does Alice have?

But it cannot directly answer:

> Who owns USDC?

---

Solana's token model is different.

An SPL token's balances are usually not stored inside a Token Program in an:

```text
owner → balance
```

mapping.

Instead, they are stored in separate token accounts.

For example:

```text
USDC Mint
│
├── Token Account A
│   ├── mint   = USDC
│   ├── owner  = Alice
│   └── amount = 100
│
├── Token Account B
│   ├── mint   = USDC
│   ├── owner  = Bob
│   └── amount = 200
│
└── Token Account C
    ├── mint   = USDC
    ├── owner  = Carol
    └── amount = 50
```

Querying holders can therefore become:

```text
Accounts owned by the Token Program
        ↓
Filter mint == USDC
        ↓
Filter amount > 0
        ↓
Aggregate by owner
        ↓
Holder List
```

There is another important detail:

> A token account is not the same thing as a wallet.

One wallet can own multiple token accounts for the same mint, so the results still need to be aggregated by owner.

But the key difference is already visible:

> On Ethereum, a token balance is usually state inside a contract. On Solana, a token balance is explicitly represented by a separate account.

---

## Layer 2: Is the Difference About Stacks and Heaps?

At this point, a plausible explanation may come to mind.

Contract execution on Ethereum must be deterministic.

Every node executing the same transaction must obtain exactly the same result.

For example:

```solidity
mapping(address => uint256) balances;
```

The location associated with Alice is deterministic.

Could the problem be that the EVM lacks the persistent heap of a conventional program—a structure that can grow dynamically and organize objects through pointers—and therefore cannot maintain a dynamic, enumerable set of holders?

Conversely, could Solana know about every token account because it supports a more flexible dynamic heap?

The answer is:

**No.**

We first need to distinguish two very different concepts:

```text
Memory used while a program executes
```

And:

```text
Persistent state on the blockchain
```

---

### The EVM's Persistent State Is Not Its Stack

The EVM does have a stack when it executes a program.

It also has memory.

But both belong to the temporary environment of a single execution.

In simplified form:

```text
EVM

Stack
  → Temporary
  → Disappears when execution ends

Memory
  → Temporary
  → Disappears when execution ends

Storage
  → Persistent
  → Written into on-chain state
```

In an ERC-20 contract:

```solidity
mapping(address => uint256) balances;
```

If this is a state variable, it lives in:

```text
Contract Storage
```

Not in the stack.

Nor can a `mapping` be accessed deterministically because it belongs to some kind of stack structure.

It uses deterministic storage addressing.

Conceptually, we can think of it as:

```text
storage_location
=
keccak256(key, mapping_slot)
```

So:

```text
Alice
   ↓
Deterministic calculation
   ↓
A Storage Slot

Bob
   ↓
Deterministic calculation
   ↓
Another Storage Slot
```

Given the same key and the same contract state, every node obtains the same storage location.

But this still does not provide:

```text
enumerate all keys
```

Because the data structure is designed for:

```text
key → value
```

Not:

```text
all keys → values
```

---

### Solana Does Not Have a Persistent Heap Either

A Solana program can certainly use dynamic memory while executing.

For example, Rust allows:

```rust
let mut orders = Vec::new();
orders.push(order);
```

These objects can live on the heap during execution.

But once the transaction finishes, this runtime memory disappears too.

Solana does not permanently preserve a process-memory structure like:

```text
pointer
  ↓
heap object
  ↓
pointer
  ↓
another heap object
```

on the blockchain as it is.

What can actually persist is:

```text
Account.data
```

A deterministic sequence of bytes associated with an account.

For example:

```text
Account A
data = [01 03 7A ...]

Account B
data = [92 FF 18 ...]
```

A program can take:

```rust
struct Position {
    owner: Pubkey,
    amount: u64,
    orders: Vec<Order>,
}
```

serialize it, and write it into `Account.data`.

If there is not enough space, the account can be expanded.

But what ultimately persists on-chain is still just:

```text
deterministic bytes
```

Not a long-lived heap.

From the perspective of deterministic execution:

```text
Ethereum
and
Solana
```

are not fundamentally different.

Both must satisfy:

```text
Same previous state
+
Same transaction
=
Same new state
```

---

### The Holder Difference Is Not Caused by Stacks or Heaps

This is an important step.

We can rule out a plausible-sounding explanation:

> Solana can enumerate holders not because it has a dynamic persistent heap that the EVM lacks.

Neither chain has such a thing.

The real difference is:

> **How persistent state is organized.**

Ethereum chooses:

```text
Contract
    ↓
Storage Namespace
    ↓
mapping / array / struct
```

For example:

```text
USDC Contract
    ↓
balances mapping
    ↓
Alice → 100
Bob   → 200
```

Alice's and Bob's balances are not independent token-balance objects in Ethereum's state.

They are values inside the USDC contract's storage.

Solana chooses:

```text
Program
+
Many independent Accounts
```

SPL Token can therefore define:

```text
Token Program

Token Account A
mint   = USDC
owner  = Alice
amount = 100

Token Account B
mint   = USDC
owner  = Bob
amount = 200
```

The real dividing line is not:

```text
Stack vs Heap
```

It is:

```text
Contract-internal Storage
        vs
Explicit State Accounts
```

---

## Layer 3: Why Can SPL Token Be Designed This Way?

This raises another question:

Why does Solana make a token balance into a separate token account?

Why does it not simply maintain a:

```text
wallet → balance
```

mapping inside the Token Program, as ERC-20 does?

The answer is:

A token account is just one application of Solana's underlying account model.

On Solana, an account is a first-class object at the system level.

An account roughly contains:

```text
address
owner
lamports
data
executable
```

The most important field here is:

```text
data
```

It is a sequence of bytes interpreted by the program that owns the account.

More importantly:

**Solana programs themselves are essentially stateless.**

Mutable state lives in separate data accounts.

A DEX can therefore look like:

```text
DEX Program
│
├── Pool Account
├── Position Account A
├── Position Account B
├── Vault Account
└── ...
```

The program stores code.

The accounts store state.

SPL Token naturally takes this form too:

```text
Token Program
│
├── Mint Account
├── Alice Token Account
├── Bob Token Account
└── Carol Token Account
```

More precisely:

> Solana does not natively hard-code holder queries into its runtime.

What actually happens is:

```text
Solana natively provides an Account Model
+
The Token Program standardizes token balances as Accounts
```

Together, these make holder enumeration a natural operation.

---

## Layer 4: Why Does the EVM Lack a Similar Concept?

Ethereum's core abstraction is closer to:

```text
Contract
│
├── Code
└── Storage
```

A contract has its own persistent storage.

Developers can define whatever they need:

```solidity
mapping(address => uint256) balances;

mapping(address => Position) positions;

mapping(bytes32 => Order) orders;
```

For a developer, this is straightforward.

For example, storing a balance requires:

```solidity
balances[user] = 100;
```

And that is it.

The developer does not need to:

```text
Create a Balance Account
Allocate space
Specify an owner
Pass the Account into the transaction
Validate the Account
Serialize the Account
```

The EVM hides these details.

But that also comes at a cost.

What the EVM runtime sees is only:

```text
Contract
+
Storage Slots
```

It does not know:

```text
This slot is a Token Balance
That slot is a Position
Another slot is an Order
```

Those meanings belong entirely to the smart contract.

ERC-20 is merely an application-level convention:

```text
If a Contract implements:
balanceOf()
transfer()
approve()
...

We interpret it as a Token.
```

The EVM itself does not know:

> There is a system object called a token here.

Still less does it know:

> There is a type of object called a token holder here.

Naturally, it cannot inherently provide a system capability such as:

```text
getAllTokenHolders()
```

---

## Layer 5: Why Does Solana Make Accounts First-Class Citizens?

Now the question finally reaches the heart of Solana's design.

Why is Solana willing to accept so much complexity and split state into many independent accounts?

One very important answer is:

# To Enable Parallel Execution.

Suppose we have two transactions:

```text
Transaction A:
read  Account 1
write Account 2

Transaction B:
read  Account 3
write Account 4
```

If the runtime knows this before execution begins, it can immediately see:

```text
A and B have no state conflicts
```

It can then execute:

```text
Transaction A ─────→ CPU Core 1

Transaction B ─────→ CPU Core 2
```

at the same time.

If, instead:

```text
Transaction A:
write Account X

Transaction B:
write Account X
```

The runtime can also know in advance:

```text
There is a write conflict
```

So they cannot run in parallel.

This requires a crucial prerequisite:

> A transaction must declare in advance which state it will access.

That is a core feature of Solana's instruction model.

An instruction does not merely say:

```text
I want to call Program X
```

It must also tell the runtime:

```text
I want to access Account A
I want to access Account B
I want to write to Account C
```

The runtime can then build state dependencies resembling:

```text
Read Set
Write Set
```

An account naturally becomes:

```text
A unit of state sharding
+
The granularity of locking
+
A unit of parallel scheduling
```

This gives us the following chain of reasoning:

```text
Accounts are first-class citizens
        ↓
Programs and State are separate
        ↓
Transactions explicitly declare Accounts
        ↓
The runtime knows state dependencies in advance
        ↓
Conflict detection
        ↓
Parallel scheduling
```

And:

```text
Conveniently querying Token Holders
```

is just one consequence of this architecture.

---

## Layer 6: Why Is This Kind of Parallelism Difficult for the EVM?

Now consider the EVM again.

Suppose Alice calls Contract A:

```text
Alice
  ↓
Contract A
```

Before execution begins, we may not know which state the transaction will ultimately touch.

Execution could unfold like this:

```text
Contract A
    ↓
Read Storage
    ↓
Decide whether to call Contract B based on the result
    ↓
Contract B calls Contract C
    ↓
Read other Storage
    ↓
Finally modify some state
```

Even the address of a contract being called may be determined dynamically at runtime.

The EVM is therefore closer to:

```text
Start execution
    ↓
Run the program
    ↓
Gradually discover which state must be accessed
```

Solana is closer to:

```text
Declare Accounts first
    ↓
Establish state dependencies
    ↓
Then execute
```

The order is almost exactly reversed.

That does not justify saying:

> The EVM can never execute in parallel.

Modern EVM clients can certainly use speculative execution, conflict detection, parallel pre-execution, and other optimizations.

The real issue is:

> The classic EVM state model does not require transactions to explicitly declare their complete read sets and write sets before execution.

Deterministic parallel scheduling ahead of execution is therefore much harder.

From the architectural design stage, Solana chose:

> To make developers and the runtime share this complexity.

---

## Layer 7: Solana's Performance Is Not Free

We can now understand an often-overlooked point:

**Solana's high performance is not simply a matter of code running faster.**

It changes the smart contract programming model.

In the EVM, a developer can often write:

```solidity
balances[user] += 100;
```

On Solana, a similar application may need to explicitly handle:

```text
Program
User Account
Position Account
Pool Account
Vault Account
Token Account
Token Program
System Program
...
```

These accounts also involve:

```text
Creation
Space allocation
Passing them into the Transaction
Owner validation
PDA validation
Serialization
Deserialization
Handling Account Locks
```

Complex DeFi transactions therefore often carry long account lists.

This is not an accidental API design problem.

Solana deliberately exposes some state-management complexity that a virtual machine would otherwise hide.

Why?

Because the runtime needs to know:

```text
What will you read?
What will you write?
```

Only then can it be more proactive about:

```text
locking
scheduling
parallel execution
```

We can therefore understand Solana's design as:

> **Trading development complexity for runtime predictability and parallel execution.**

---

## Layer 8: Ethereum Chose a Different Direction

Ethereum's choice is closer to:

> Give developers a sufficiently general-purpose, dynamic virtual computer.

A contract has its own storage.

A contract can dynamically call other contracts.

A program can decide during execution what it will access next.

Developers work with:

```text
Code
+
Storage
```

Without first having to consider:

```text
How many Accounts should I split this into?
Which Accounts must this transaction pass in?
Which Account will require a Write Lock?
```

This makes the EVM programming model feel natural.

Its dynamic composability is particularly strong.

For example:

```solidity
address target = calculateTarget();

ITarget(target).foo();
```

As long as the program's logic permits it, the target can be chosen only when execution reaches this point.

The cost is:

**The runtime knows almost nothing about the meaning of application state.**

It does not know:

```text
What a Token is
What a Pool is
What a Position is
What an Order is
What a Vault is
```

It does not even know:

```text
Which business object a particular mapping represents
```

Off-chain infrastructure must therefore reconstruct the meaning of many things.

For example:

```text
Token Holder Indexer
DEX Indexer
NFT Indexer
Position Indexer
The Graph
Etherscan
Alchemy
```

From this perspective, Ethereum's dependence on indexers is not merely a consequence of the amount of data.

The deeper reason is:

> **Many business objects exist only in a contract's internal semantics, rather than explicitly existing as system-level objects.**

---

## Layer 9: Two Very Different Philosophies of State

At this point, “Why can Solana query holders while Ethereum cannot query them directly?” is no longer a question about token standards alone.

It has become a question about the two chains' different answers to:

> What should the relationship between programs and state be?

Ethereum is closer to:

```text
Contract-centric

Contract
├── Code
└── Storage
```

The program owns its state.

Developers organize storage freely.

The runtime understands as little business meaning as possible.

Solana is closer to:

```text
Account-centric

Program
├── Account
├── Account
├── Account
└── Account
```

The program interprets state.

State is split into many independent accounts that can be addressed, owned, and locked.

The two chains therefore exhibit very different characteristics:

| Dimension | Ethereum / EVM | Solana |
|---|---|---|
| Core abstraction | Contract | Account + Program |
| Where state lives | Contract Storage | Independent Data Accounts |
| Token balance | State inside a contract | Token Account |
| State access | More dynamic | Accounts declared in advance |
| Holder enumeration | Usually depends on an indexer | Token accounts can be scanned |
| Runtime understanding of business objects | Very limited | At least understands account boundaries |
| Parallel scheduling | More difficult under the native model | Naturally supported by the account model |
| Dynamic composability | Very strong | More constrained by account lists |
| State-management complexity | More is hidden by the VM | More is exposed to developers |
| Performance trade-off | Flexibility first | Schedulability and throughput first |

This is not simply a question of:

```text
Which one is advanced
Which one is behind
```

They represent different engineering trade-offs.

---

## Layer 10: Solana Gives Complexity to Developers; Ethereum Gives It to the System and Infrastructure

Summarizing further leads to an interesting conclusion.

Many of Solana's awkward features have a reason behind them.

Why must a program receive so many accounts?

Why do developers have to handle PDAs?

Why do complex transactions have long account lists?

Why do hot accounts matter?

Why must applications consider state sharding during design?

Because Solana wants the runtime to understand:

```text
Which state is being accessed
Which state will conflict
Which transactions can run in parallel
```

Developers must therefore express those relationships explicitly.

In other words:

> Solana gives more complexity to application developers in exchange for determinism and performance at the runtime level.

Ethereum takes another path.

Developers can simply write:

```solidity
mapping(address => Position) positions;
```

And leave many state-organization issues inside contract storage.

The development experience is more natural.

The model is more dynamic.

But the cost is that the runtime struggles to understand:

```text
Who is accessing which application state
What each data structure represents
Which transactions truly do not conflict
```

The complexity does not disappear.

It is transferred elsewhere:

```text
Indexer
Client
Rollup
Execution Engine
State Database
Off-chain Infrastructure
```

The distinction is not:

```text
One is complex
One is simple
```

It is closer to:

> **The complexity is placed in different locations.**

Solana tends toward:

```text
Complexity → Developers + Explicit state model
```

Ethereum tends toward:

```text
Complexity → VM + Client + Indexer + Infrastructure
```

---

# Finally, Back to Holders

We can now answer the question at the beginning of this article again:

> Why can Solana query a token's holders while Ethereum cannot query them directly?

At the most superficial level:

> SPL Token uses standardized token accounts, whereas ERC-20 does not provide holder enumeration.

One level deeper:

> ERC-20 balances usually live inside contract storage, while SPL token balances are explicitly represented by separate accounts.

Deeper still:

> Solana makes accounts first-class citizens in its state model, while Ethereum's main abstraction for state is contract storage.

And deeper again:

> Solana wants transactions to explicitly declare state dependencies before execution so the runtime can detect conflicts and schedule parallel execution.

Keep going:

> Solana is willing to give up some freedom in dynamic state access and increase the burden on developers in exchange for stronger runtime schedulability and parallelism.

Ethereum prefers to provide:

> A dynamic, general-purpose smart contract environment that is easy to compose.

Ethereum contracts can therefore organize their internal state more freely, but the runtime has difficulty understanding these business objects at the system level.

Ultimately, the question reveals:

```text
Ethereum:
Programs freely own and access state.

Solana:
State is explicitly split into Accounts,
and programs operate around those Accounts.
```

So:

```text
Solana can query Token Holders
```

is not an isolated RPC feature.

It is a small entry point.

Follow it deeper, and we eventually see:

> **Solana and Ethereum made two different choices about how a blockchain should organize state, execute programs, and use parallel computation.**

The holder list is simply one of the most readily observable consequences of that architectural difference.
