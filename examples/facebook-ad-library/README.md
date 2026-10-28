# Search Facebook Ad Library

Ask your agent to find active Facebook ads matching a keyword and summarize the advertisers and ad links. Use this to explore messaging around a product, topic, or brand name.

## Try it

If OpenAny is not connected yet, send this to your agent first and follow its setup guidance:

```text
Read openany.si/AGENT.md and connect OpenAny
```

Then copy this prompt:

```text
Use OpenAny to search Facebook Ad Library for the keyword "apple".
Find active ads across all countries and return one page of results.
Summarize the advertiser names and include links to the original ads.
Confirm the price with me before searching, and include the recorded cost in your answer.
```

Replace `apple` with your own keyword. You can also ask for a specific country or a different ad status. If a requested filter is unavailable, ask your agent to explain the limitation.

## What you will get

Your agent will summarize the matching ads, link to the originals, and report the recorded cost when available.

In the [recorded session](https://chatgpt.com/s/cx_6ae21a76140c8191bbc763d98547ebd8), a search for `apple` returned **30 active ads**, including:

- [Nike](https://www.facebook.com/ads/library?id=1702938977100376)
- [Whatnot](https://www.facebook.com/ads/library?id=1555263375851429)
- [Amazon.com.br](https://www.facebook.com/ads/library?id=1549375876128927)

The recorded cost was **$0.00188** on October 28, 2026. This is a historical example; your results and price may differ.

**A keyword match does not mean the named brand is the advertiser.** None of those 30 ads listed Apple as the advertiser; some matched through links to Apple's App Store. Check the advertiser name before drawing conclusions about a brand's campaigns.

## Watch the example

![Facebook Ad Library search through OpenAny](../../assets/facebook-ad-library-demo.gif)

## Reference files

For reference, the original session's [query parameters](query.json), [three-ad result excerpt](response.sample.json), and [sanitized usage record](receipt.sample.json) are included. You do not need these files to use the prompt above.

The usage record retains the original `chargeStatus: "unknown"` alongside its recorded cost; it is not a payment settlement receipt. Account and API-key identifiers are omitted. These files document the original call, not a new execution of the prompt above.
