---
title: Why Crypto Wallet Products Struggle to Make Money
date: 2026-10-14 09:11:56
collection: ai
tags: AI Article Library
lang: en
---


I've worked on several cryptocurrency wallet projects. Looking back, I've become increasingly certain of one thing:

**Building a cryptocurrency wallet is one of the stupidest startup ideas in the entire Web3 industry.**

There's something deeply absurd about this industry: plenty of people build wallets, but very few actually make money from them. A wallet may need five or six developers, cost hundreds of thousands of yuan a month, support more than a hundred chains, and carry enormous security risks. Yet the finished product is something nobody is willing to pay for.

Even more absurdly, many founders start hiring developers before they've even figured out how the wallet will make money.

## 1. I've Seen Firsthand How Several Wallet Projects Died

In February 2025, Bybit suffered a security incident that shocked the entire cryptocurrency industry. Approximately $1.5 billion in crypto assets was stolen.

Afterward, I met two developers who had left teams working on Bybit's wallets. From what I understood at the time, the original wallet development group had undergone extensive layoffs, with almost the entire team cut.

In May 2025, I changed jobs and joined a company called Tantin Chain (天体链).

The company had close ties to Coinstore and was developing a public blockchain, a cross-chain bridge, and a wallet simultaneously. Both the lead of the wallet team I joined and one of its core developers had come from Bybit.

I worked there for just one month. Before the product officially launched, the company laid off the entire development team.

Even more outrageous, when I later investigated the company's background, I found that it originated in Cambodia, appeared to have suspected connections to projects with pyramid-scheme backgrounds, and had a history involving alleged exit scams.

I even wrote an article, “The Company Is Gone, and I'm Unemployed Again,” making my findings public. Those assessments about pyramid schemes and exit scams were conclusions from my investigation at the time, rather than findings established by a court.

A project with a complicated background hired a group of developers to build a public chain, a cross-chain bridge, and a wallet all at once. After a month of work, with the product still unlaunched, the whole team was dissolved.

Later, in August and September 2025, I worked part-time on a wallet startup project at a traditional company in Beijing.

The company originally worked on Face ID-related business. Its head wanted to build a wallet and sought outside developers, hoping to get the product built as cheaply as possible.

After participating for a while, I increasingly felt the project had no future.

These people hadn't even thought through the most basic questions: Who would use the wallet once it was built? Where would users come from? How would it make money? Why would it be able to compete with MetaMask or Phantom?

They knew none of this, yet still dared to hire people to build a product.

**This is the actual level of many Web3 startups: they don't know how to run a business. They only know how to spend money hiring programmers to write code.**

These experiences made me think about why wallet projects are so difficult. There aren't any fundamentally unsolvable technical problems, but their business models are riddled with holes.

Developers work hard to build the features. Then the boss discovers there are no users, no revenue, and no next funding round, and the entire project goes straight into the trash.

## 2. A Wallet Is a Bottomless Pit That Keeps Burning Money

Many people's understanding of wallets stops at generating seed phrases, deriving addresses, signing transactions, and checking balances.

What's so difficult about that, they think? A few people working for a few months, and it's done, right?

That idea is utterly naive.

For a multichain wallet operated as a real commercial product, five or six developers are just the starting point. At salaries of 10,000 to 20,000 yuan each, labor alone costs around 100,000 yuan per month.

Then come server costs.

To support different public blockchains, a wallet must continuously query balances, monitor transactions, identify ERC-20 transfers, internal transactions, contract calls, and historical transaction records.

Where does that data come from?

You either buy services from third-party providers such as Alchemy, BlockPI, Ankr, and QuickNode, or run your own nodes and indexers.

Third-party RPC isn't a free charity. For workloads involving continuous monitoring and large numbers of historical queries, costs of $200 to $300 per chain per month haven't been unusual in my experience.

And not every RPC supports comprehensive identification of internal transactions.

