---
title: Solana Smart Contract Development Tutorial (1)
tags:
  - Smart Contracts
  - Tutorial
date: 2025-06-24 21:51:06
draft_date: 2025-06-24 18:36:25
---


> This is a beginner-friendly series that starts from the very basics and teaches you how to develop Solana smart contracts. Due to space, only Part 1 is included here:
> 
> - [Part 1](/2025/06/24/Solana Smart Contract Development Tutorial (1)/): Install the base environment, deploy a HelloWorld contract, call an on-chain contract
> - “Part 2”: Implement the minimal model of a USDT-like contract; define custom data structures and methods
> - “Part 3”: Reuse contract functionality with the official SPL library to issue a standardized token

We’ll start from the most basic operations to learn Solana smart contract development. You only need general programming basics—understanding concepts like OOP is enough. You don’t need prior knowledge of smart contracts on other networks, nor do you need to know the Rust programming paradigm in advance.

### 1. Install the environment

Visit the official Solana installation guide: <https://solana.com/docs/intro/installation>

The docs provide a single command to install all dependencies, as well as a step-by-step guide. Note that Solana CLI requires updating your environment variable file. After everything is installed, the `solana` command should be available:

```
solana --help
```

### 2. Initialize the project

Use the `anchor` command to initialize a smart contract project. This CLI tool was installed in the previous step. Don’t worry yet about the generated directory structure:

```
anchor init hello_sol
cd hello_sol
```

### 3. Write the contract code

There’s a `lib.rs` file under `programs/hello_sol/src`. The `.rs` extension means it’s a Rust source file. Copy the following code into it. Note that the content inside `declare_id` is automatically generated when you initialize your project—you don’t need to copy the exact value shown below:

```
use anchor_lang::prelude::*;

declare_id!("3Zbdw1oWu1CiMiQr3moQeT4XzMgeqmCvjH5R5wroDWQH");

#[program]
pub mod hello_sol {
    use super::*;

    pub fn say_hello(ctx: Context<Hello>) -> Result<()> {
        msg!("Hello, world!");
        Ok(())
    }
}

#[derive(Accounts)]
pub struct Hello {}
```

### 4. Compile the smart contract

Use the `anchor` command to compile the contract code you just added and make sure the compilation succeeds with no errors. You may see some warnings—that’s okay. Rust is very strict and will emit verbose warnings for even small issues. If all goes well, there won’t be error logs in the output:

```
anchor build
```

### 5. Set the local default network

Run this command to make your local `solana` CLI use devnet by default. Devnet is for developers to test programs without spending real SOL:

```
solana config set --url https://api.devnet.solana.com
```

### 6. Create a local account file

This command creates a Solana account in your default local path for deploying smart contracts. Deployments consume fees, which must be paid by an account:

```
solana-keygen new -o ~/.config/solana/id.json  
```

Among the command’s output, there will be a line starting with `pubkey:`—the value after `pubkey` is your local account address. Since we set devnet as the default network in the previous step, you can directly check your local account balance with:

```
solana balance
```

