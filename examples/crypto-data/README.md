# Look up cryptocurrency prices

Ask your agent for a timestamped price snapshot of Bitcoin, Ethereum, and Solana using CoinGecko through OpenAny.

## Try it

Copy this prompt into your agent:

```text
Read https://openany.si/SKILL.md, then use CoinGecko through OpenAny to get USD prices for bitcoin, ethereum, and solana, including 24-hour percentage changes and last updated times.
```

Replace the coin IDs or quote currency to query the assets you follow. Ask your agent to verify unfamiliar coin IDs before querying.

## Actual result

Retrieved on **October 29, 2026, at 00:23 Asia/Shanghai** (October 28, 16:23 UTC):

| Coin | Price (USD) | 24-hour change | Price last updated (UTC) |
| --- | ---: | ---: | --- |
| Bitcoin (`bitcoin`) | $80,423.00 | -0.0552% | 2026-10-28 16:22:40 |
| Ethereum (`ethereum`) | $2,329.47 | -0.6180% | 2026-10-28 16:22:51 |
| Solana (`solana`) | $100.83 | +0.0354% | 2026-10-28 16:22:51 |

Changes are rounded to four decimal places for display; the saved response preserves the original precision. The last-updated times come from CoinGecko and differ from the request time. These are historical market data, not current prices or guaranteed execution quotes.

The recorded API cost was **$0.00** for one request containing all three coin IDs. Check the current price before future calls.

## Reference files

- [Query parameters](query.json)
- [Actual price response](response.sample.json)
- [Sanitized usage record](receipt.sample.json)

This usage record shows the recorded API cost; it is not a payment settlement receipt. Account and API-key identifiers are omitted.
