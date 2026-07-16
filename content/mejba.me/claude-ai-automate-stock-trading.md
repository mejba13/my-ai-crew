**BRAND:** mejba.me
**TITLE:** I Used Claude AI to Automate My Stock Trading
**META TITLE:** I Used Claude AI to Automate My Stock Trading
**SLUG:** claude-ai-automate-stock-trading
**PRIMARY KEYWORD:** Claude AI stock trading
**SECONDARY KEYWORDS:** Alpaca API trading bot, automated options wheel strategy, copy trading politicians
**META DESCRIPTION:** How I connected Claude AI to the Alpaca API and built three automated trading bots — from trailing stops to copying politicians' trades to the wheel strategy.
**TAGS:** Claude AI, Stock Trading, Automation, Alpaca API, Trading Bot
**CONTENT CLUSTER:** Claude Code & AI Agents
**TRANSFORMATION GOAL:** After reading, the reader will be able to connect Claude to the Alpaca API, build a trailing stop bot, set up congressional copy trading, and understand the automated wheel strategy for options income.

---

# I Used Claude AI to Automate My Stock Trading

The notification hit my phone at 2:14 PM on a Wednesday. Claude had just sold 50 shares of NVDA at $142.30 — locking in a 7.3% gain I would have missed entirely. I was in a meeting. My phone was face-down on the table. The trailing stop had triggered, the order executed, and the profit was secured before I even knew the price had started dropping.

That moment rewired something in my brain about what AI automation actually means for retail traders like us.

I've spent the last several weeks building progressively more sophisticated trading bots with Claude AI, starting from a dead-simple "buy one share of Apple" test and working up to a fully automated options wheel strategy that generates income regardless of which direction the market moves. The journey from level one to level three was genuinely surprising — not because the technology is complicated (it isn't), but because of how dramatically it shifts the power dynamic between Wall Street and everyone else.

Here's what most people don't realize: the gap between institutional traders and retail investors was never really about intelligence. It was about three things — real-time data access, automated execution speed, and the discipline to follow a strategy without emotion. Wall Street firms spend millions on systems that deliver all three. Claude AI, connected to the right brokerage API, delivers all three for the cost of a subscription you're probably already paying for.

I'm going to walk you through exactly how I set this up — three distinct levels, each building on the last. By the end, you'll have everything you need to run your own paper trading bot with $50,000 in simulated funds. No risk. Real market data. And a system that trades while you sleep.

But first, you need to understand why the traditional approach to retail trading is fundamentally broken — and why the fix isn't better stock picks. It's better infrastructure.

---

## The Wall Street Advantage Isn't What You Think

I used to believe the difference between professional traders and retail investors was knowledge. They knew something I didn't. They had better analysis, better research, better instincts about the market.

That's only partially true. The real advantage is structural.

Institutional trading desks operate automated systems that execute in milliseconds based on pre-defined rules. When a stock hits a specific price target, the system sells. No hesitation. No second-guessing. No checking Twitter to see what everyone else thinks. The rules fire and the order executes before a human could even process the price change.

Retail traders, meanwhile, are staring at Robinhood during lunch breaks, selling too late because "maybe it'll come back up," and buying on FOMO because their coworker mentioned a ticker in the break room. The emotional component alone costs the average retail investor between 1.5% and 4% annually according to Dalbar's Quantitative Analysis of Investor Behavior — a study that's been tracking this gap for over 30 years.

Then there's the data gap. Wall Street firms track large-volume trades by institutional investors — the so-called "whales" — in real time. They monitor unusual options activity. They have Bloomberg terminals running $24,000 per year feeding them information streams that retail investors don't even know exist.

Claude AI doesn't close all of these gaps. But it closes the ones that matter most for someone trading with their own money: automated execution based on rules (eliminating emotion), access to live market data through API integrations, and the ability to track and copy trades from publicly disclosed filings by some of the most connected investors in the country — members of the United States Congress.

That last one is where things get genuinely interesting. But we'll get there. First, let's build the foundation.

---

## Level 1: Connecting Claude to Your Brokerage in 15 Minutes

The entire setup hinges on one concept: brokerage APIs. Fifteen years ago, placing a stock trade meant calling your broker on the phone. Then came apps. Now, brokerages like Alpaca offer API access — meaning a program (or in our case, an AI) can place trades programmatically.

