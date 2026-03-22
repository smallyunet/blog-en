---
title: "DeFi Basics: Understanding the AMM Pricing Mechanism"
date: 2025-08-20 22:00:00
draft_date: 2025-08-20 18:15:46
tags: DeFi
---

> This is a DeFi tutorial series aimed at learning and understanding DeFi concepts and principles through hands-on practice. Due to space constraints, only Part 1 is included here:
> 1. [DeFi Basics: Understanding the AMM Pricing Mechanism](/2025/08/20/DeFi Basics1/)
> 2. DeFi Basics: Oracles and Price Feeds
> 3. DeFi Basics: Lending and Liquidation
> 4. DeFi Advanced: Flash Loans and Arbitrage

AMM stands for Automated Market Maker. Its purpose is to enable automated pricing and trading without the need for an order book.

This article explains the core pricing logic of Uniswap V2 and provides complete contract code examples, command-line operations, and real on-chain transactions to support a thorough understanding of AMM.

### AMM Pricing Formula

#### Basic Logic

Uniswap V2 uses the Constant Product Market Maker (CPMM) model, which is also used by the AMM contract in our example. The model is based on a simple invariant:

```
x * y = k
```

This means that for two assets `x` and `y` in the pool, when `x` increases, `y` must decrease, and vice versa, to keep the product `k` constant.

When adding initial liquidity, we set this value of `k`. For example, if we add liquidity at a price of 2000 USDC / 1 WETH (ignoring precision), we get:

```
k = 2000
```

When swapping USDC for WETH, the pool’s USDC increases. To keep `k` constant, the contract calculates how much WETH should remain and sends the rest to the user.

#### First Swap

When we swap USDC for WETH, the pool’s USDC increases, and to maintain the constant `k`, the contract sends out some WETH.

For instance, if we swap 500 USDC, and the pool already has 2000 USDC, the total becomes 2500 USDC:

```
x = 2500
y = k/x = 2000/2500 = 0.8
```

This means the pool must retain 0.8 WETH to keep `k` at 2000, so it sends us 0.2 WETH.

#### Second Swap

If we swap another 500 USDC, the pool now has 2500+500 = 3000 USDC:

```
x = 3000
y = k/x = 2000/3000 = 0.667
```

The pool should retain 0.667 WETH. Since there were 0.8 WETH left from the last trade, we receive 0.8 - 0.667 = 0.133 WETH.

Comparing the two trades: the first 500 USDC got us 0.2 WETH, while the second got us only 0.133 WETH. As the remaining WETH in the pool decreases, its price rises.

#### Price Curve

This is the core logic of AMMs: prices are not fixed but calculated based on remaining liquidity in the pool. The x*y=k formula gives a curve: since `y = k/x`, the graph looks like this:

<img src="1.png" width="50%">

Next, we will interact with the blockchain to experience how AMMs work in practice.

### Example Contract

