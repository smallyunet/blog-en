---
layout: post
title: "How to Execute the Fastest Sniping Trades on Solana"
date: 2026-09-17 12:18:00
collection: ai
tags: AI Article Library
lang: en
---

The first time I took a serious look at sniping latency was when the results fell well short of expectations.

We created a new liquidity pool on Solana Devnet and had two wallets start listening in advance. The pool appeared, the bot detected the opportunity, and then bought. The workflow succeeded, but the page showed a delay of around 13 seconds.

For ordinary transactions, this wait may not be unacceptable. For new pool sniping, it means that the bot has seen the opportunity, but it took a long time to send out the buy order.

We began optimizing layer by layer: first the execution workflow, then the signal source, then validation on mainnet, and finally earlier signals such as Preprocessed transactions and Raw Shreds. The most interesting finding was this: **Reducing one stage from more than ten seconds to a few milliseconds does not mean the trade will execute within a few milliseconds.**

## The first level: First, speed up your transaction process

The original execution mode was called Standard. It follows a more general trading idea: after discovering the opportunity, prepare the information, complete the inspection, construct the transaction, and then submit.

In several successful tests, the "detected submission" recorded on the page was 13.230 seconds, 14.098 seconds and 9.629 seconds respectively, and the corresponding slot differences were 91, 139 and 77 respectively. In the early days, the complete segmentation time was not recorded, so we could not attribute all these ten seconds to a certain RPC query or database operation. But the direction of optimization is already clear: too much work is left until the opportunity arises.

New pool sniping has an advantage: the target token, which wallet to use, how much to buy, and how high the price is acceptable can often be determined in advance. In this case, there is no need to wait for the pool to appear and then prepare it from scratch.

This led to Fast mode. Before a wallet starts listening, it completes unlocking and authorization, checks its balance budget, and prepares token and account parameters. While listening, it refreshes the blockhash and priority fee in the background. When an executable signal arrives, the foreground path only needs to finish constructing, signing, and broadcasting the transaction.

Simulation before sending is also skipped. Solana broadcast usage `skipPreflight: true`, no longer wait for one more processing for pre-simulation. Price caps, fee caps and authorization constraints continue to be retained, but try to use prepared data to make local judgments.

The database also needs to stay off the sending path. An operation record can be created before listening starts. Once a signal fires, sending must not wait for a result to be written. Transaction outcomes and timing data are saved asynchronously in the background.

This also gives "constructing transactions in advance" a more specific meaning. Try to prepare the parts that can be determined in advance, but the new pool address, account combination, and latest blockhash may not be ready yet, so the message still needs to be completed and signed after the trigger. The goal of the warm-up is to make this last step as short as possible.

After the modification, the earliest Fast success sample showed that it only took **4ms**. Then the two wallets were tested at the same time, respectively: **5ms and 5ms**.

Going from seconds to milliseconds is a substantial improvement. Yet the on-chain results showed that both buy orders were only included **29 slots, or about 5 seconds**, after the pool was created.

This exposed the first measurement trap. The 5ms shown on the page measures only the time from meeting the execution conditions to starting the send. It excludes the wait for the signal and the wait for block inclusion after submission. The old “detection to submission” metric and the new “detection to start of broadcast” metric also have different boundaries. Dividing them directly does not justify claiming that the entire trade is thousands of times faster.

After the wait in the application is shortened, the next part of the wait becomes clear: How early do we know that the pool has appeared?

## Second level: advance from Confirmed to Processed

Initially, the listener uses the Confirmed state. It will wait for stronger on-chain confirmation. Processed can provide updates when the node has been processed to the corresponding state.

For the scenario of striving for the earliest purchase in a new pool, it is natural to think of using Processed to trigger the transaction, and Confirmed to be left to the background for observation and reconciliation.

There are two different switches here. Fast determines how to execute after receiving the signal, and Processed and Confirmed determine when to trigger. Changing the latter to earlier will not automatically eliminate the waiting for the former; similarly, Fast can also be used in conjunction with Confirmed.

We changed to Processed and ran another round. The results are somewhat counterintuitive.

The single wallet page has appeared before **11 slots**, this time both Processed wallets arrived. **34 slots**. If you only look at these two numbers, it is easy to doubt: The signal is advanced, why is the transaction later?

Continuing to unpack the timeline, I discovered that there was more than one moment of "seeing the pool" in the system.

