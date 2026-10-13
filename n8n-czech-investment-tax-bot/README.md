# CZ Tax Helper: Czech investment taxes in n8n + Telegram

A self-hosted [n8n](https://n8n.io) workflow that calculates **Czech personal income tax on investments** held at **Trading 212** and **Interactive Brokers**. You send the broker's CSV statement to a Telegram bot. The bot keeps your purchase lots, applies the Czech rules, and answers commands like "how much tax will I pay if I sell this today?".

No bank or broker API access. Your data stays in your own n8n instance.

<img width="514" height="420" alt="Screenshot 2026-10-12 194220" src="https://github.com/user-attachments/assets/088d8e15-6fc8-4dbb-83cd-5ee8648ade08" />
<img width="466" height="474" alt="Screenshot 2026-10-12 194238" src="https://github.com/user-attachments/assets/7aec7dea-47e1-42f3-a7ff-6341f3864c68" />



## What it does

**On every imported statement**
- Skips duplicate files and statements whose period overlaps one already imported
- Parses Trading 212 and IBKR Activity Statement CSVs, including FX fees, stock splits missing from the T212 export, and daily cash interest summed per month
- Matches every sell to purchase lots (**FIFO**) and runs the **3-year time test per lot** (časový test)
- Converts every amount at the **ČNB daily rate** of its own date. Rates are fetched once and cached.
- Tracks the **100 000 CZK yearly sales limit**
- Books dividends **gross**, with foreign withholding tax split out by country (from the ISIN)
- Writes everything at the very end, so a failed run writes nothing and the file can simply be resent

**Commands**

| Command | What you get |
|---|---|
| `/report` (or `/zvit`) | All years: realized gain, dividends, interest, tax estimate, P/L per ticker, open positions with the date each becomes tax-free |
| `/report 2026` | One year in detail with a 15 % tax estimate |
| `/sell NVDA 0.5 185 USD` | What-if: which lots FIFO would sell, gain, effect on the 100k limit, extra tax, and the date the sale becomes tax-free |
| `/sell VOO all 690` | Same, for the whole position |
| `/sell VOO free` | How many shares you can sell tax-free today |
| `/tax 2026` (or `/dap 2026`) | Numbers grouped the way the Czech tax return (DAP) asks for them: § 10 příjmy / výdaje, § 8 dividends per country with the credit capped at 15 %, interest |
| `/export 2026` | CSV with every sale, dividend, withholding and interest row of the year (Czech Excel format), as evidence |
| `/help` | Command list |

Replies are available in **English, Czech or Ukrainian** (one setting).

## Requirements

- Self-hosted n8n, recent enough to have **Data Tables**
- A Telegram bot token from [@BotFather](https://t.me/BotFather)
- Your numeric Telegram user ID (send any message to [@userinfobot](https://t.me/userinfobot))

## Setup

### 1. Bot token as an environment variable
The file download step reads the token from `TELEGRAM_BOT_TOKEN`. Add it to the `environment:` section of your n8n service in `docker-compose.yml`:

```yaml
    environment:
      - TELEGRAM_BOT_TOKEN=123456:ABC...
```
Then run `docker compose up -d`.

### 2. Create six Data Tables
In n8n, open **Data tables → Create** and add these columns. Names must match exactly.

| Table | Columns (type) |
|---|---|
| `tax_lots` | broker (string), ident (string), ticker (string), purchase_date (string), qty_remaining (number), cost_native (number), currency (string) |
| `tax_sales` | year (number), broker (string), ticker (string), sell_date (string), purchase_date (string), qty (number), cost_czk (number), proceeds_czk (number), time_test_passed (boolean) |
| `tax_income` | year (number), broker (string), type (string), ticker (string), isin (string), country (string), date (string), amount (number), currency (string), amount_czk (number), note (string) |
| `tax_ytd` | year (number), sales_revenue_czk (number), dividend_czk (number), withholding_czk (number), interest_czk (number) |
| `cnb_rate_cache` | date (string), currency (string), rate (number) |
| `FILE_UNIQUE_CZ_TAX` | file_unique_id (string), broker (string), period_from (string), period_to (string) |

### 3. Import the workflow
**Workflows → Create → ⋯ → Import from File** → `cz-tax-helper.workflow.json`

### 4. Connect the pieces
- **Telegram credential**: create one with your bot token and select it in every Telegram node (Telegram Trigger, Get a file, Notify period overlap, Send report, Send a document, Send import summary).
- **Data Tables**: open every Data Table node (the ones named *Get…*, *Save…*, *Delete…*, *Report: get…*, *Check processed files*, *Mark file processed*) and pick the table shown in its name.
- **Config node**:
  - `telegram_user_id`: your numeric Telegram ID. The bot ignores everyone else.
  - `lang`: `en`, `cs` or `uk`.

### 5. Activate
Turn the workflow **Active**, then send `/help` to your bot.

## Usage

1. **Export statements**
   - **Trading 212**: History → Export → CSV. **Don't rename the file**: the period is read from its name (`from_YYYY-MM-DD_to_YYYY-MM-DD`).
   - **IBKR**: Performance & Reports → Statements → Activity Statement → CSV.
2. **Send them to the bot one at a time, oldest first.** Wait for each reply. FIFO needs to know a purchase before it sees the sale.
3. **Make periods follow each other per broker**, with no gaps and no overlaps. The bot refuses overlapping periods.
4. **Check the import summary.** If it lists **"Rows the bot did not process"** (spin-offs, transfers, options…), handle those manually.

## How the tax logic works

- **FIFO per broker and instrument.** T212 uses the ISIN, IBKR the symbol. Buys on the same day merge into one lot.
- **Cost** = purchase price incl. fees × ČNB rate on the purchase date, + sell fees × rate on the sale date.
- **Proceeds** = sale amount × ČNB rate on the sale date.
- **3-year test**: a lot held ≥ 3 years is exempt.
- **100k limit**: if total sales proceeds in a year are ≤ 100 000 CZK, all gains of that year are exempt. If above, every gain not covered by the 3-year test is taxable. Losses offset gains within the same year only.
- **Dividends**: gross, 15 % Czech rate, foreign withholding credited up to 15 % per country.
- **Interest** (T212 cash interest, IBKR interest): 15 %, nothing withheld.
- Every event is booked to the year of its own date. IBKR withholding corrections for the previous year go to the previous year.

### Known limitations
- Stocks and ETFs only: no options, futures or bonds.
- Corporate actions other than splits (spin-offs, mergers) and transfers between brokers must be handled manually.
- T212 splits that are missing from the export must be added to `MISSING_SPLITS` in the *Parse Import* node. NVIDIA 10:1 (2024) is already there.
- Currencies cached: EUR, USD and GBP. Add more in *Parse CNB response*.

## Disclaimer

This is a personal tool, **not tax advice**. The numbers are estimates. Check where each figure goes in your return against the official *Pokyny k vyplnění* for the given year, or ask a daňový poradce.

## License

MIT
