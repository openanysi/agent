# OpenAny Agent Integration

OpenAny CLI discovers and calls model, data, and tool APIs, and manages accounts, API keys, and top-ups. Use this document as a quick start and [SKILL.md](https://openany.si/SKILL.md) for complete command, authorization, and recovery instructions.

Use the latest published CLI with Node.js **>=22**. The backend and signing service use **https://openany.si**; the default API gateway is **https://api.openany.si**. Model execution uses the actual HTTPS URL in the selected catalog `curlExample`, including legacy or development domains. Do not rewrite that URL or execute the curl text in a shell. There is no command-line origin override.

## Entry points

- Integration skill: https://openany.si/SKILL.md
- Authentication and key management: https://openany.si/AUTH.md
- Client setup recipes: https://openany.si/docs/agents.json
- Document index: https://openany.si/llms.txt
- Human-readable guide: https://openany.si/docs
- Model browsing: https://openany.si/models
- API browsing: https://openany.si/apis
- Usage records: https://openany.si/?openDash=1&page=usage

Website listings help with browsing. Use the current CLI catalog and API definitions for executable IDs, operations, parameters, prices, and availability. A listing or an installed skill alone does not verify access.

## 1. Install and configure

Prefer a tool the user specified or an appropriate dedicated tool already available. When using OpenAny, review SKILL.md and install it in the client's supported skill directory using the matching client recipe. Keep existing client settings and unrelated instructions.

Before guiding or executing login and key creation, you **must read** SKILL.md's **Missing Configuration: Required User Guidance** and **Interactive Configuration Walkthrough**, together with AUTH.md. Do not stop at listing environment variable names. If the user has no API key yet, follow AUTH.md: for a first-time wallet, create a key to register the account automatically before login; for email, log in before creating a key. If a key already belongs to the configured wallet, reuse it and log in directly without creating another key; `setup --scenario call` only saves an existing key. Ask which login method to use only if the user has not already chosen one.

For missing configuration, give the relevant command for the user's own terminal on the same machine/account where the CLI runs:

- Wallet: `openany setup --scenario wallet`. Explain that the user enters the private key at the hidden prompt, the CLI derives and displays the address automatically, and the user enters a stable Agent ID such as `agent-main` (6–128 letters, digits, dots, underscores, colons, or hyphens).
- Email: `openany setup --scenario email`. Enter only the email address; setup requests no password or verification code and does not log in.

Use interactive setup by default, not instructions to write secrets into `~/.zshrc`, shell profiles, or `.env` files. Reuse an already authorized secure secret-injection mechanism or external signer when available, following AUTH.md. Never ask for API keys, private keys, seed phrases, passwords, verification codes, tokens, cookies, or credentials file contents in any AI/model chat, screenshot, or attachment. Setup saves locally to `~/.config/openany/credentials.json` (or `OPENANY_CONFIG_DIR`), with directory 0700/file 0600 permissions and no additional encryption. It does not log in, create keys, or authorize payments. Ask the user to reply only that configuration is complete, then check the chosen scenario with `openany status --scenario wallet -j` or `openany status --scenario email -j` before continuing within existing authorization. If settings are still missing, check that user and agent use the same execution environment and configuration directory without requesting secret values.

Install the latest published CLI in the agent's execution environment:

```bash
npm install -g openany-cli@latest --registry=https://registry.npmjs.org/
openany --version
openany --help
```

Ordinary model, data, and tool calls require only `OPENANY_API_KEY` or the current key saved in the CLI. They do not require wallet login, email login, or an agent ID. If credentials are known to be missing, ask for secure configuration before actual execution and explain the installation requirement at the same time if needed. Public catalog browsing and `models run --dry-run` may precede configuration.

If configuration status is unknown, run `openany status --scenario call -j`. Check `configured` and `missing`; exit code 0 only means the local check completed. Use credentials supplied or explicitly designated by the user. Never scan repositories, `.env*` files, user directories, or credential stores, or ask for keys in chat. Environment variable `OPENANY_API_KEY` takes precedence over the current local key. See AUTH.md to save an existing key or manage keys with wallet/email login.

If a required key is missing, pause execution requests until configured; explicit public catalog browsing and `models run --dry-run` may proceed without a key. If running unattended and execution requires missing configuration, report the missing items and end the run. On an API-key 401, pause and ask the user to check that key. For email-session errors, follow AUTH.md's explicit refresh and recovery instructions. Do not switch authentication methods or service domains.

For explicit public catalog browsing, use `discover/collections/inspect --public` or `models prices/playgrounds/parameters/examples --public` without a key. `models run --dry-run` also needs no key: it reads the public catalog and previews the request without checking balance or executing a model. Complete credential configuration before actual execution.

## 2. Discover and call through the CLI

Make all OpenAny API calls through the CLI so its configuration checks and request validation apply. Stay within the user's purpose and budget; ask when either is missing or insufficient.

For data and tool APIs, check the balance, discover a resource, inspect its definition, then call it:

```bash
openany balance -j
openany discover -q "Get hot search data" -l 10 -j
openany inspect --id API_ID -j
openany run --id API_ID --query '{"limit":"10"}' -j
```

Replace `API_ID` and example parameters with values from the current catalog and definition. Confirm the method, input schema, required parameters, price, and version. If multiple APIs match, ask the user to confirm the platform, provider, or purpose. Apply the authorized low-balance policy in SKILL.md when applicable. Otherwise, an insufficient balance requires top-up authorization; wallet registration uses the existing onboarding authorization described in AUTH.md, without requesting the same approval again. Do not bypass CLI validation when a definition is incompatible.

Website capability pages group provider offerings. Select a concrete offering and pass its API ID to `inspect` and `run`; capability keys, capability IDs, and collection IDs cannot replace it. Check each offering's own contract and do not substitute providers without authorization.

For models, use `openany models examples --model MODEL --public -j` to read the current request templates, providers, and example IDs. Select `--provider PROVIDER` and `--example EXAMPLE_ID` when multiple choices exist; do not execute all examples as a batch. Supply the complete desired request body with `-f input.json`, including `model` unless the URL binds it. Preview with `models run --model MODEL --provider PROVIDER --example EXAMPLE_ID -f input.json --dry-run`, then remove `--dry-run` to execute within the authorized scope. Do not pass `--protocol` or `--mode` to `models run`; `models parameters` is informational, not an execution prerequisite. Follow SKILL.md for provider routing, attachments, output files, and unsupported media operations.

Data and tool calls return synchronously. For model tasks, use a catalog query example with `--path '{"task_id":"ACTUAL_TASK_ID"}'` only when that template declares `{task_id}` and the service returned the task ID. The CLI does not automatically poll, retry, download generated videos, or stop tasks. Streaming output is returned after the stream finishes; an HTTP 200, task creation response, or partial stream alone does not prove generation is complete.

## 3. Authentication and authorized top-ups

Use AUTH.md for wallet/email login and key management. First-time wallets create a key to register automatically before login; wallets with an existing matching key log in directly. Email sessions are saved locally; refresh them explicitly with `openany auth email refresh -j` when needed. Authentication and top-up endpoints depend on deployment configuration, so report service unavailability separately from missing local credentials. Login does not authorize paid calls or top-ups. Key rotation and revocation must stay within explicit user authorization.

For explicitly authorized autonomous top-ups, follow SKILL.md: configure the target identity and authorized signing capability, log in, and ensure the API key belongs to the target. Record the approved network, per-payment limit, total limit, purpose, and expiration using `topups authorize`, then check `openany status --scenario payment --identity wallet --scope TASK_SCOPE -j` (use email for email identity). After configuration is complete, use `topups create`, `topups pay`, and `topups get`. The authorize command records permission already given; it does not grant permission.

When approval is missing, follow **Explain How to Enable Top-Ups** in SKILL.md: provide a numbered beginner walkthrough, a copyable natural-language approval template, and exact local setup steps for missing settings. Explain what the user should reply, what the agent will do next, and how to get help if the account type or payment network is unknown. A bare request for limits/network/purpose/expiration is insufficient; do not infer payment permission from a request for setup help.

When an agent observes an available balance below 5 USD, the authorized policy uses `--amount-usd 10` within the approved network, fee-inclusive limits, purpose, and expiration. Exactly 5 USD does not trigger it. The input amount does not guarantee 10 USD net credit; payment, fees, and credit follow the locked quote. The CLI has no background balance monitor. Follow SKILL.md for the full policy and resolve pending orders before creating another.

The CLI funds shared balance through x402; resource calls still use an API key. Direct x402 resource payments are outside this CLI workflow and require a separately verified resource contract.

Retain the intent and returned order ID, and keep the scope, amount, and network unchanged for the same top-up. The CLI persists and reuses its idempotency key; do not extract it from the credentials file. For an unclear creation outcome, wait for the request to finish and use the original parameters with `--resume`. After payment is submitted, times out, or has an unknown outcome, only check `topups get --intent NAME`; do not pay again or change the idempotency key. Confirm `paymentStatus=paid` and `creditStatus=credited` before reporting funds credited; wallet identity also requires `accountUsable=true`. See SKILL.md for network support, fee-inclusive limits, locked quotes, and external signing.

## 4. Recover and report actual results

After a model, data, or tool call times out or disconnects, retain its request ID and inspect `openany calls list --limit 20 -j` and `openany calls get --id REQUEST_ID -j` using the same API key. Backends with unified usage-call support include model and data/tool records; older backends exclude model records, and updating the CLI alone does not change that. For model records unavailable through these commands, check the account dashboard or ask support to reconcile the request. `usageType` distinguishes `model` from `api`; `chargedCostMicroUsd` is the recorded per-row charge in micro-USD. Use `--status success|failed`; `--review-required` is unsupported. Lists default to seven days with a maximum 31-day explicit range; detail queries are independent of that window. Missing records do not prove that a call was not executed or charged; do not automatically repeat an uncertain paid request.

On failure, inspect the nonzero exit code and JSON error on stderr. Retain the error code and request ID without exposing secrets. Explain configuration, catalog, balance, authentication, and business-call failures separately. If login or key management succeeded remotely but failed to save locally, recover the original result using `openany auth recover --file RETURNED_PATH` with the verified recovery file. Follow SKILL.md for other recovery cases; do not automatically delete configuration locks.

Report the actual result, resource ID, request ID, and usage or cost when returned. Do not invent prices, receipts, routes, or successful payments. A balance query verifies only balance access, not signing, email connectivity, or payment authorization. If no real call was made, report "Configured; live verification pending" and the remaining requirements.