The source code for the contract can be found at: [smallyunet/defi-invariant-lab@v0.0.1](https://github.com/smallyunet/defi-invariant-lab/tree/v0.0.1)

We will prepare two contracts. The first is [TestERC20.sol](https://github.com/smallyunet/defi-invariant-lab/blob/v0.0.1/contracts/libs/TestERC20.sol), a customizable ERC-20 token with adjustable decimals and minting.

The second is [SimpleAMM.sol](https://github.com/smallyunet/defi-invariant-lab/blob/v0.0.1/contracts/amm/SimpleAMM.sol), which provides functions for adding liquidity and swapping tokens. Though not trivial, we’ll gradually understand the functionality and source code through practice.

All actions are performed on Ethereum’s Sepolia testnet.

### Environment Setup

Prepare your CLI tools and set two environment variables:

```bash
foundryup

export RPC_URL="https://ethereum-sepolia-rpc.publicnode.com"
export PK_HEX="<YOUR_PRIVATE_KEY_HEX>"
```

Clone the repo and switch to the correct branch:

```
git clone https://github.com/smallyunet/defi-invariant-lab/
git switch v0.0.1
cd defi-invariant-lab
```

### Deploy ERC-20 Tokens

#### Deploy Contracts

Deploy two test tokens: one called USDC and one called WETH:

```bash
forge create \
  --rpc-url $RPC_URL \
  --private-key $PK_HEX \
  --broadcast \
  contracts/libs/TestERC20.sol:TestERC20 \
  --constructor-args "USD Coin" "USDC6" 6
```

Deployed at: [`0x84637EaB3d14d481E7242D124e5567B72213D7F2`](https://sepolia.etherscan.io/address/0x84637EaB3d14d481E7242D124e5567B72213D7F2)

```bash
forge create \
  --rpc-url $RPC_URL \
  --private-key $PK_HEX \
  --broadcast \
  contracts/libs/TestERC20.sol:TestERC20 \
  --constructor-args "Wrapped Ether" "WETH18" 18
```

Deployed at: [`0xD1d071cBfce9532C1D3c372f3962001A8aa332b7`](https://sepolia.etherscan.io/address/0xD1d071cBfce9532C1D3c372f3962001A8aa332b7)

#### Verify Contracts

You may verify them like this:

```bash
export ETHERSCAN_API_KEY=your_key
cast abi-encode "constructor(string,string,uint8)" "USD Coin" "USDC6" 6

forge verify-contract \
  --chain-id 11155111 \
  0x84637EaB3d14d481E7242D124e5567B72213D7F2 \
  contracts/libs/TestERC20.sol:TestERC20 \
  --constructor-args <ENCODED_ARGS> \
  --etherscan-api-key $ETHERSCAN_API_KEY

forge verify-contract \
  --chain-id 11155111 \
  0xD1d071cBfce9532C1D3c372f3962001A8aa332b7 \
  contracts/libs/TestERC20.sol:TestERC20 \
  --constructor-args $(cast abi-encode "constructor(string,string,uint8)" "Wrapped Ether" "WETH18" 18) \
  --etherscan-api-key $ETHERSCAN_API_KEY
```

### Deploy AMM Contract

#### Deploy

Set 30 (0.3%) as the swap fee:

```bash
forge create \
  --rpc-url $RPC_URL \
  --private-key $PK_HEX \
  --broadcast \
  contracts/amm/SimpleAMM.sol:SimpleAMM \
  --constructor-args $USDC_ADDR $WETH_ADDR 30
```

Deployed at: [`0x339278aA7A09657A4674093Ab6A1A3df346EcFCF`](https://sepolia.etherscan.io/address/0x339278aA7A09657A4674093Ab6A1A3df346EcFCF)

#### Verify

```bash
forge verify-contract \
  --chain-id 11155111 \
  0x339278aA7A09657A4674093Ab6A1A3df346EcFCF \
  contracts/amm/SimpleAMM.sol:SimpleAMM \
  --constructor-args $(cast abi-encode "constructor(address,address,uint16)" $USDC_ADDR $WETH_ADDR 30) \
  --etherscan-api-key $ETHERSCAN_API_KEY
```

### Mint Tokens

Set addresses:

```bash
export MY_ADDR=0x44D7A0F44e6340E666ddaE70dF6eEa9b5b17a657
export AMM_ADDR=0x339278aA7A09657A4674093Ab6A1A3df346EcFCF
export USDC_ADDR=0x84637EaB3d14d481E7242D124e5567B72213D7F2
export WETH_ADDR=0xD1d071cBfce9532C1D3c372f3962001A8aa332b7
```

Mint 1 million USDC (6 decimals):

```bash
cast send $USDC_ADDR "mint(address,uint256)" $MY_ADDR 1000000000000 \
  --rpc-url $RPC_URL --private-key $PK_HEX
```

Mint 1000 WETH (18 decimals):

```bash
cast send $WETH_ADDR "mint(address,uint256)" $MY_ADDR 1000000000000000000000 \
  --rpc-url $RPC_URL --private-key $PK_HEX
```

[USDC Mint Tx](https://sepolia.etherscan.io/tx/0x7140377c3c495be8a593a97c63cfa768783923861e539d330e49f9a93a2cfacd)  
[WETH Mint Tx](https://sepolia.etherscan.io/tx/0xeb659015a41982a3daaca481869ef4e6afc6c5b0b7a34fa656efc7bc2f9be6ad)

### Approve AMM Contract

Approve AMM to transfer your tokens:

```bash
cast send $USDC_ADDR "approve(address,uint256)" $AMM_ADDR "0xffffffffffffffffffffffffffffffffffffffffffffffffffffffffffffffff" \
  --rpc-url $RPC_URL --private-key $PK_HEX

cast send $WETH_ADDR "approve(address,uint256)" $AMM_ADDR "0xffffffffffffffffffffffffffffffffffffffffffffffffffffffffffffffff" \
  --rpc-url $RPC_URL --private-key $PK_HEX
```

[USDC Approval Tx](https://sepolia.etherscan.io/tx/0x5d71669f6a5a63f439a53e6af801d21fdf23ae67cf43d17e30d34b5f66974354)  
[WETH Approval Tx](https://sepolia.etherscan.io/tx/0x1650337af02c74280b9ae1b83222a35f77e090500e3099f419cb0dc0ec7062ab)

### Add Initial Liquidity

Add 2000 USDC / 1 WETH as initial liquidity:

```bash
cast send $AMM_ADDR "addLiquidity(uint256,uint256)" 200000000000 100000000000000000000 \
  --rpc-url $RPC_URL --private-key $PK_HEX
```

[Tx Link](https://sepolia.etherscan.io/tx/0x060bfff6111946c7c84fe6415f883c92ffe7ff9bd694e5b87598a647b63c089f)

Check pool reserves:

```bash
cast call $AMM_ADDR "getReserves()(uint112,uint112)" --rpc-url $RPC_URL

# 200000000000 [2e11]
# 100000000000000000000 [1e20]
```

### Swap USDC for WETH

#### Contract Code Review

Key function [`swap0For1`](https://github.com/smallyunet/defi-invariant-lab/blob/v0.0.1/contracts/amm/SimpleAMM.sol#L39):

```solidity
function swap0For1(uint256 amtIn) external returns (uint256 out) {
    require(token0.transferFrom(msg.sender, address(this), amtIn), "t0in");
    uint256 r0 = token0.balanceOf(address(this));
    uint256 r1 = token1.balanceOf(address(this));

    uint256 amtInEff = (amtIn * (10_000 - feeBps)) / 10_000;
    uint256 k = (r0 - amtInEff) * r1;
    out = r1 - Math.ceilDiv(k, r0);
    require(token1.transfer(msg.sender, out), "t1out");
}
```

This reflects the x*y=k logic. `amtInEff` accounts for the fee deduction.

#### First Swap Test

Try swapping 1000 USDC:

```bash
cast send $AMM_ADDR "swap0For1(uint256)" 1000000000 \
  --rpc-url $RPC_URL --private-key $PK_HEX
```

[Swap Tx](https://sepolia.etherscan.io/tx/0xf13bd1d1602d7c106c2acdf4cb3b1ec37fa42d8871a682e32cce3f2049fff5a2)

Check balances:

```bash
cast call $USDC_ADDR "balanceOf(address)(uint256)" $MY_ADDR --rpc-url $RPC_URL
cast call $WETH_ADDR "balanceOf(address)(uint256)" $MY_ADDR --rpc-url $RPC_URL
cast call $AMM_ADDR "getReserves()(uint112,uint112)" --rpc-url $RPC_URL
```

You received `0.496019900497512437` WETH. The 0.3% fee explains why it's slightly less than 0.5 WETH.

#### Second Swap Test

Try again:

```bash
cast send $AMM_ADDR "swap0For1(uint256)" 1000000000 \
  --rpc-url $RPC_URL --private-key $PK_HEX
```

[Second Swap Tx](https://sepolia.etherscan.io/tx/0x1ee9ceb0707d77d78669bfb6cc1179bf9d6b31c57b868f5f52ed2f01a4127481)

This time you received `0.491116179005960297` WETH—less than before, showing price slippage.

### Swap WETH for USDC

Feel free to try it out yourself.