If one provider doesn't support it, you need another. If providers return different formats, you have to adapt them yourself. If an API has rate limits, you need caching. If a chain upgrades, you have to update your code accordingly.

If none of that solves it, you run your own nodes, maintain your own databases, and build your own transaction indexes.

And that's just querying transaction data.

What if you want to support 200 or 300 chains?

That doesn't necessarily mean running a separate node for every chain. But adapting to different blockchains, providing data services, handling upgrades, and resolving failures can still push infrastructure costs to extraordinary levels.

That doesn't even include security audits, mobile maintenance, app store updates, operations, or customer support.

A medium-sized multichain wallet can easily cost hundreds of thousands of yuan a month.

**The nastiest part is that these expenses don't disappear once development is finished. Every day the product stays alive is another day of burning money.**

Finishing development isn't the start of making money. It's the start of losing money continuously.

## 3. The Biggest Joke About Wallets: Your Users Have Money, but You Can't Earn Any

Suppose a user keeps 100 BTC in your wallet.

How much do you earn?

Zero.

Suppose they keep 1,000 ETH there.

How much do you earn?

Still zero.

Suppose they leave those assets there for ten years without making a single transaction.

You still have to pay ten years of server and maintenance costs out of your own pocket.

**This is the most absurd thing about non-custodial wallets: your users may be multimillionaires, while your company is too broke to pay its developers.**

Because a wallet isn't a bank.

Your users' money isn't yours. You can't lend it out, earn an interest spread, or charge management fees simply because they hold a lot of assets.

So how do wallets make money?

Through swaps, DEX aggregation fees, cross-chain bridges, and commissions on fiat on-ramps and off-ramps, staking, and DeFi products.

In plain terms, they collect tolls.

If users don't trade, you have no revenue. If they only hold coins, you can only lose money.

This makes the wallet business model deeply contorted.

A wallet is supposed to help users manage assets. But to make money, the wallet company has to find ways to keep users trading.

**A tool originally meant for storing money ends up having to push users relentlessly to take that money out and trade, just to support itself.**

What wallet companies really want has never been how much users hold, but how often they trade.

And users aren't even willing to pay a $5 monthly subscription.

What entitles you to charge them?

A prettier UI? Support for more blockchains? An added AI feature?

The market is full of free wallets with more complete features and longer security track records. Why should users pay extra for yours?

**The most hopeless thing about wallets isn't being unable to build one. It's building one and discovering users don't think it's worth paying for at all.**

## 4. Wallets May Be Among the Highest-Risk, Lowest-Profit Software Products

Beyond development and server costs, there's an even more frightening issue: security.

In February 2025, about $1.5 billion was stolen from Bybit.

The truly frightening part was that the incident involved the signing process of the Safe multisignature wallet.

The attackers compromised Safe's development environment, modified frontend JavaScript, and tricked Bybit's multisig signers into approving a malicious transaction.

What is a multisig wallet supposed to do?

Isn't its purpose to prevent the theft of one private key from causing the loss of all the wallet's assets?

And what happened?

**There's no need to crack the multisig contract or steal everyone's private keys. If you can control the signing interface users see, you may be able to get them to hand their assets to the attacker themselves.**

Then, in October 2026, reports emerged of another hardware wallet supply-chain security incident involving Ledger.

Attackers were suspected of tampering with hardware wallets through distribution channels, planting malicious components inside the devices to steal seed phrases during wallet initialization. Some on-chain researchers estimated suspected related losses in the tens of millions of dollars, with the final scale still unconfirmed.

This wasn't a confirmed compromise of Ledger's official secure element. Rather, the hardware devices may have been tampered with in the supply chain.

But what difference does that make?

For users who lose assets, whether the cause is a software vulnerability, a frontend attack, or a compromised supply chain, the end result is the same: the money is gone.

