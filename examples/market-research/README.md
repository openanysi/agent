# Compare search interest for a market idea

Ask your agent to compare interest in two product categories before deciding what to research next. This example compares US searches for wireless earbuds and noise cancelling headphones using Google Trends through DataForSEO.

## Try it

If needed, connect your agent first:

```text
Read openany.si/AGENT.md and connect OpenAny
```

Then copy this prompt:

```text
Use OpenAny's DataForSEO Google Trends API to compare "wireless earbuds"
and "noise cancelling headphones" in the United States over the past 12 months.
Use web search interest. Show the average interest index for each term,
the peak periods, and any missing or incomplete data.
Explain what the comparison tells me and what it cannot tell me about demand.
Include the source link, observation time, and recorded API cost.
Confirm the price with me before making the request.
```

Replace the keywords, country, or time range with the market you want to explore.

## Actual result

Retrieved on **October 29, 2026, at 00:22 Asia/Shanghai** (October 28, 16:22 UTC). The response contained **53 time periods**, with these provider-reported averages and peaks among periods not marked as missing:

| Search term | Average interest index | Peak interest index | Peak period |
| --- | ---: | ---: | --- |
| wireless earbuds | 23 | 100 | April 12–18, 2026 |
| noise cancelling headphones | 9 | 31 | August 9–15, 2026 |

In this query, wireless earbuds had a higher average search-interest index. The two terms peaked in different weeks. This is a starting point for researching audience interest; it does not establish which category generates more revenue.

These are relative interest indices, not search counts, sales, or market share. The final period, October 25–28, was marked `missing_data: true`; do not treat it as a complete week or use it to claim a recent decline. The averages above are returned by the provider, not recalculated after removing that period.

[Explore these terms on Google Trends](https://trends.google.com/trends/explore?hl=en&geo=US&date=today%2012-m&q=wireless%20earbuds,noise%20cancelling%20headphones). This live link uses a rolling time range; the saved response preserves the historical result.

The recorded API cost was **$0.011** for one request. Future results and prices may change.

## Reference files

- [Request parameters](request.json)
- [Result excerpt with all 53 graph periods](response.sample.json)
- [Sanitized usage record](receipt.sample.json)

The usage record retains `chargeStatus: "unknown"` alongside the recorded cost; it is not a payment settlement receipt. Account and API-key identifiers are omitted.
