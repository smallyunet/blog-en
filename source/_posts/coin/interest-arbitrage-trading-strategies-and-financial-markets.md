---
title: Interest, Arbitrage, Trading Strategies, and Financial Markets
date: 2026-03-02 13:52:16
draft_date: 2026-03-02 12:37:54
tags: Investment
---

### Funding-rate arbitrage

Funding-rate arbitrage is a risk-free, principal-protected arbitrage strategy. At first I tried to write scripts to run it myself, but later I discovered that Binance directly provides a bot for this:

<div style="display:flex; justify-content:center; gap:12px; flex-wrap:wrap;">
	<img src="1.png" style="width:35%; height:auto;">
	<img src="2.png" style="width:35%; height:auto;">
</div>

The Arbitrage Bot here is the funding-rate arbitrage bot. Once you open it, there are many tokens to choose from, including sorting by funding rate. Binance's bot is also quite smart: if the funding rate is positive, it opens the normal arbitrage strategy using USDT; if the funding rate is negative, it opens the reverse strategy using the token.

The profit margin of funding-rate arbitrage is very small, especially for major coins such as BTC, where APR is below 1%. In the current weak market environment, you would probably need to hold for more than 300 days just to overcome fee drag. In practice, it is not very cost-effective. If, however, you sort by funding rate and choose the token with the highest one, the APR can be somewhat higher, perhaps 40% or even more. Of course, high returns also mean high volatility and risk:

<div style="display:flex; justify-content:center; gap:12px; flex-wrap:wrap;">
  <img src="3.png" style="width:35%; height:auto;">
  <!-- <img src="4.png" style="width:35%; height:auto;"> -->
  <img src="5.png" style="width:35%; height:auto;">
</div>

Funding-rate arbitrage is delta-neutral. If a token suddenly doubles and liquidates the 2x long contract, would that cause you to lose half your principal? No. Because once the spot position doubles, selling it offsets the liquidation loss exactly. So your principal does not change. The whole process is simply earning the funding rate during that period.

So sorting tokens by funding rate and then opening an arbitrage bot is the only method I have personally tried when I wanted to earn a bit of interest.

### Principal-protected earn

<div style="display:flex; justify-content:center; gap:12px; flex-wrap:wrap;">
  <img src="5.png" style="width:35%; height:auto;">
  <img src="6.png" style="width:35%; height:auto;">
</div>

Simple Earn is simple: deposit funds into Binance and earn interest. The problem is that the APR is not high. USDC only yields about 6%, and there is a very strict cap, supporting only 200 USDC. It is almost useless.

### Dual investment

The key to the Dual Investment strategy is that you need to have a target price in mind. For example, if you are willing to buy BTC at 60000, then you buy at 60000 regardless of whether it keeps falling afterward. The same applies on the sell side.

Dual Investment is more like futures settlement logic. For ordinary retail traders, it is likewise not very useful. Why is the interest high? Because the risk is high too: you can sell too early and miss the upside, or catch a falling knife. Higher risk leads to higher interest.

What if you set a completely absurd price in Dual Investment? For example, if you set it to buy BTC at 100000, could you not just keep earning interest forever because the condition would never be triggered? In reality, interest is determined by risk. When the market sees little risk, nobody is willing to pay a high rate, so a market with a ridiculous strike like that simply does not exist.

### Opening futures, adding leverage

Do not do dumb things.

### Lido

<img src="8.png" width="80%">

An APR of 2.3% is simply too low. As someone once put it, for ordinary people anything below 5% APR is not even worth considering. It takes at least 20 years to make meaningful money.

### Aave

<img src="9.png" width="80%">

Aave's APR is also very low. Even the highest rate is below 2%, which is almost the same as nothing.

### Uniswap

<img src="10.png" width="80%">

At first glance it looks pretty shocking: USDC/ETH shows a 76% APR. The problem is that providing liquidity comes with impermanent loss. You put in 1000 ETH and 1000 USDC, and as the pool keeps being traded against, when you finally withdraw you might be left with only 300 ETH and 1700 USDC. The total token count gets rebalanced, but if ETH moves violently, the economics of your deposit can become completely unattractive. As for stablecoin pools on Uniswap such as USDT/USDC, their yields are naturally very low, around 1%.

### BTC 5-minute up or down

<img src="11.png" width="80%">