The independent observation stream was recorded in the pool at about 16:38:47.72, but the account subscription actually used for transactions did not receive the corresponding update until 16:38:52.440. The difference between the two is about **4.72 seconds**. After the execution conditions are met, it only takes about **42ms** The broadcast starts, and the RPC request takes about **76～82ms**.

It turns out that the monitoring module has seen it early, but it does not mean that the execution module has also obtained the data in time. The largest known wait in this round occurs before the execution signal arrives. Based on these records alone, the entire cause cannot be assigned to upstream push or local processing, but at least the explanation of "these few seconds were spent on RPC broadcast" can be ruled out.

So the optimization began to go deep into the monitoring itself. We record the actual subscription reception time used for transactions, separate "received updates" and "satisfied execution conditions", record the arrival times of Processed and Confirmed respectively, and check the subscription life cycle, reconnection and scheduling at the same time.

When doing the comparison next, we no longer just compare the total slot difference in different rounds, but let two wallets monitor the same new pool: one uses Processed and the other uses Confirmed.

In one of the optimized comparisons, Processed's execution account update arrived earlier than Confirmed's **259ms**. Finally, after establishing the pool, the Processed wallet **28 slots** Buy, Confirmed Wallet is **30 slots**;The application receives the Processed receipt of the former’s purchase order earlier than the latter. **208ms**.

This round finally saw the complete correspondence from signal leading to buying leading.

However, subsequent tests did not maintain this result. Another comparison with the same pool shows that both orders were bought 13 slots after the pool was established, and the Confirmed buy order was still ranked higher in the same block. Another double-processed test showed that it only took 11 to 13ms from trigger to broadcast, but it took 54 slots and about 9 seconds from pool establishment to purchase.

These results allow us to gradually accept the fact that Processed gives the opportunity to execute earlier. Whether the transaction can be completed earlier depends on the actual arrival of the signal, application processing and block falling process. A single best result does not represent stable performance.

## Layer 3: After reaching the mainnet, the bottleneck has changed again

After repeated testing on Devnet, we brought the same idea to the mainnet. Both wallets use Fast + Processed. Each purchase is 0.001 SOL. They enter monitoring first and then create a new pool.

One round worked. The pool was created in slot 447510659, and both orders entered slot 447510667. The difference is **8 slots**. According to the second-level time on the chain, the pool is built to buy the contract. **3 seconds**.

This result is better than some Devnet samples, but not enough to indicate that mainnet is faster. What is really worth noting is the segmented data of this round.

From the time the account update arrives to when the execution conditions are met, it takes **1005ms**. Later, it was detected that the broadcast was started, and they were used separately. **198ms and 199ms**. The RPC request time is close to **1.2 seconds**.

There have been single-digit millisecond application paths on Devnet, but in this round of mainnet testing, post-trigger processing of nearly 200ms occurred. There is about 1 second of execution condition waiting ahead. The next step should be to dismantle these measured times, rather than generally interpreting everything as more intense competition on the mainnet.

The time consumption of RPC also needs to be looked at separately. The return of the call does not mean that the transaction has just been completed, and the buy order status notification may arrive in parallel through another subscription channel. Therefore, all fields cannot be added together as the total delay. In the end, we have to go back to which slot the purchase order actually entered, and then use segmented records to explain why it appeared there.

This is also when priority fees become more worthy of discussion. It helps to participate in transaction scheduling competition, but it does not shorten the local construction for us, nor does it allow a late listener message to arrive early. Our processing method is to sample the background fee, prepare the lower limit and the upper limit of the total fee, and use the cache parameters directly when triggered. Otherwise, in order to get a more "accurate" fee, temporarily waiting for the query may eat up the leading time of the signal.

However, the next lesson the mainnet teaches us is not about fees, but about stability.

After the successful round, the same situation occurred in two consecutive rounds: the pool was built and the task was monitored, but both wallets failed before broadcasting and reported that the authorization had expired.

Digging deeper into the logs, we found that when one of the rounds was triggered, the internal authorization lease was only about approx. **494ms**, while construction and signature took about **792～793ms**. By the time of pre-broadcast check, the authorization has expired nearly 300ms. At this time, there is actually about 14.2 seconds left in the trading opportunity window.

