---
title: How to Develop a Bitcoin Runes Protocol
tags:
  - BTC
  - Tutorial
date: 2025-07-15 22:00:00
draft_date: 2025-07-11 22:18:37
---

Technically, Bitcoin Runes are simple and easy to understand. Implementing Runes only requires the use of the `OP_RETURN` opcode in Bitcoin Script. Precisely because of this simplicity, Runes are relatively clean from a technical perspective and not as complex as Inscriptions or RGB in terms of off-chain state management. The power of Runes lies in how it sparked an ecosystem around it. Though that hype has cooled down, we’re not focused on market trends here—we’ll look at Runes from a purely technical angle and develop a simplified version of the Runes protocol step-by-step. Once we fully understand Runes, we can dive into more complex projects like Alkanes, a protocol supporting WASM smart contracts on Bitcoin.

This tutorial builds on concepts covered in [Bitcoin Script Development Tutorial](/2025/07/10/Bitcoin Script Development Tutorial/). If you are unfamiliar with Bitcoin Script, it is recommended to read that first.

### 1. Define the Data Structure

We define Runes operations as JSON-formatted structures. Language choice is flexible—this example uses Rust. To keep the tutorial simple, we skip some practical details (e.g., the `transfer` structure only supports one target address):

```rust
struct IssueRune {
    op: u8,          // Always 0 = Issue
    symbol: String,  // Rune name
    supply: String,  // Total supply
}

struct TransferRune {
    op: u8,          // Always 1 = Transfer
    id: u64,         // rune_id
    vout: u32,       // Output index of the receiving address
    amount: String,  // Amount to transfer
}
```

Why `vout` instead of address? In Runes, to save space, transfers target an output index (`vout`) in the same transaction:

```json
tx {
  vin:   [...]
  vout:  [
    { vout: 0, scriptPubKey: OP_RETURN <json_data> },
    { vout: 1, scriptPubKey: OP_0 <recipient_1> },
    { vout: 2, scriptPubKey: OP_0 <recipient_2> }
  ]
}
```

Add serialization functions to convert the structs into JSON strings:

```rust
impl IssueRune {
    fn toJson(&self) -> String {
        format!(
            "{{\"op\":{},\"symbol\":\"{}\",\"supply\":\"{}\"}}",
            self.op, self.symbol, self.supply
        )
    }
}

impl TransferRune {
    fn toJson(&self) -> String {
        format!(
            "{{\"op\":{},\"id\":{},\"vout\":{},\"amount\":\"{}\"}}",
            self.op, self.id, self.vout, self.amount
        )
    }
}
```

### 2. Issue Runes

Using a local `regtest` node, confirm the wallet is loaded and has balance. Define the Rune issuance:

```rust
fn issue_rune() {
    let issue = IssueRune {
        op: 0,
        symbol: "Doge".to_string(),
        supply: "1000".to_string(),
    };
    println!("Issue Doge JSON: {}", issue.toJson());
}
```

Output:

```bash
Issue Doge JSON: {"op":0,"symbol":"Doge","supply":"1000"}
```

Convert JSON to hex:

```bash
echo -n '{"op":0,"symbol":"Doge","supply":"1000"}' | xxd -p -c 999
```

Hex:

```bash
7b6f703a302c73796d626f6c3a446f67652c737570706c793a313030307d
```

Pick a UTXO (must be unspent):

```bash
bitcoin-cli -datadir=./ -regtest listunspent
```

Example UTXO:

```json
{
  "txid": "8bfd524e9fc150dab11289d7e6d07860b2b5d6acb54b278a5dc1d1d7631bc8fa",
  "vout": 0,
  "amount": 50.00000000
}
```

Generate a change address:

```bash
bitcoin-cli -datadir=./ getrawchangeaddress legacy
```

My address: `n4Ybvvzm9vRQepuMpXBnTWWbYuTgsPSZCV`

Create the raw transaction:

```bash
bitcoin-cli -datadir=./ createrawtransaction \
  '[{"txid":"...","vout":0}]' \
  '[{"data":"7b6f703a302c73796d626f6c3a446f67652c737570706c793a313030307d"},{"n4Ybvvzm9vRQepuMpXBnTWWbYuTgsPSZCV":49.99}]'
```

Sign the transaction, then broadcast it. Finally, mine a block:

```bash
bitcoin-cli -datadir=./ signrawtransactionwithwallet <rawtx>
bitcoin-cli -datadir=./ sendrawtransaction <signed_tx>
bitcoin-cli -datadir=./ generatetoaddress 1 <your_mining_address>
```

Decode the transaction to confirm `OP_RETURN` content.

### 3. Transfer Runes

Calculate the Rune ID from the issuance transaction:

```rust
fn calc_run_idby_txid() {
    let txid = "e2061d0b8b2f98ee47ba6564c1e7409872432354c7617d278fe0e8c4485ff04a".to_string();
    let mut bytes = hex::decode(txid).unwrap();
    bytes.reverse();
    let run_id = u64::from_le_bytes(bytes[0..8].try_into().unwrap());
    println!("Run ID: {}", run_id);
}
```

Output: `10367542271932362826`

Prepare a transfer structure:

```rust
fn transfer_rune() {
    let transfer = TransferRune {
        op: 1,
        id: 10367542271932362826,
        vout: 1,
        amount: "1000".to_string(),
    };
    println!("Transfer Rune JSON: {}", transfer.toJson());
}
```

Convert to hex:

```bash
echo -n '{"op":1,"id":10367542271932362826,"vout":1,"amount":"1000"}' | xxd -p -c 999
```

Create new address for receiving:

```bash
bitcoin-cli -datadir=./ getnewaddress
```

Create and sign a transaction with the new OP_RETURN and outputs (Runes transfer + BTC output + change). Broadcast and mine a block.

### 4. Parse Runes Transactions

Runes are entirely on-chain but stateless. Parsing must be done off-chain.

Add dependencies in `Cargo.toml`:

```toml
[dependencies]
hex              = "0.4"
bitcoin          = "0.31"
bitcoincore-rpc  = "0.18"
serde            = { version = "1.0", features = ["derive"] }
anyhow           = "1.0"
```

Fetch and decode transaction data via RPC:

```rust
fn parse_tx() {
    let mut cookie = PathBuf::from("/Users/yourname/bitcoin-regtest");
    cookie.push("regtest/.cookie");

    let rpc = Client::new(
        "http://127.0.0.1:18443",
        Auth::CookieFile(cookie),
    ).unwrap();

    let txid = Txid::from_str("...").unwrap();
    let hex = rpc.get_raw_transaction_hex(&txid, None).unwrap();
    parse_op_return(hex);
}
```

Parse the OP_RETURN data:

```rust
fn parse_op_return(tx_str: String) {
    let tx: Transaction = bitcoin::consensus::deserialize(&hex::decode(tx_str).unwrap()).unwrap();
    let script = tx.output[0].script_pubkey.clone();
    let mut iter = script.instructions();
    if let (Some(Ok(_)), Some(Ok(bitcoin::blockdata::script::Instruction::PushBytes(bytes)))) =
        (iter.next(), iter.next())
    {
        let json_str = std::str::from_utf8(bytes.as_ref()).unwrap();
        println!("{}", json_str);
    }
}
```

Sample output:

```bash
{op:0,symbol:Doge,supply:1000}
{"op":1,"id":10367542271932362826,"vout":1,"amount":"1000"}
```

This concludes the core of the Runes protocol. The rest involves building an off-chain indexer to track rune IDs, balances, and link related transactions—exactly how Runes works under the hood.
