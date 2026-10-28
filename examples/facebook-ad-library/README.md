# Facebook Ad Library keyword search

Search Facebook Ad Library through OpenAny and inspect the matching ads, request ID, and recorded cost. This example reproduces the inputs from a real call in the [Connect OpenAny conversation](https://chatgpt.com/s/cx_6ae21a76140c8191bbc763d98547ebd8).

## Recorded call

- Date: October 28, 2026, at 15:41:05.230 UTC.
- CLI version used: `0.2.0`. Install the latest version when reproducing the example.
- Provider: `scrapecreators`.
- API: **Facebook Ad Library — Search**.
- API ID: `52ed6918-8929-46c3-a507-f0982c7e6a04`.
- Inspected definition: revision `2`, method `GET`.
- Inputs: keyword `apple`, all countries, active ads, all ad types, sorted by impressions.
- Result: HTTP `200`, `body.success: true`, and 30 ads in one response page.
- Request ID: `c0b9e2ae-9ae1-4b62-a083-8167a3c9e798`.
- Recorded cost: `1880` micro-USD, equivalent to **$0.00188**.

The call record reports `status: "success"` and `chargedCostMicroUsd: "1880"`, but also `chargeStatus: "unknown"`. These values are preserved in the sample; it is a usage-record excerpt, not a payment or blockchain settlement receipt.

## Run the example

Use Node.js 22 or later and install the CLI in the environment where your agent runs:

```bash
npm install -g openany-cli@latest --registry=https://registry.npmjs.org/
openany --version
openany --help
```

Configure your own API key securely. If you already have one, run the following in your own terminal and enter the key at the hidden prompt:

```bash
openany setup --scenario call
```

If you need a key, follow [AUTH.md](https://openany.si/AUTH.md). Do not put credentials in these files, command arguments, or agent chats. Ordinary API calls do not require wallet or email login.

From the repository root, enter this example's directory and check configuration:

```bash
cd examples/facebook-ad-library
openany status --scenario call -j
```

Continue only when `configured` is `true` and `missing` is empty. A successful command exit alone does not prove the key is configured or valid. Check the balance and discover the API:

```bash
openany balance -j
openany discover -q "Ad Library" -l 20 -j
```

Select **Facebook Ad Library — Search** from **ScrapeCreators**, then inspect it. The following ID is the one used in the recorded call; confirm that the current catalog still identifies the same offering before using it:

```bash
API_ID='52ed6918-8929-46c3-a507-f0982c7e6a04'
openany inspect --id "$API_ID" -j
```

Check the current method, query schema, revision, and price against [query.json](query.json). The recorded price is historical. Proceed only if the current call fits your approved budget and the input contract still matches. If the offering is missing or incompatible, stop and resolve the change rather than silently switching providers.

The next command makes **one paid API call**, saves the response, and prints metadata including its request ID:

```bash
mkdir -p outputs
openany run --id "$API_ID" \
  --query "$(cat query.json)" \
  -j -o outputs/result.json
```

The CLI refuses to overwrite an existing output file. Inspect a previous result before choosing a fresh filename for an intentionally new call. Review `status` and `body.success`, count `body.searchResults`, and retain the returned `requestId`.

To look up the usage record for **your own call**, replace the placeholder below with its returned request ID:

```bash
openany calls get --id YOUR_REQUEST_ID -j
```

Call records are scoped to the API key used for the request. The historical request ID in this example is provenance, not a record your own key can necessarily access. On timeout or disconnection, use `calls list/get` to investigate before retrying; missing records do not establish that no call or charge occurred. Follow the [skill's recovery guidance](../../skills/openany/SKILL.md).

## Understand the result

The recorded response contained **30 active ads**, including Nike, Whatnot, and Amazon.com.br. None of those 30 listed Apple as the advertiser. A keyword search for `apple` can match ads linking to Apple's App Store; it does not establish that Apple ran those ads. Use a separately discovered and inspected advertiser-specific operation when that is the actual task.

This example retrieves one page only. It does not automatically follow cursors or make additional paid calls. Future results and ad availability may differ.

## Sample files and provenance

- [query.json](query.json): the exact query parameters used in the recorded call.
- [response.sample.json](response.sample.json): an excerpt containing three of the 30 returned ads and four fields per ad, plus the original response status and request ID. It is not the full response or a complete response schema.
- [receipt.sample.json](receipt.sample.json): selected fields from the original `openany calls get` output. Account, workspace, and API-key identifiers are omitted; the original status and recorded cost are retained.

The samples were extracted from the original conversation's command outputs and saved response. No new paid call was made to prepare this example. Provider credit balances, pagination cursors, ad creative content, and media URLs are excluded from the response excerpt. Live `outputs/` are ignored by Git; do not commit them without reviewing and sanitizing their contents.

The same workflow is shown in the [recorded demo](../../assets/facebook-ad-library-demo.gif).