Software wallets can be attacked, and hardware wallets can be tampered with. Private keys can leak, multisig signers can be deceived, supply chains can be compromised, and users can sign malicious approvals.

**The security risks of wallets never end.**

If an ordinary app has a bug, at worst you fix it, roll back, and pay compensation.

If a wallet has a bug, it may directly cause tens or hundreds of millions of dollars in asset losses.

And how does the wallet company make money?

By charging a fraction of a percent in swap fees.

**You earn a few thousand dollars in tolls while potentially bearing hundreds of millions of dollars in security risk.**

An exchange as large as Bybit still has the capital and resources to keep operating after a massive theft.

What about a wallet startup with only a few dozen employees?

One security incident could destroy the entire company.

For a small team, building a wallet isn't just an unprofitable venture. It also means taking on a security responsibility you may never be able to bear.

## 5. The Wallet Market Is Already Tiny

Many founders like to tell stories using the global number of cryptocurrency users.

Hundreds of millions of users today, billions in the future, and wallets as the great gateway to Web3.

It sounds as though the entire internet is your potential customer base.

But how many people are actually willing to use your wallet?

First, cryptocurrency itself is still a relatively niche market.

Second, many holders keep their assets directly on centralized exchanges such as Binance and OKX and don't need a separate wallet at all.

Some long-term holders use hardware wallets such as Ledger and Trezor and have little interest in frequent swaps.

The remaining software wallet users are already divided among MetaMask, Rabby, Phantom, Solflare, and others.

If you build an EVM wallet, how will you take users from MetaMask and Rabby?

If you build a Solana wallet, why would users abandon Phantom?

If you build a Bitcoin wallet, the market already has plenty of mature options.

Build a multichain wallet, and development and maintenance costs explode.

Build a specialized wallet, and the market is pitifully small.

Most fatally, even winning users doesn't mean you can make money.

Suppose you have 10,000 users, of whom 1,000 each trade $1,000 per month. That's still only $1 million in monthly trading volume.

At an effective fee revenue rate of 0.5%, that's just $5,000 a month.

**You burn over a hundred thousand or several hundred thousand yuan a month supporting a development team, and the fees you earn may not even cover two programmers' salaries.**

What kind of fucking startup is that?

## 6. Established Wallets Have Been Around for Years, and Few Have Truly Become Huge

Now look at BlueWallet and Unstoppable Wallet.

Neither is some newly launched garbage project. Both are established wallets that have operated for years.

BlueWallet focuses on Bitcoin and supports multisig, PSBT, and use alongside hardware wallets.

Unstoppable Wallet focuses on privacy and self-custody, supports multiple cryptocurrencies, is open source, and has technical strengths of its own.

How good are these wallets?

Technically and functionally, they're perfectly capable.

But look at their market performance.

Taking public data from the US App Store in October 2026 as an example, BlueWallet had only around 855 user ratings, while Unstoppable Wallet had around 1,400.

Of course, rating counts aren't download counts, much less active user counts. But they at least show that the scale of public user feedback on the App Store is nowhere near the grandiose “gateway to Web3” some people describe.

These wallets haven't been completely abandoned either. They still release new versions and update features.

Yet after so many years of development, increasingly mature code, and increasingly complete functionality, they still haven't become super-products dominating the mass market.

What does that tell us?

**Spending more time developing a wallet doesn't automatically bring more users. Nor does better technology guarantee that anyone will pay you.**

Many wallets are already technically mature enough, but simply can't grow into large businesses.

Why should a new wallet with no brand, no users, and no distinctive positioning expect to surpass these established products?

You aren't opening up a new market. You're just cramming more garbage into an already overcrowded one.

## 7. Old Wallets Are Shutting Down, Yet New Ones Still Want In

In 2026, Ctrl Wallet, formerly XDEFI Wallet, announced it was shutting down.

Cosmostation Wallet also announced the discontinuation of its wallet services.

Even Exodus, a wallet business that has operated for years, ended support for its Web3 browser extension wallet in October 2026.