Polymarket has launched a BTC 5-minute up-or-down game. So is there any profit opportunity in that market? The conclusion is no. It is a negative-expected-value game. For retail traders and low-frequency participants, it is pure gambling. You could even say that all event prediction markets are gambling. Forget about any stable arbitrage opportunity.

### Free money on Polymarket

On one hand, Polymarket has some novelty markets, such as [Will Jesus Christ Return Before 2027?](https://polymarket.com/event/will-jesus-christ-return-before-2027). In that kind of market, the price of `No` is around 96.2c, which means that if you hold the position for a year until the event settles, the annualized return is about 4%. If you insist on comparing it with traditional bank deposits or cash APR, that yield is actually not bad.

On the other hand, Polymarket has a mechanism where, for certain [eligible](https://help.polymarket.com/en/articles/13364459-holding-rewards) markets, you continue to receive shares at an annualized rate of 4% simply by holding. Some markets have outcomes that are obviously knowable, so you can buy with confidence. For example, if the `No` price in a certain market is 91.1c, then once the event settles you earn close to 9% annualized, and after adding the 4% holding reward, it is actually a fairly decent cash-management option.

### MEV attacks, on-chain scientists, the dark forest

Give it a rest.

### Financial markets

After so many years of development, plus DeFi Summer a few years ago, whether in CEX yield products or DeFi, the moment the market offers a low-risk arbitrage opportunity, hundreds of millions of dollars and high-frequency, low-latency bots from Wall Street giants will rush in. There are very few opportunities left for ordinary players.

How do you make money in financial markets?

1. Trade time for space (farm airdrops)
2. Trade principal for interest (deploy large capital)
3. Trade knowledge and effort for excess return (spot DCA, searching for alpha)

Beyond that, there is no other way.

Retail gets harvested because:

1. It does not have unlimited principal.
2. It lacks top-tier understanding.
3. It lacks extreme patience.

In short, it has none of those things. What it does have is ignorance and the desire to make money.

### DCA strategy

After going through so many projects, Li Xiaolai ultimately concluded that the ultimate trading strategy is DCA. The book [Dollar-Cost Averaging Changes Your Fate](https://ri.firesbox.com/#/cn/) is actually very good and very dense with information. When we feel tempted by certain high-frequency trading strategies, it is worth revisiting the key points mentioned in that book. DCA is exactly the sort of strategy that requires extreme patience.

Of course, both DCA as a strategy and Li Xiaolai's theory also have parts that can feel a bit hand-wavy. For example, by the end of the book his advice is to improve your ability to earn outside the market. That is basically very correct nonsense. Everyone knows that outside income matters.

DCA is a strategy that fights against human nature, and it is indeed hard to execute strictly, especially for people who work in the industry. You see market prices and sentiment every day. It is impossible to keep DCAing when sentiment is euphoric, and it is also hard to restrain yourself when sentiment is depressed.

If you want to make money, you still have to do something that other people cannot do.

### Restaking

I just mentioned DCA. So if you stick to DCAing BTC, can BTC restaking let you earn a bit of interest on top of holding BTC?

<img src="12.png" width="80%">

Babylon, the only project in the restaking space that truly does BTC restaking, offers up to 0.87% APR. That is basically negligible. After all, with $2B locked in the protocol, APR cannot come out of thin air. No matter how much revenue the project generates, once it is spread across $2B of staked assets, the APR cannot be high. So BTC restaking is hardly worth doing.

### Earning outside the market

I have been thinking about this question of earning outside the market. I realized that the process of DCA plus hold is mind-numbingly boring.

There is a strange logic in financial markets. Precisely because your ability to earn outside the market is limited, you want to come into finance and look for new high-leverage, high-return opportunities through investing. And yet the big names in investing all tell you that your ability to earn outside the market is what matters most: you need a continuous stream of cash flow.

And in fact, finance only amplifies returns on capital. It does not provide the source of capital itself. The impossible triangle in finance is:

- principal
- rate of return
- time

If you have enough principal, even a low rate of return can still generate excellent results. With 1 million USD and a 10% annual return, you make 100,000 USD in a year. If you are willing to take extremely high risk, micro-cap meme tokens and 100x coins are the most direct path to high returns. Return is the compensation for taking risk.

So if your principal is not large enough and you are not willing to take on that risk, then the only thing left that you can invest is time.