You can also open the devnet [explorer](https://explorer.solana.com/?cluster=devnet) and search for the address you just generated. After searching, the URL will look like:
https://explorer.solana.com/address/75sFifxBt7zw1YrDfCdPjDCGDyKEqLWrBarPCLg6PHwb?cluster=devnet


Of course, you’ll see your account balance is `0 SOL`.

### 7. Request a devnet airdrop

Run this command and your account will receive 2 SOL. The `2` in the argument is the requested amount. Due to faucet limits, you can get at most 2 SOL per request. Don’t worry—that’s enough for our next steps.

```
solana airdrop 2
```

### 8. Deploy the contract to devnet

Now that we have the contract code, a local account, and some SOL in it, we can deploy the contract to devnet. Run:

```
anchor deploy --provider.cluster devnet 
```

If deployment succeeds, you’ll see `Deploy success`. There’s another line to note in the output: the value after `Program Id:` is the contract address after deployment. You can search this address directly in the devnet explorer and see a page like this one. In the URL below, `3Zbdw1oWu1CiMiQr3moQeT4XzMgeqmCvjH5R5wroDWQH` is the address of the contract I deployed:
https://explorer.solana.com/address/3Zbdw1oWu1CiMiQr3moQeT4XzMgeqmCvjH5R5wroDWQH?cluster=devnet


### 9. Call the on-chain contract

In the `hello_sol/app` directory, create a new file named `app.js` and paste in the following code. In short, this script reads your default local account file and uses your Solana account to submit a transaction that calls the smart contract. Each run of the script creates one on-chain transaction.

```
const anchor = require('@coral-xyz/anchor');
const fs     = require('fs');
const os     = require('os');
const path   = require('path');
const { Keypair, Connection } = anchor.web3;

const RPC_URL    = process.env.RPC_URL;
const connection = new Connection(RPC_URL, { commitment: 'confirmed' });

const secretKey = Uint8Array.from(
  JSON.parse(
    fs.readFileSync(
      path.join(os.homedir(), '.config/solana/id.json'),
      'utf8',
    ),
  ),
);

const wallet   = new anchor.Wallet(Keypair.fromSecretKey(secretKey));
const provider = new anchor.AnchorProvider(connection, wallet, {
  preflightCommitment: 'confirmed',
});
anchor.setProvider(provider);

const idlPath = path.resolve(__dirname, '../target/idl/hello_sol.json');
const idl     = JSON.parse(fs.readFileSync(idlPath, 'utf8'));
const program = new anchor.Program(idl, provider);

(async () => {
  try {
    const sig = await program.methods.sayHello().rpc();
    console.log('✅ tx', sig);
    console.log(`🌐 https://explorer.solana.com/tx/${sig}?cluster=devnet`);
  } catch (err) {
    console.error('❌', err);
  }
})();
```

Go back to the project root `hello_sol` and run the following commands to install Node.js dependencies:

```
npm init -y 
npm install @coral-xyz/anchor
```

Still in the project root, run this command to execute the `app.js` script you just wrote. The script will call our deployed smart contract on devnet:

```
export RPC_URL=https://api.devnet.solana.com
node app/app.js
```

Here the environment variable `RPC_URL` is the API endpoint the script uses. Since Node.js scripts don’t use the system proxy by default, if your network is restricted you’ll need a more reliable endpoint than the public RPC. You can use a service like [Helius](https://www.helius.dev/)—just register a free account. If you see the following error while executing the script, it’s a network issue; switch to a more reliable RPC endpoint:

```
❌ Error: failed to get recent blockhash: TypeError: fetch failed
    at Connection.getLatestBlockhash (/Users/smallyu/work/github/hello_sol/node_modules/@solana/web3.js/lib/index.cjs.js:7236:13)
    at process.processTicksAndRejections (node:internal/process/task_queues:95:5)
    at async AnchorProvider.sendAndConfirm (/Users/smallyu/work/github/hello_sol/node_modules/@coral-xyz/anchor/dist/cjs/provider.js:89:35)
    at async MethodsBuilder.rpc [as _rpcFn] (/Users/smallyu/work/github/hello_sol/node_modules/@coral-xyz/anchor/dist/cjs/program/namespace/rpc.js:15:24)
    at async /Users/smallyu/work/github/hello_sol/app/app.js:40:17
```

You might wonder why we don’t need to specify the contract address—how does this script know where the contract you just deployed lives on-chain? Notice the `idlPath` variable in the script. If you open the file at `target/idl/hello_sol.json`, you’ll see the contract’s compiled metadata, which includes the contract address. That’s right—the contract address is generated offline; it doesn’t need to be on-chain for the contract to already have its unique address.

If the script runs without errors, you’ll see the transaction hash for this contract call printed in the terminal, along with a browser URL you can open directly. For example, here’s a transaction that calls the contract:
https://explorer.solana.com/tx/2fnPgKkv3tGKKq72hhRxmW6WFSXuofMzXfY2UYoFZXTdJi37btdESy9NzS2gjpWzXX4CL5F7QfxugpctBVaMcBFY?cluster=devnet

At the bottom of that transaction page, you’ll see the log `Program logged: "Hello, world!"` emitted when our smart contract was called. That’s exactly the `msg` we wrote in the contract code.

### 10. Troubleshooting

If you run into errors when executing any of the above commands or code, first consider whether it’s a CLI tooling version issue. The blockchain space iterates quickly, and version incompatibilities are common. My local environment and versions are:

```
rustup: rustup 1.28.2 (e4f3ad6f8 2025-04-28)
rustc: rustc 1.90.0-nightly (706f244db 2025-06-23)
solana: solana-cli 2.2.18 (src:8392f753; feat:3073396398, client:Agave)
archor: anchor-cli 0.31.1
node: v24.2.0
@coral-xyz/anchor(nodejs): ^0.31.1
```
