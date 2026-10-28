# Track competitor product prices

Ask your agent to collect a price snapshot for competing products, then compare it with a later snapshot. This example searches US Google Shopping listings through Serper for Sony WH-1000XM5 and Bose QuietComfort Ultra Headphones.

## Try it

If needed, connect your agent first:

```text
Read openany.si/AGENT.md and connect OpenAny
```

Then copy this prompt:

```text
Use OpenAny's Serper Shopping API to search US listings for
"Sony WH-1000XM5 headphones" and "Bose QuietComfort Ultra Headphones".
Retrieve one page for each product. Show the listing title, seller,
displayed price, product ID, and link when available.
Flag refurbished items, bundles, different generations, earbuds,
and unclear condition instead of treating every result as equivalent.
Save a dated price snapshot so I can compare the same listings later.
Include the recorded API cost, and confirm the total price before searching.
```

Replace the product names and market with your own competitors. For a later comparison, provide your saved snapshot and ask:

```text
Refresh these product searches through OpenAny and compare them with my
attached price snapshot. Match product IDs and sellers, verify model,
generation, condition, and currency, and show absolute and percentage
price changes only for comparable listings. Mark new, missing, or ambiguous
listings separately. Confirm the price before making new requests.
```

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

Both usage records retain `chargeStatus: "unknown"` alongside their recorded costs; they are not payment settlement receipts. Account and API-key identifiers are omitted.
