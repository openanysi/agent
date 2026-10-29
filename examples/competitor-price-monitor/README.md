# Track competitor product prices

Ask your agent to collect a price snapshot for competing products, then compare it with a later snapshot. This example searches US Google Shopping listings through Serper for Sony WH-1000XM5 and Bose QuietComfort Ultra Headphones.

## Try it

Copy this prompt into your agent:

```text
Read https://openany.si/SKILL.md, then use Serper Shopping through OpenAny to search US listings for "Sony WH-1000XM5 headphones" and "Bose QuietComfort Ultra Headphones", one page for each product.
```

Replace the product names and market with your own competitors. Save the returned listings as a dated snapshot. For a later comparison, provide that snapshot and ask:

```text
Read https://openany.si/SKILL.md, then refresh these product searches through OpenAny and compare prices with my attached snapshot, matching the same products and sellers with the same generation, condition, and currency.
```

This follow-up is a suggested next use; the recorded results below cover only the first snapshot.

## Actual result

Retrieved on **October 29, 2026, at 00:22 Asia/Shanghai** (October 28, 16:22 UTC). The Sony query returned **40 listings** and the Bose query returned **38 listings**. Selected results:

| Listing title | Seller | Displayed price |
| --- | --- | ---: |
| Sony WH-1000XM5 Noise Canceling Wireless Headphones | Best Buy | $301.99 |
| Sony Wh-1000XM5 Wireless Noise-Cancelling Over-the-Ear Headphones | Target | $149.99 |
| Bose QuietComfort Ultra Noise Wireless Cancelling Headphones | Bose | $249.00 |
| Bose QuietComfort Ultra Headphones | Best Buy | $349.99 |

These are returned listing prices, not verified checkout quotes. A US search filter does not guarantee every listing has the same currency, condition, warranty, or shipping terms. The response does not establish the condition or exact generation of every listing above; verify those details before comparing prices. In particular, the $149.99 result alone does not prove an equivalent new product is cheaper.

The same Bose response also included **2nd Gen refurbished headphones at $279.00**, **2nd Gen headphones at $459.00**, and **earbuds at $149.00**. The Sony response included a listing explicitly sold without a manufacturer warranty. These should not be merged into a single like-for-like price range.

This is the **first snapshot**. It contains no measured price changes and does not set up scheduled monitoring. Re-run the prompt with your saved snapshot when you want an updated comparison. Returned links, where present, lead to Google Shopping results rather than verified merchant checkout pages.

The recorded API cost was **$0.004 total**: two requests at **$0.002 each**. Product prices are separate from API charges. Future results and prices may change.

## Reference files

Each result excerpt preserves the first six listings, including product IDs and links where returned; the tables above select four of those listings.

- Sony: [request](sony-request.json), [result excerpt](sony-response.sample.json), [sanitized usage record](sony-receipt.sample.json)
- Bose: [request](bose-request.json), [result excerpt](bose-response.sample.json), [sanitized usage record](bose-receipt.sample.json)

These usage records show recorded API costs; they are not payment settlement receipts. Account and API-key identifiers are omitted.