The specific reasons these products shut down or scaled back weren't identical. But together they illustrate one thing:

**Even a wallet with an established brand, existing users, and years of development behind it doesn't necessarily have a commercially worthwhile future.**

When leading and established wallets are cutting back, what makes your new team think it can succeed?

Because you can write React Native?

Because you can call ethers.js?

Because you've integrated hundreds of chains?

Or because you added an AI chatbot and think you've found Web3's next source of traffic?

Features you spend six months developing may take an established wallet only a few weeks to copy.

You have no technological moat, no user moat, no distribution moat, and not even basic trust in your brand.

**This isn't entrepreneurship. It's finding yourself an unpaid job where you also have to cover the server bills.**

## 8. Coinbase Has Already Paid for the Lesson Wallet Founders Need

Another deeply ironic example is Base App.

Coinbase has money, users, an exchange, its own Base chain, and an entire Web3 ecosystem.

Conditions for building a wallet could hardly be better.

But even Coinbase recognized that a wallet alone offered little scope for a new business story.

So in 2025, Coinbase repackaged its wallet as Base App and began building a creator ecosystem, SocialFi, and creator tokens, trying to turn the wallet into a super-app combining social interaction, content, and trading.

It looked incredibly ambitious.

But by 2026, Coinbase CEO Brian Armstrong himself acknowledged that the SocialFi experiment hadn't met expectations.

Ultimately, it refocused on trading and self-custody.

**Coinbase had money, people, an exchange, and a public blockchain. After all that effort, it still couldn't prove that a wallet could conjure a new user ecosystem out of thin air.**

Yet some small teams still think building a wallet and adding a little AI, a little social functionality, and a little trading analysis will let them overturn MetaMask and Phantom.

On what basis?

This isn't innovation. It's a complete failure to understand the market.

## 9. Exchanges Don't Build Wallets for the Sake of Building Wallets

Some might ask: if wallets are so unprofitable, why are exchanges such as Binance, OKX, and Bitget all building them?

Because they don't make their money from the wallet itself.

Start with Binance Wallet.

Binance already has an enormous user base, a centralized exchange, liquidity, and a brand.

Does it need another tool purely for storing coins?

Not at all.

One important role of Binance Wallet is to capture on-chain trading that its centralized exchange can't cover.

Many tokens can't enter Binance's centralized spot market because of liquidity, risk, or other reasons.

But what if users want to buy them?

Let them trade inside Binance Wallet.

Those trades might otherwise happen through Uniswap, PancakeSwap, or another DEX frontend. Now Binance can use its wallet to retain that trading traffic.

**Binance isn't building a wallet to give you somewhere to store a seed phrase. It's bringing trades that can't happen on its centralized exchange into its own trading ecosystem as well.**

Now consider OKX Wallet.

OKX developed a standalone Wallet app, but it remains deeply integrated with OKX's trading and account ecosystem.

Why?

Because what OKX wants isn't merely a wallet. It wants on-chain traders, DEX volume, and an extension of its entire trading ecosystem.

Bitget Wallet follows a similar logic.

These exchanges are essentially building another toll road for their existing trading businesses.

They already have traffic, so building the road makes sense.

Your small team doesn't even have any cars, yet wants to spend millions of yuan building a toll road first.

Once it's finished, you fantasize that people will automatically arrive and pay.

**This isn't a business model. It's a daydream.**

## 10. Why Should Users Sacrifice Their Money for Your Startup Dream?

Wallets face a problem even more serious than costs and profitability: trust.

If an ordinary app is bad, users can uninstall it.

If a wallet is bad, users could lose everything.

You're an obscure little team with a few developers and a newly launched product, and you want users to entrust hundreds of thousands of dollars in assets to it.

Why should they?

Because you write good code?

Because you're open source?

Because you've had a security audit?