The problem lies in the lease renewal logic: as soon as the task is triggered, the old implementation treats it as no longer needing to renew the lease; but the transaction is still being constructed and signed at this time, and has not yet been sent out. Lease renewal relies on a complete background task cycle and is easily delayed by other work.

Subsequent local implementations separated the lease renewal, allowing it to continuously cover the construction and signing phases, and reserved a construction margin of 1.5 seconds. However, within the scope of this article’s records, there has been no successful retest of the repaired mainnet, so this local repair cannot be directly counted as a verified online result.

At this point, the way to evaluate speed has also changed. We started looking at both success rate and time consumption. A success of 8 slots and about 3 seconds is valuable, but a system that can occasionally run very fast and other times not send at all is far from complete optimization.

## Level 4: If Processed is not early enough

After completing these tests, I will naturally continue to ask: Processed is already in the state after node processing. Can I see the pool building transaction before it?

This brings us to Preprocessed.

From the perspective of the receiving node, the transaction data will go through the process of receiving shreds, reorganization and decoding, execution or playback, and generation of status. Processed account subscription must wait for the corresponding status to appear; Preprocessed can deliver data when the transaction content has been decoded and the receiving node has not yet completed the execution result generation.

For sniping new pools, this means that you have the opportunity to see the pool creation instructions first, without having to wait for the pool account status to be pushed out. The "pre-execution" here describes the processing stage of the receiving node. It cannot be understood as meaning that the transaction has not been sorted by the leader, nor is it equivalent to monitoring the Ethereum-style public mempool.