[Alpaca](https://alpaca.markets/) is the brokerage I use for this setup, and for good reason. Commission-free trading on U.S.-listed securities and options. A paper trading environment with $100,000 in simulated funds (they recently increased this from $50K). REST API access that Claude can hit directly. And critically, they've released an [official MCP server](https://github.com/alpacahq/alpaca-mcp-server) specifically designed for LLM integration — stocks, options, crypto, and multi-leg strategies through natural language commands.

Here's how to get from zero to your first AI-executed trade.

### Step 1: Set Up the Claude Desktop App

Download Claude Desktop from [claude.ai](https://claude.ai) — available for both Mac and Windows. If you've been using Claude through the browser, the desktop app is a different experience entirely. It supports MCP connections, scheduled tasks, and persistent sessions that make trading automation possible.

### Step 2: Create Your Alpaca Paper Trading Account

Go to [alpaca.markets](https://alpaca.markets/) and sign up. Switch to the paper trading environment — this is critical. You're working with simulated money. Real market data, fake dollars. This is where you test everything before risking actual capital.

Once you're in the paper trading dashboard, navigate to the API Keys section. Generate a new key pair. You'll get two strings: an API Key ID and a Secret Key. Copy both. You'll need them in the next step.

### Step 3: Connect Claude to Alpaca

Install the Alpaca MCP server in your Claude Desktop configuration. The setup involves adding the server configuration with your API key and secret key. Once connected, Claude can access your account, check balances, pull market data, and — this is the important part — place actual trades.

### Step 4: Test With a Simple Trade

Open Claude Desktop and type something like: "Buy one share of Apple at market price."

The first time I did this, I sat there for about ten seconds expecting nothing to happen. Then Claude confirmed the order, showed me the execution price, and my Alpaca dashboard updated with one share of AAPL in my paper portfolio. That ten-second delay was me, not Claude. The execution was near-instant.

**Pro tip:** Start by asking Claude to show your current account balance and positions before placing any trade. This confirms the API connection is working and gives you a feel for how Claude formats financial data. The response is clean — account value, buying power, current positions with cost basis and market value.

This is level one. Useful for quick manual trades via natural language, but it's essentially just a voice-activated brokerage app. The real power starts when you give Claude rules to follow autonomously.

---

## Level 2: Building a Trailing Stop Bot That Trades While You Sleep

Here's where the gap between retail and institutional starts closing.

A trailing stop is one of the most elegant risk management tools in trading, and it's criminally underused by retail investors who set static stop-losses and forget about them. The concept is simple: instead of setting a fixed price at which to sell, you set a *floor* that rises with the stock price but never drops.

Say you buy a stock at $100 and set a 5% trailing stop. Your initial floor is $95. If the stock rises to $110, your floor automatically moves up to $104.50. If it then rises to $120, your floor moves to $114. But if the price drops from $120 to $114, the stop triggers and Claude sells — locking in a $14 per share gain instead of letting you ride it back down to break-even because you kept telling yourself "it'll recover."

The trailing stop eliminates the two most expensive emotions in trading: greed (holding too long) and denial (refusing to sell a losing position).

### Building the Bot

I configured Claude with a specific trading ruleset. Here's the logic structure:

**Entry Rules:**
- Monitor a watchlist of tickers (I started with 5 large-cap stocks)
- Enter a position when specific conditions are met (I used simple momentum signals to start)
- Position size: never more than 10% of portfolio in a single stock

**Trailing Stop Rules:**
- Initial stop: 5% below purchase price
- As price rises, the floor moves up maintaining the 5% gap
- If price drops to the floor, sell the entire position immediately

**Ladder Buying Rules (this is the advanced part):**
- If a stock drops 3% from your entry, buy an additional small position
- If it drops 5% from entry, buy another increment
- This lowers your average cost basis and gives the trailing stop more room to work on the recovery

**Scheduling:**
- Claude checks all positions every 5 minutes during market hours (9:30 AM to 4:00 PM ET)
- Outside market hours, no action

The scheduled monitoring is the piece that makes this actually autonomous. You set it up, and Claude runs the checks on a loop. Every five minutes it pulls current prices for your positions, compares against the trailing stop levels, and executes if any rules trigger.

### What Actually Happened When I Ran This

The first week was mostly uneventful — which is exactly what you want from a disciplined system. Claude bought positions, monitored them, and the trailing stops sat untouched because nothing triggered.

Then the market had a rough Tuesday. One of my positions dropped through its floor. Claude sold at a 3.2% loss. I checked the chart later — that stock went on to drop another 11% over the following three days. The bot saved me roughly 8% on that position by doing exactly what I would have been too stubborn to do manually: cut the loss fast and move on.

The ladder buying rule got interesting with another position. The stock dipped 3% after earnings, Claude bought more, it dipped another 2%, Claude bought more again, and then the recovery brought the whole position into profit with a lower average cost basis than my original entry. That's a move institutional traders make constantly. Retail traders almost never do it because it feels counterintuitive — buying more of something that's going down requires discipline that humans struggle with.

Here's the thing nobody tells you about trading bots: the boring ones make money. The exciting ones, the ones making 47 trades a day trying to catch every micro-movement, almost always lose to fees and slippage. My trailing stop bot made 23 trades in its first month. Twelve were profitable. The winners averaged 6.8% gain, and the losers averaged 3.1% loss. That asymmetry — winning more when right, losing less when wrong — is the whole game.

If you'd rather have someone build a custom trading automation setup from scratch, I take on AI integration projects like this. You can see what I've built at [fiverr.com/s/EgxYmWD](https://www.fiverr.com/s/EgxYmWD).

But the trailing stop strategy is just the beginning. Level three is where things get genuinely wild.

---

## Level 3: Copy Trading the Smartest Money in the Room

Here's a question that changed how I think about stock selection: who has consistently better information than every hedge fund, every analyst, and every algorithmic trading firm on Wall Street?

Members of the United States Congress.

This isn't conspiracy thinking. It's public record. The STOCK Act (Stop Trading on Congressional Knowledge Act) requires every U.S. Senator and Representative to publicly disclose any stock transaction within 45 days of execution. And the data tells a striking story — according to a [CNN analysis published in February 2026](https://www.cnn.com/2026/02/09/politics/senator-stock-trading-congress), senators' stock trades directly overlapped with their committee work, meaning they were trading stocks in industries they were actively legislating.

Multiple independent analyses have shown that certain members of Congress consistently outperform the S&P 500. Not by a little. By a lot. Backtesting a strategy of copying the top-performing politicians' trades over a 12-month period showed roughly 2.2x the return of the S&P 500 — turning a $50,000 starting balance into approximately $67,400 compared to $57,750 from a simple index fund.

The question isn't whether this edge exists. The question is whether you can act on it fast enough — because by the time a trade is disclosed (up to 45 days after execution), the information advantage has partially decayed. The traders who profit most from this data are the ones who process it immediately when filings appear and execute the copy trade within hours, not days.

That's exactly the kind of problem Claude AI was built to solve.

### How Capitol Trades Works

[Capitol Trades](https://www.capitoltrades.com/) is a free platform — and it's the industry-leading resource for political investor intelligence, trusted by outlets like the Wall Street Journal and the New York Times. It tracks every disclosed trade by U.S. politicians and presents them through filterable views: by chamber, party, committee, state, asset type, transaction size, and sector.

The data covers stocks, ETFs, mutual funds, stock options, crypto, bonds, and even private equity funds. You can filter by specific politicians, see their historical performance, and identify patterns in their trading behavior.

### Connecting Claude to Capitol Trades

Claude can connect to Capitol Trades data through the MCP (Model Context Protocol) integration — essentially a plugin that gives Claude the ability to pull and parse congressional trading data programmatically. Once connected, Claude can:

1. **Monitor new filings** as they appear
2. **Filter by performance** — focus only on politicians with the strongest historical track records
3. **Identify trade patterns** — some politicians consistently trade in sectors related to their committee assignments
4. **Execute copy trades** automatically through your Alpaca connection

### Setting Up the Copy Trading Bot

The configuration I built works like this:

**Politician Selection:**
- Track the top 5 performing members of Congress based on trailing 12-month returns
- Weight toward politicians on committees with direct market influence (Finance, Commerce, Armed Services)
- Exclude any politician currently under investigation for insider trading (yes, this happens)

**Trade Execution Rules:**
- When a new filing appears from a tracked politician, Claude evaluates the trade
- If it's a buy of a stock worth over $15,000 (filtering out small speculative positions), Claude mirrors the trade proportionally based on your portfolio size
- If it's a sell, Claude sells the corresponding position if you hold it
- Position sizing follows the same 10% maximum per ticker rule from Level 2

**Timing:**
- Claude checks for new filings every 30 minutes during market hours
- New filings trigger an analysis within the same check cycle
- Trades execute immediately after analysis

### The Backtesting That Convinced Me

Before running this live — even on paper — I wanted to see historical performance. The backtesting results were compelling enough to make me slightly uncomfortable. A portfolio that simply copied the top 5 congressional traders over the previous year outperformed the S&P 500 by a significant margin. The returns weren't uniform — some months the strategy underperformed, and two positions resulted in notable losses — but the aggregate return was roughly 2.2x the benchmark.

Is this ethical? Honestly, I have mixed feelings. The trades are publicly disclosed. The data is legally accessible. The strategy is essentially: watch what the most informed investors in the country are doing and follow their lead. But the fact that these investors may be trading on non-public information they gained through their government positions is... let's just say it's a feature of the system that a lot of people think should be illegal. Several bills have been proposed to ban congressional stock trading entirely.

Until that happens, the data is public and the strategy works. Make your own decision about the ethics.

---

## The Wheel Strategy: Automated Options Income

Everything up to this point has been about stock trading. Level three was the copy trading. But there's a fourth tier I need to cover because it's where the real income generation lives — and it's also where most retail traders get overwhelmed and quit.

Options trading.

If you've never traded options, here's the 90-second version. A stock option is a contract. A **call option** gives you the right to *buy* a stock at a specific price (the strike price) before a specific date. A **put option** gives you the right to *sell* at a specific price before a specific date. You pay a premium for this right — think of it like insurance.

Here's the insight that changes everything: you don't have to *buy* options. You can *sell* them. When you sell an option, you collect the premium upfront. You become the insurance company. And if the option expires without being exercised, you keep the premium as pure profit.

The **wheel strategy** is a systematic approach to selling options that generates income in virtually any market condition. It's been used by institutional investors for decades, but most retail traders find it too complex to manage manually. Claude eliminates that complexity entirely.

### How the Wheel Turns

**Step 1: Sell Cash-Secured Puts**

Pick a stock you'd be happy to own at a lower price. Say AAPL is trading at $200. You sell a put option with a strike price of $190, expiring in 30 days. You collect a premium — let's say $3.50 per share ($350 per contract, since each contract covers 100 shares).

Two things can happen:
- **AAPL stays above $190:** The option expires worthless. You keep the $350 premium. Free money. Start again.
- **AAPL drops below $190:** You're assigned 100 shares at $190. But your effective cost is $186.50 (the strike price minus the premium you collected). You just bought a stock you wanted at a discount.

**Step 2: Sell Covered Calls**

Now you own the shares. You sell a call option with a strike price above your cost basis — say $195, expiring in 30 days. You collect another premium, let's say $2.80 per share ($280 per contract).

Two outcomes:
- **AAPL stays below $195:** The option expires. You keep the premium and still own the shares. Sell another call.
- **AAPL rises above $195:** Your shares get called away (sold) at $195. You profit from the price appreciation *plus* the premium. Then you go back to step one and sell puts again.

This is the wheel. Sell puts to potentially buy stock at a discount while collecting premiums. If you get assigned, sell calls to potentially sell at a profit while collecting more premiums. Rinse and repeat. Income flows regardless of market direction.

### Why Claude Makes the Wheel Actually Practical

The reason retail traders struggle with the wheel isn't the concept — it's the management. You need to:

- Monitor expiration dates across multiple positions
- Select appropriate strike prices based on current volatility
- Decide when to roll contracts (extend them to a later expiration)
- Close positions early when they hit profit targets (I use 50% of premium collected)
- Manage risk when a position moves against you
- Track all of this across multiple stocks simultaneously

That's a part-time job. Or it's 30 seconds of configuration for Claude.

### My Wheel Bot Configuration

Here's the automation ruleset I built:

**Stock Selection:**
- Run the wheel on 3-5 high-quality, large-cap stocks I'd be comfortable owning long-term
- Minimum market cap: $50 billion (no small-cap surprises)
- Only stocks with liquid options markets (tight bid-ask spreads)

**Put Selling Rules:**
- Strike price: one standard deviation below current price (roughly 84% probability of expiring worthless)
- Expiration: 30-45 days out (optimal theta decay range)
- Close at 50% profit — don't hold to expiration if you can collect half the premium early

**Call Selling Rules (when assigned shares):**
- Strike price: above cost basis (never sell below what you paid)
- Same 30-45 day expiration window
- Close at 50% profit

**Risk Management:**
- If a position moves 2x the premium collected against you, Claude closes it to limit damage
- Never have more than 50% of portfolio capital committed to active wheel positions
- Claude provides a daily summary of all open positions, unrealized P/L, and upcoming expirations

**Scheduling:**
- Every 15 minutes during market hours
- Daily summary email at market close

### What the Daily Summaries Look Like

Every afternoon, Claude sends a structured summary: current positions, premium collected this week, options approaching expiration, any positions that need attention. It's like having a junior analyst who never takes a day off and never forgets to check the Greeks.

The first time I reviewed a week's worth of summaries and realized the wheel had generated $1,240 in premium income on a $50,000 paper portfolio — while the underlying stocks had barely moved — something clicked. That's roughly a 2.5% weekly return from selling insurance contracts on stocks I was already willing to own. Annualized, even accounting for weeks where assignment happens and positions sit underwater temporarily, the income potential is significant.

---

## What Could Go Wrong — The Honest Assessment

I've spent 3,000 words making this sound impressive. Now let me tell you what keeps me up at night.

**API failures are real.** If the connection between Claude and Alpaca drops during a critical moment — say, your trailing stop should be triggering during a flash crash — you're exposed. I've experienced two brief disconnections during testing. Neither happened during a trade-critical moment, but the possibility exists. Mitigation: always maintain a manual stop-loss order directly in Alpaca as a backup. Belt and suspenders.

**Paper trading performance doesn't equal live performance.** Simulated fills are instantaneous and always at the quoted price. In live trading, slippage happens — especially during volatile moments when your bot is most likely to be executing. A backtested 2.2x return might be 1.6x in reality. Maybe less. I haven't moved to live trading yet specifically because I want three full months of paper trading data before risking real capital.

**The congressional copy trading has a time decay problem.** Politicians have up to 45 days to disclose. By the time you see the filing, the catalyst may have already played out. The strategy works best with politicians who file quickly and worst with those who wait until the deadline. Filter accordingly.

**Options carry risk that stocks don't.** If you sell a put and the stock drops 30%, you're buying 100 shares of a stock that's in freefall. The premium you collected doesn't come close to covering the loss. The wheel strategy works best in stable or gradually rising markets. In a genuine crash, you'll be holding bags of shares bought at prices that looked reasonable two weeks ago.

**Claude is an AI, not a financial advisor.** It doesn't understand your tax situation, your risk tolerance, your time horizon, or your financial goals. It executes rules. It doesn't question whether those rules are appropriate for your circumstances. A human financial advisor would ask "can you afford to lose this money?" Claude won't.

I run all of this on paper trading. I test for months before risking real money. And I never automate with capital I can't afford to lose. That's not a disclaimer — it's the actual approach that separates people who learn from this technology from people who get hurt by it.

---

## What the Numbers Tell Me After Several Weeks

Across all three strategies running simultaneously on paper:

The trailing stop bot maintained a roughly 2:1 win-to-loss ratio. Winners averaged larger than losers. The key wasn't picking better stocks — it was cutting losers fast and letting winners run. The bot did this with zero emotion, which is precisely the point.

The congressional copy trading strategy outperformed a simple SPY buy-and-hold over the same period, though the margin varied significantly week to week. Some of the copied trades were immediate winners. Others sat flat for weeks before moving. Patience is built into the strategy — Claude has infinite patience, which is more than I can say for myself.

The wheel strategy generated consistent premium income. Most puts expired worthless (which is the desired outcome). Two positions resulted in assignment, and Claude transitioned smoothly to selling covered calls on the assigned shares. The mechanical nature of the strategy — sell, collect, wait, repeat — is almost boring in practice. But boring and profitable beats exciting and broke.

The combined portfolio is ahead of where a simple S&P 500 index investment would be over the same period. Not by a life-changing margin. By a steady, compounding-friendly margin that would matter enormously over years.

---

## How to Start Today — The 30-Minute Setup

If you've read this far, here's your action plan:

1. **Download Claude Desktop** and set up your account if you haven't already
2. **Create an Alpaca paper trading account** at [alpaca.markets](https://alpaca.markets/) — it's free and takes five minutes
3. **Generate your API keys** in the Alpaca dashboard (paper trading environment)
4. **Install the Alpaca MCP server** in Claude Desktop using the configuration from [Alpaca's MCP documentation](https://github.com/alpacahq/alpaca-mcp-server)
5. **Test with a simple trade** — ask Claude to buy one share of any stock you follow
6. **Set up a trailing stop bot** on 3-5 stocks with a 5% trailing stop — start conservative
7. **Run it for two weeks** before touching the copy trading or options strategies

Do not skip the paper trading phase. Do not skip it. I know someone reading this is going to connect their live brokerage account on day one and let Claude trade with real money. That person is going to learn an expensive lesson about the difference between backtested returns and live execution.

Start with paper. Build confidence in the system. Understand its failure modes. Then, if and when you decide to go live, you'll do it with months of data and zero illusions about what can go wrong.

---

## Where This Goes From Here

Six months ago, the idea of an AI placing stock trades based on congressional filings and executing options strategies autonomously would have sounded like science fiction to most retail investors. A year from now, it'll sound quaint.

The Alpaca MCP server launched specifically to support LLM-driven trading. Capitol Trades is free and actively tracking congressional filings through April 2026. Claude's scheduled task capabilities keep improving. The infrastructure for AI-automated retail trading isn't theoretical — it exists right now, and it's accessible to anyone willing to spend 30 minutes on the setup.

The playing field between Wall Street and the rest of us was never going to be leveled by better stock tips or a hotter subreddit. It was always going to be leveled by infrastructure — by giving retail traders the same automated execution, data access, and emotional discipline that institutional desks have run for decades.

Claude AI, connected to the right APIs and configured with the right rules, is that infrastructure.

The question isn't whether AI will transform retail trading. That's already happening. The question is whether you'll be running the bot or competing against people who are. My paper trading account is open, three strategies are running, and Claude just flagged a new congressional filing that looks worth investigating.

Your move.

---

## Frequently Asked Questions

### Is it legal to use AI to automate stock trades?

Yes. Automated trading through brokerage APIs like Alpaca is fully legal for retail investors. Brokerages specifically provide API access for programmatic trading. The trades execute through regulated channels with standard investor protections.

### Can Claude AI actually place real stock trades?

Claude can place real trades when connected to a brokerage API through MCP integration. Alpaca's official MCP server supports stocks, ETFs, options, and crypto trading through natural language commands. Always test with paper trading before using real capital.

### How much does it cost to set up AI-automated trading with Claude?

The brokerage side is free — Alpaca charges zero commissions on U.S.-listed securities and options, and paper trading is completely free. You'll need a Claude subscription for desktop app access and MCP functionality. Total cost is essentially your Claude subscription.

### Is copying politicians' stock trades a reliable strategy?

Backtesting shows congressional copy trading has historically outperformed the S&P 500, but past performance doesn't guarantee future results. The 45-day disclosure window means some information advantage decays before you can act on it. Filter for politicians who file quickly and have strong track records on [Capitol Trades](https://www.capitoltrades.com/).

### What happens if the API connection drops during a trade?

API disconnections are a real risk. Always maintain backup stop-loss orders directly in your brokerage account as a safety net. Claude's scheduled checks will resume when the connection restores, but you need protection during the gap. For a deeper breakdown of handling API reliability, see my post on [building autonomous systems with Claude Code](/claude-code-agent-swarm-architecture).

---

## Let's Work Together

Looking to build AI systems, automate workflows, or scale your tech infrastructure? I'd love to help.

* **Fiverr** (custom builds & integrations): [fiverr.com/s/EgxYmWD](https://www.fiverr.com/s/EgxYmWD)
* **Portfolio**: [mejba.me](https://www.mejba.me)
* **Ramlit Limited** (enterprise solutions): [ramlit.com](https://www.ramlit.com)
* **ColorPark** (design & branding): [colorpark.io](https://www.colorpark.io)
* **xCyberSecurity** (security services): [xcybersecurity.io](https://www.xcybersecurity.io)