Open source doesn't mean secure. An audit doesn't mean there are absolutely no vulnerabilities.

Mature wallets have accumulated years of reputation, development experience, vulnerability response procedures, and user trust.

What do you have?

Why should users take the risk of switching to your wallet?

Because your UI is a little prettier? Your fees a little cheaper? You support more blockchains?

**Nobody is obliged to experiment with the safety of their assets to support your startup.**

A new wallet without users can't build a brand. Without a brand, it can't gain trust. Without trust, it can't attract users.

It's an almost inescapable vicious circle.

And every bit of trust you build takes substantial time and money.

By the time you've finally established a brand, your company may already be broke.

## 11. The Wallet Isn't the Business. The Trading Behind It Is.

Put the entire logic together, and wallet entrepreneurship starts to look like a joke.

First, you pay to develop the wallet. Then you pay to maintain it, promote it, establish a brand, and conduct security audits.

Finally, you have to pray that users will trade inside your wallet and contribute a little fee revenue.

You're funding exchange-level infrastructure with a small company's budget, hoping a few swap fees will support the entire team.

What kind of business logic is that?

**It means spending hundreds of thousands of yuan a month to compete for a few thousand dollars in fees.**

Even more laughably, many wallet startups begin planning support for a hundred or two hundred chains before they even know where their first users will come from.

Does supporting more blockchains bring more users?

Then why not support a thousand?

You can support every chain in the world. Without users, you still have no users. Without trades, you still have no trades.

**A wallet nobody needs remains a useless garbage product even after adding support for another hundred chains.**

The biggest paradox of wallets is that costs keep rising with features, chain count, and user scale, while revenue doesn't necessarily rise alongside them.

It's a business model inherently ill-suited to small-team startups.

## 12. Give Up the Dream of Making Money by Building a Wallet

I've participated in several wallet projects, watched entire development teams get laid off, and seen founders start hiring people to write code before they'd even figured out their business models.

Now I finally understand why many wallet projects are doomed to go nowhere from the day they're born.

They solve a technical problem without solving the business problem at all.

Can a wallet be built?

Of course.

Can it support hundreds of chains?

Of course.

Can it look better than MetaMask?

That too.

But so what?

**Technical feasibility doesn't imply any commercial value.**

Users don't need the product you build. Even if they do, mature free alternatives exist. Even if they use it, they may not trade. Even if they trade, the fees may not cover your costs.

Most absurdly, the more successful your product becomes, supporting more chains and serving more users, the higher its infrastructure and maintenance costs may be.

Without enough trading revenue, growth may actually accelerate your losses.

For companies such as Binance, OKX, and Coinbase, a wallet is one component of a trading ecosystem.

For a small team without users, distribution, a trading business, or deep pockets, a wallet is a cost center that burns money indefinitely.

You think you're building a startup. In reality, you're building free infrastructure for the entire Web3 industry.

You think you're competing to become the future gateway to Web3. In reality, you're fighting the giants that already dominate the market for a miserable scrap of trading fees.

You think users will naturally come as long as you get the technology right.

In reality, this market has no shortage of wallets.

**What it lacks isn't a hundred-thousandth wallet. It's a reason for that hundred-thousandth wallet to exist.**

So for small teams still thinking about developing a cryptocurrency wallet from scratch and using it to launch a business, raise funding, and make money, I have just one thing to say:

**Give up that dream.**

Stop confusing technical ability with business ability, development progress with market progress, and the number of supported chains with product competitiveness.

What you've built, others already have.

The users you want, others have already won.

The fees you want to charge, others are already collecting.

But you won't be spared a single cent of the costs.

**The cruelest part of the wallet business isn't being unable to build one, or finding that nobody will use it. It's that you can work yourself to the bone, get everything right, and still end up earning nothing.**

Stop building wallets.

**You think you're prospecting for gold. In reality, you're the person paying out of your own pocket to hand out free shovels to every gold prospector.**