The price is that the data obtained changes. Account subscription directly gives the status, while Preprocessed gives the transaction content, usually without execution results, balance changes or complete account updates. To take advantage of this lead time, the program needs to identify the pool creation instructions, parse the account and protocol parameters, and then construct a buy order based on the pre-prepared information. This change goes beyond switching confirmation levels. [Interface description of Helius](https://www.helius.dev/docs/preprocessed-transactions/preprocessed-subscribe) clearly differentiates between these two types of data.

When researching providers, we found that pricing and interface versions must be checked together. As of September 17, 2026, Helius’s Preprocessed WebSocket was in Public Beta and available on all paid plans. The Developer plan started at **$49/month**, with usage metered at **0.1 credits per message**. The older gRPC version was marked for deprecation. It would therefore be incorrect to keep claiming that Helius Preprocessed requires the $999 plan. [Helius pricing](https://www.helius.dev/pricing)

Another option is Shyft RabbitStream. It delivers transactions extracted from shreds through gRPC, and the Build package is **$199/month**, the higher Grow and Accelerate are $349 and $649/month respectively. [Shyft Pricing](https://shyft.to/solana-rpc-grpc-pricing)

QuickNode's Blazar also offers `shredTransactionSubscribe`, delivering pre-execution transaction flow via WebSocket. However, the product announcements we reviewed did not give an independent price that could be confirmed, and the package and billing need to be verified before actual use. [QuickNode Product Announcement](https://www.quicknode.com/blog/introducing-shredtransactionsubscribe)

The most questionable numbers at this stage are:**Only 8ms earlier, is it worth it?**

This 8ms comes from the old Helius gRPC document, which means that it is about 8ms ahead of Processed on average. It's not a deal improvement for the entire sniping trade, nor is it a new version of the interface or a speed cap for all providers. Shyft gives a leading range of about 15 to 100ms in its own Pump.fun detection test. The two conditions are different, so we cannot directly determine who is faster. [Helius old gRPC description](https://www.helius.dev/docs/preprocessed-transactions/grpc), [RabbitStream Speed Test Instructions](https://shyft.to/solana-shreds-rabbitstream)

A few milliseconds of lead time might catch an earlier processing opportunity, or the trade might still land in the same block. We have not yet measured this signal ourselves, so the provider’s figures cannot be presented as improvements observed in our own trades.

Especially looking back at the mainnet round, there is about 1 second of waiting for execution conditions, and nearly 200ms of post-trigger processing. If these times are not handled well, the few milliseconds obtained by the new signal can easily disappear in the subsequent process.

## Level 5: Directly receive Raw Shreds

Continuing along the signal, the next step is Raw Shreds.

Preprocessed usually has completed shreds reorganization and transaction extraction for the user. Raw Shreds delivers the original propagation fragments directly and lets the receiver process them. In this way, the time that service providers spend on decoding, encapsulating, and distributing becomes space that they can fight for.

But it also means that work that was once performed by providers now needs to be done by yourself.

It is necessary to have a service that continuously receives UDP, handles duplicate packets and out-of-order, completes necessary data recovery, reorganizes transactions, and identifies pool creation instructions. Address tables, transaction versions, and protocol dependencies cannot be left out. The most important thing is that the entire period from receiving the first package to the time the strategy can actually generate a buy order must be fast enough.

Otherwise, an embarrassing result may occur: the original package is received before others, but the transaction is parsed later than using the ready-made Preprocessed service.

In public service, Helius Raw Shreds are usually **$1000/month/IP**, the Professional user is **$800/month/IP**. This fee is the cost of the Raw feed; if you purchase a new $999 Professional for the discount and add a Raw seat, the total is $1,799/month. If you only purchase Raw, you can add it from the Free plan. [Helius Raw Shreds Documentation](https://www.helius.dev/docs/shred-delivery/raw-shreds), [price list](https://www.helius.dev/pricing)

DoubleZero Edge pricing is based on access region and is calculated per machine per month: Frankfurt and Amsterdam are **$1500**, London, New York, Singapore and Tokyo are **$900**, other regions are **$450**. In addition to subscribing, you also need to install the client and configure the corresponding network access. [DoubleZero Subscription Instructions](https://docs.malbeclabs.com/Edge%20Subscriber%20Connection/)

Jito ShredStream, which has been often mentioned in the past, cannot directly copy old materials. The official announcement states that the service is **Closed September 5, 2026**, so it should no longer be used as a new access option at the time of compilation of this article. [Jito Announcement](https://docs.jito.wtf/lowlatencytxnfeed/)

These are data subscription fees, not counting own servers, network and development and maintenance. The raw feed is also not responsible for sending buy orders, and transaction broadcasts still need their own sending path.

So, how much faster can you spend this money?

We have not yet measured Raw Shreds, so we do not have a number of milliseconds that can be directly reported, let alone a certain number of slots reduction. The amount that determines the profit is the difference between "the current Processed signal is available" and "the Raw is decoded by itself until the strategy is available". Comparing only the collection time will miss the second half of the cost.

Even if the strategy is indeed advanced, the final block placement is still affected by the sending path, leader scheduling, priority fees and account competition. The larger disparity between different providers may also come from region, routing and coverage, rather than the Raw format itself.

A more appropriate next step is to not trade first, receive Processed, Preprocessed and Raw at the same time on the same machine, and align them according to the same pool building transaction. Record the moment when the strategy is available, observe common situations and the slowest batch of samples, and then see whether there are missed reports and how long it takes to recover after disconnection. Only then can you decide whether buying earlier signals is worth it.

The signaling products themselves also continue to evolve. Helius currently also lists Preconfirmations that may predate shreds, but coverage relies on participating validators. It reminds us once again that "fastest" requires conditions: in which region, which transactions are covered, and which processing stages are compared. [Official signal level description](https://www.helius.dev/docs/preprocessed-transactions/preprocessed-subscribe)

## From tens of seconds to a few milliseconds

Looking back at this process, I initially faced a waiting time of more than ten seconds for the application. Moving the preparation work forward, skipping the pre-send simulation, and letting the database exit the critical path can bring significant improvements.

After that, the question becomes when the signal actually enters the execution module. Processed brought about a 208ms lead in buy order receipts in a same-pool comparison, but there were also cases where the results worsened when entering the same block or even across rounds.

When we arrived on the mainnet, we obtained a successful result of 8 slots and about 3 seconds, and also discovered new processing delays and authorization races. The older Preprocessed and Raw Shreds are still worth looking into, but they can't automatically fix these already existing waits.

There is another number habit that needs to be broken: slot is not a fixed stopwatch, nor is it equal to the number of blocks that will inevitably be produced. In these tests, Devnet's 34 slots took about 6 seconds, 54 slots took about 9 seconds, and mainnet's 8 slots took about 3 seconds. The real time of the corresponding transaction should be used instead of being uniformly multiplied by 400ms. The blockTime on the chain only has second-level accuracy. Analysis of a lead of several milliseconds depends on accurate reception and execution timing.

In the next round of optimization, we will first focus on the execution condition wait of about 1 second in the mainnet and the post-trigger processing of nearly 200ms. After explaining them clearly, shortening them, and confirming that the task can send transactions stably, then measure how much time the new signal source can gain.
