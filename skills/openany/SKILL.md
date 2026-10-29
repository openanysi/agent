---
name: openany
description: >-
  Use when the user needs to discover and call model, data, or tool APIs through OpenAny, check balances and call records, or manage wallet login, email login, API keys, and top-ups.
metadata:
  cli-channel: "latest"
---

# OpenAny CLI

OpenAny CLI discovers and calls model, data, and tool APIs, and manages accounts, API keys, and top-ups. Use the latest published OpenAny CLI with this skill. Node.js >=22 is required.

Prefer a tool the user has specified or an appropriate dedicated tool that is already available. When operating through OpenAny, stay within the purpose and budget authorized by the user.

The backend and signing service use **https://openany.si**; the default gateway is **https://api.openany.si**. Model execution uses the actual HTTPS URL published in the selected catalog `curlExample`, including legacy or development domains. Do not rewrite that URL or execute the curl text in a shell. There is no command-line origin override.

The CLI uses API keys and shared-balance billing for resource calls. Its x402 workflow tops up that balance; it does not implement direct x402 payment for an individual API request. Separate x402 resources must be evaluated under their own published contracts, outside this CLI workflow. Authentication and top-up availability also depend on the deployed service configuration; local configuration checks do not prove those services are enabled.

## Preflight Checks

For actual model, data, or tool execution, use scenario A below; only an API key is required. Public catalog browsing and `models run --dry-run` are exempt from these credential checks and can precede configuration.

1. If required credentials or local settings are missing, follow **Missing Configuration: Required User Guidance** below. Explain the exact terminal steps and explicitly warn against sending sensitive information in any AI/model conversation; ask the user to configure locally and confirm completion, never to supply secret values in chat. If tools or authorization details are missing, explain what is needed and request only the relevant connection or authorization. Missing CLI-managed login sessions or payment policies can be established within existing authorization using the login and Authorized Top-Ups steps below; do not ask the user to edit the credentials file.
2. If configuration status is unknown, run one local check: `openany status --scenario call -j`. Use `configured` and `missing` to determine whether all required settings are present. Exit code 0 means the check completed; subsequent requests must still verify that the configuration works.
3. If the API key is missing, pause execution requests. Explicit public catalog browsing and `models run --dry-run` may proceed without a key. If the user already has a key, guide them through `openany setup --scenario call` in their own terminal; otherwise, follow the wallet/email login and key-creation flow below within their authorization. Use only credentials configured or explicitly designated by the user through an authorized secure mechanism. Do not scan repositories, `.env*` files, user directories, or credential stores, and do not send invalid keys as a test. If running unattended and configuration cannot be obtained, record the missing setting names without values and end the run.
4. Once configuration is complete, reuse it to check the balance and make calls. On a 401 from an API-key-authenticated request, pause and ask the user to check that key. For an email-session 401, use the explicit refresh and recovery steps below. Keep the original authentication method and service URL.

### Missing Configuration: Required User Guidance

Whenever required configuration is missing, the response to the user **must** include all of the following, in the user's language. A bare variable name, a bare `setup` command, or "configure your credentials" is insufficient.

For wallet/email login and key creation, apply this guidance before authentication, even when the user started from AGENT.md or AUTH.md. You must use interactive setup; do not substitute instructions to export private keys or write secrets into `~/.zshrc`, other shell profiles, or `.env` files. Ask which login method to use only if the user has not already chosen one.

1. State the missing setting names and why they are needed for this task. Request only the applicable scenario; ordinary API calls do not require wallet or email setup.
2. Give the exact interactive command from the configuration walkthrough below, explain each input in order, and say to run it in the user's own terminal on the machine/account where the CLI will be used. If the CLI is absent, include the installation command from this skill first. Do not run a hidden-input wizard on the user's behalf and then ask them to send you its answers.
3. Explicitly remind the user: **Do not submit API keys, wallet private keys, seed phrases, passwords, verification codes, access/refresh tokens, cookies, or credentials file contents in any AI/model conversation, including this chat, screenshots, or attachments.** API keys and private keys belong only in the CLI's hidden terminal prompts or an already authorized secure secret-injection mechanism. Email setup requires only an email address, never an email password. If the user offers to send a secret, redirect them to local configuration without requesting or repeating it.
4. Explain that setup saves locally to `~/.config/openany/credentials.json` (or `OPENANY_CONFIG_DIR`), using the existing directory 0700/file 0600 permissions. Do not ask the user to open, print, edit, or upload that file. Setup does not log in or authorize payments.
5. Ask the user to reply only that configuration is complete. Then rerun the relevant `openany status --scenario SCENARIO -j` check in the CLI's execution environment. Inspect `configured` and `missing`; do not rely on exit code 0 alone or treat a local check as proof of login, key validity, or payment permission. If settings are still missing, explain those names and the next local step without asking for secret values. Nonempty environment variables override local values; if `environmentOverrides` is reported, explain the precedence without requesting variable values.

Example response for a missing API key (translate to the user's language):

> OpenAny is missing an API key for this request. In your own terminal, run `openany setup --scenario call`. Enter a key name such as `main`, enter the API key at the hidden prompt, and answer `y` to make it the current key. The CLI saves it locally in `~/.config/openany/credentials.json` with file permission 0600 and directory permission 0700. Do not send API keys, wallet private keys, seed phrases, passwords, verification codes, tokens, cookies, or the credentials file in this or any other AI/model chat, including screenshots or attachments. When finished, reply only "configured"; I will check with `openany status --scenario call -j` before continuing. Setup only saves configuration and does not log in or authorize payments.

Example response after the user selects wallet login and authorizes login and key creation, with wallet settings missing (translate to the user's language):

> The core settings required for wallet login are not configured yet. In your own terminal on the same machine/account where OpenAny runs, execute `openany setup --scenario wallet` to complete configuration. Enter the private key at the hidden prompt; the CLI derives and displays your wallet address automatically. Then enter a stable Agent ID such as `agent-main`, using 6–128 letters, digits, dots, underscores, colons, or hyphens, and keep it for later use. Setup saves locally to `~/.config/openany/credentials.json` with directory 0700/file 0600 permissions. Do not send private keys, seed phrases, API keys, passwords, verification codes, tokens, cookies, or the credentials file in any AI/model chat, screenshot, or attachment. Reply only "configured" when finished. I will run `openany status --scenario wallet -j`; once it reports configured with nothing missing, I will follow the wallet flow under your existing authorization: for a first-time wallet without a key, run `openany keys create --identity wallet --label agent-main -j` to register the account automatically, then run `openany auth wallet -j` after the key is saved. If you already have a key for this wallet, I will reuse it and log in directly without creating another key. The generated key is saved locally; `keys list` shows only metadata, not its value. Setup alone does not log in, create keys, or authorize payments.

If settings remain missing after the user's confirmation, verify that the user and agent are using the same execution environment and configuration directory without reading or requesting secret values. Do not treat configuration on the user's laptop as configuration in a separate agent runtime.

If the user does not have an API key yet, explain that setup saves an existing key and cannot issue one. Offer the wallet or email login and key-creation flow below within the user's authorization; those commands save the generated key locally without requiring the user to paste it into chat.

When explaining check results, report only what was actually checked. Ordinary API calls do not require a wallet, email address, service URL, or agent ID.

**Browsing the public catalog only:** If the user explicitly wants only to browse the catalog without making paid calls, use `discover/collections/inspect --public` or `models prices/playgrounds/parameters/examples --public` without an API key. `models run --dry-run` also needs no key: it only reads the public catalog and previews a request; it never checks balance or executes a model. Complete credential configuration before actual execution; public discovery and dry-run may happen first.

## Installation and Configuration Scenarios

Install or update to the latest published CLI through npm to make the global `openany` command available. Use the npm `latest` release channel instead of a fixed version:

```bash
npm install -g openany-cli@latest --registry=https://registry.npmjs.org/
openany --version
openany --help
```

If credentials are known to be missing, ask for configuration before actual execution; public browsing and dry-run may happen first. If the CLI is also missing, explain the installation requirement at the same time. Do not require a specific numeric CLI version. Use `openany --version` to report the installed version and `openany --help` to check available commands. If an installed CLI lacks a documented command or option, update it with the command above and check again before proceeding; do not assume that installation alone verifies API access. Make all API calls through the CLI so that configuration checks and call validation apply.

Choose the configuration scenario for the task, reuse existing settings, and supply only missing items:

| Scenario | Ask the user to provide or authorize | Local check |
| --- | --- | --- |
| A. Model, data, and tool calls only | `OPENANY_API_KEY`, or the current key saved in the CLI | `openany status --scenario call -j` |
| B. Autonomous top-ups | A + target identity (wallet/email) + payment wallet address, private key or authorized signing tool + network, per-payment limit, total limit, scope, and expiration; log in using C/D and record existing approval with `topups authorize` | `openany status --scenario payment --identity wallet --scope TASK_SCOPE -j` (use email for email identity) |
| C. Create or rotate keys with a wallet | `OPENANY_WALLET_ADDRESS`, `OPENANY_WALLET_PRIVATE_KEY` or an authorized signing tool, and a stable `OPENANY_AGENT_ID` | `openany status --scenario wallet -j` |
| D. Manage keys with email login | `OPENANY_EMAIL` and authorized access to that mailbox; select the target if there are multiple workspaces | `openany status --scenario email -j` |

Store private keys and API keys using interactive `openany setup` or the runtime's secure credential settings (secrets), never in chat or command-line arguments. Access email through an email tool already connected and authorized for the agent; the CLI does not need an email password. When using an existing external signing tool outside setup, add `--prepare` to configuration check commands; a local private key is not required. Before execution, still confirm that the signing tool is available and authorized.

Wallet private key formats: EVM uses a 32-byte hexadecimal key with or without a `0x` prefix; Solana uses a Base58-encoded 64-byte secret key. The CLI verifies that the address matches the private key. Use a stable agent ID supplied by the user or runtime, and keep it consistent across subsequent calls.

### Interactive Configuration Walkthrough

Give the relevant row as an ordered walkthrough, together with the mandatory sensitive-information warning above. `openany setup` also offers a menu: 1 for API calls, 2 for wallet, and 3 for email.

| Needed configuration | Command in the user's terminal | Inputs in order | Check after the user confirms completion |
| --- | --- | --- | --- |
| API calls | `openany setup --scenario call` | Enter a key name (default `main` or the current key label); enter the API key at the hidden prompt; answer `y` to activate it for subsequent calls. | `openany status --scenario call -j` |
| Wallet | `openany setup --scenario wallet` | Enter the private key at the hidden prompt; the CLI derives and displays the wallet address automatically; enter a stable Agent ID of 6–128 letters, digits, dots, underscores, colons, or hyphens, and keep using that ID. Setup offers no external-signing option. | `openany status --scenario wallet -j` |
| Email | `openany setup --scenario email` | Enter the email address only; no password or verification code is requested during setup. Saving the address does not log in. | `openany status --scenario email -j` |

Setup only saves local configuration. It does not log in, send email, create keys, authorize top-ups, or submit payments. Its result includes the saved path, status, next commands, and names of environment variables overriding the saved values. When login is needed, follow Wallet Login and API Key Management below (first-time wallets create a key before login), or use `auth email send` followed by `auth email verify`.

For agents and scripts, prefer `status --scenario SCENARIO -j`. `setup -j`, `setup --non-interactive`, setup without a TTY, `setup --prepare`, and `setup --scenario payment` remain read-only checks and never prompt or save. An interactive setup must not be used as a passive readiness check.

### Save an Existing Key

`OPENANY_API_KEY` in the environment takes precedence over the current local key. You can also save a key from a securely injected environment variable:

```bash
openany keys add --label main --activate
openany keys list -j
```

If the environment variable is not set, enter the key with hidden input in an interactive terminal. For unattended use, pass a secret through controlled standard input: `openany keys add --label main --activate --stdin`. Never include a plaintext key in command text.

Local configuration is stored at `~/.config/openany/credentials.json`, with existing file permissions 0600 and directory permissions 0700. The file is not additionally encrypted and can include a wallet private key saved by setup; do not upload or print it. `keys list` shows only labels and metadata; `keys activate --label NAME` switches the local key, and `keys remove --label NAME` removes only the local record.

## Call Data and Tool APIs

```bash
openany balance -j
openany discover -q "Get hot search data" -l 10 -j
openany inspect --id API_ID -j
openany run --id API_ID --query '{"limit":"10"}' -j
```

Follow this sequence: check the balance → search for an API → inspect its definition → make the call. Replace `API_ID` in the examples with an ID returned by the current search. Use the latest API definition returned by `inspect`, including pricing, required parameters, and version. If multiple APIs match, ask the user to confirm the platform, provider, or intended use.

Website capability pages group concrete API offerings from providers. Select an authorized offering, then use its API ID with `inspect` and `run`; a capability key, capability ID, or collection ID is not an executable API ID. The CLI currently discovers concrete APIs, not capability groups. Inspect the selected offering's own parameters, revision, and price; providers in the same group can have different contracts. Do not substitute a provider without authorization.

- `balance` checks the balance using the current API key and supports keys created through wallet or email login.
- `discover` supports `--provider`, `--data-platform`, `--category`, `--method`, and `--cursor`; `collections` lists catalog collections.
- Before calling, confirm the request method, path, input type, required fields, and cost, and stay within the current task's authorization and budget. After each balance check, follow **Low-Balance Guidance** below; apply the execution rule in Authorized Top-Ups when autonomous top-ups are already authorized. If the balance cannot cover the requested call, explain that recharge is needed before proceeding. Wallet registration follows the authorized onboarding flow below; reuse existing registration and key-creation authorization without asking again.
- `run` automatically reloads the API definition, validates parameters, checks the balance, and sends a synchronous request. `--path/--query/--headers` accept JSON; use `-i JSON` or `-f FILE` for the request body. If the API definition is incompatible with the CLI, such as a GET request that requires a body, report the error instead of bypassing CLI validation to send the request.
- `-j` outputs JSON; `-o FILE` writes results to a file without overwriting an existing file. Show the user the data actually returned by the API and retain `requestId`. Explain catalog query, balance query, and authentication errors separately from business call results.
- Calls return synchronously; there are no commands for asynchronous polling or stopping tasks. After a timeout or disconnection, check call records to verify the outcome before repeating a request that may already have been charged.

```bash
openany calls list --limit 20 -j
openany calls get --id REQUEST_ID -j
```

`calls list/get` queries persisted model and data/tool API usage for the current API key when the backend includes unified usage-call support. Older backends exclude model calls; updating the CLI alone does not change that. An empty result or missing record does not prove a request was not executed or charged; keep uncertain requests pending rather than automatically repeating them.

Record filters include `--api-id`, `--status success|failed`, `--from/--to` (UTC ISO format with milliseconds), and `--cursor`. `--review-required` is unsupported because usage logs have no review state; the CLI rejects it before making a request. Do not guess status aliases such as `settled`, `succeeded`, `ok`, or `pending`.

Replace `REQUEST_ID` with the real request ID. Model trace IDs are supported; canonical UUIDs match their 32-hex form as well, while other opaque IDs match exactly. `usageType` distinguishes `model` from `api`; model rows have `apiId: null`, a model name and the recorded provider. `chargedCostMicroUsd` is the persisted per-row charge in micro-USD; monthly usage is an aggregate, not proof of any individual charge. Unknown review or settlement metadata stays unknown. Lists default to seven days, with a maximum 31-day explicit range; detail queries are independent of that window. Old API-only cursors become invalid after the backend update; restart listing without a cursor. Never bypass key scope or automatically retry a possibly charged model request because its log is missing.

## Calling Models from Curl Examples

1. Run `openany models examples --model MODEL --public -j`. Read the request templates, provider names, example IDs, path parameters and per-example errors.
2. Choose `--provider PROVIDER` if the model has multiple providers. Choose `--example EXAMPLE_ID` if the chosen provider has multiple requests; never execute all examples as a batch. Copy IDs from the current listing; refresh after a sample changes.
3. Save the selected `request.json` template as `input.json` and supply the complete desired body, including `model` (unless the URL explicitly binds `{model}`). Do not assume example prompts/defaults are merged. JSON requests and image-edit multipart requests bind `route.provider` to the chosen catalog record, replacing `<provider-for-this-model>` or inserting the routing field. A conflicting concrete model/provider is an error.
4. Preview, then execute only within the user's authorized scope:

```bash
openany models run --model MODEL --provider PROVIDER --example CREATE_EXAMPLE_ID -f input.json --dry-run
openany models run --model MODEL --provider PROVIDER --example CREATE_EXAMPLE_ID -f input.json
openany models run --model MODEL --provider PROVIDER --example QUERY_EXAMPLE_ID --path '{"task_id":"ACTUAL_TASK_ID"}'
```

Dry-run needs no key and sends no model or balance request. Execution needs an API key. POST checks balance; GET task queries do not. Use the returned service response to obtain the task ID; the CLI does not infer response fields or automatically poll, retry, or download generated videos. Query templates must declare `{task_id}`; `YOUR_TASK_ID` is rejected until the catalog example is corrected.

Multipart image edits (`/v1/images/edit` or `/v1/images/edits`) use explicit `--attach FIELD=FILE`; repeat the flag for multiple files. Never read example file paths. Supply scalar fields in the input JSON; arrays become repeated fields, and `route` is a JSON object. Audio transcription is unsupported and must not be attempted.

`operations` and `parameters` are not execution prerequisites. `models parameters` remains informational; do not use `--protocol` or `--mode` with `models run`. `--endpoint` only checks the chosen example path. Invalid JSON, dangling continuations, unknown options, unresolved placeholders and conflicting model/provider values fail explicitly; do not repair or bypass them. Request URLs come from the actual examples; redirects are not followed even when an example includes `--location`. Preserve service errors and request IDs; uncertain POST outcomes must not be retried automatically.

## Wallet Login and API Key Management

After completing configuration for scenario C, choose the flow for the configured wallet. Use `openany keys list -j` to inspect local labels and metadata, together with the user's confirmed account/key information. An unrelated key, an imported key without wallet metadata, or an environment override does not establish that a key belongs to this wallet. An empty local list does not prove the wallet has never registered; ask if its account/key history is unclear.

**First-time wallet without a key:** under the user's authorization to register and create a key, run:

```bash
openany keys create --identity wallet --label agent-main -j
openany auth wallet -j
```

The first successful key creation automatically registers the wallet account and shared balance account; it does not require a login session. Wait for the key to be saved successfully before logging in. `auth wallet` itself only logs in and does not register an account. Registration is part of this authorized onboarding flow; do not request the same authorization again. Setup completion alone does not authorize registration or key creation.

**Existing key for this wallet:** reuse the key and log in directly:

```bash
openany auth wallet -j
```

Do not create or rotate a key just to log in. Keep the configured wallet and stable Agent ID unchanged. Do not treat a login failure or generic 401 as proof that the wallet is unregistered. If key creation returns a recovery file, follow Errors and Recovery before login; if the outcome is uncertain, resolve it before creating again. Do not change the label or Agent ID to bypass an existing-key conflict.

Key rotation is a separate, explicitly authorized operation:

```bash
openany keys rotate --identity wallet --label agent-next --expected-key-id CURRENT_KEY_ID -j
```

The CLI retrieves the message to sign, validates the service domain, identity, operation, and expiration, then signs it. Preserve the full original title and body of the signing message returned by the service. After saving a new key, the command outputs only metadata.

When using an external signing tool, add `--prepare` to the commands above to obtain the message to sign. Sign it with an authorized tool, then submit it using the same command with `--challenge-id ID --signature-file FILE`. The signature file must contain the raw signature text and correspond to the challenge ID just retrieved.

Rotation invalidates the old key; perform it only with the user's explicit authorization. Before rotating, identify applications using the key and prepare to update their secrets. If `OPENANY_API_KEY` still contains the old value, it takes precedence over the newly saved CLI key, so update the environment and verify it as well. If the outcome is unclear, verify the state before deciding what to do next.

## Email Login and Key Management

```bash
openany auth email send -j
openany auth email verify --code SIX_DIGITS -j
openany auth email workspace --workspace WORKSPACE_ID -j
openany auth email refresh -j
openany keys remote list --limit 100 --offset 0 -j
openany keys remote create --label agent-main -j
openany keys remote get --id KEY_ID -j
openany keys remote revoke --id KEY_ID -j
```

Read the current verification code through an authorized email connection, preserving all six digits and any leading zeros. If the email connection is unavailable, ask the user to connect an authorized mailbox tool or complete `openany auth email send` and `openany auth email verify --code SIX_DIGITS` in their own terminal, replacing the placeholder locally. Never ask them to paste the code or mailbox password into a model conversation; ask only for confirmation that login is complete. Avoid sending codes repeatedly. A single workspace is selected automatically; if there are multiple workspaces, select the one authorized by the user. Relevant commands also accept `--workspace ID`.

The CLI saves email login state but does not automatically refresh it. For an expired email session, explicitly run `openany auth email refresh -j`; if refresh is denied, log in again through the authorized mailbox. If refresh has an uncertain outcome or returns a recovery file, follow the recovery error before retrying. Refreshing an email session does not repair an invalid API key. Remote key lists use `limit/offset` pagination. Updating an email-based key requires separate steps: create a new key → update applications using it → verify the new key → revoke the specified old key within the user's authorization. Keep the old key until verification is complete to avoid disrupting existing calls.

## Authorized Top-Ups

### Low-Balance Guidance

Use `availableMicroUsd` from the current API key's balance check. A low balance means less than **5 USD** (5,000,000 microUSD); exactly 5 USD does not trigger a low-balance prompt or automatic top-up.

- **User-provided API key with no wallet information:** When the balance is below 5 USD, tell the user to recharge manually in the OpenAny dashboard at **https://openany.si**. For example: "Your API key's available balance is below 5 USD. Please sign in to the OpenAny dashboard and recharge manually." Do not invite automatic top-up authorization or guide wallet binding/setup as a response to this low balance.
- **API key confirmed to have been generated when the user created an account by binding a wallet, without existing automatic top-up authorization:** You may offer the authorization walkthrough below. Creating an account or generating a key does not itself authorize payments.
- **Other or unknown key origins without existing authorization:** Do not invite automatic top-up authorization. If a recharge is needed, direct the user to the OpenAny dashboard. A configured payment wallet alone does not prove that the current API key was generated through wallet-based account creation; do not infer its origin.
- **Existing automatic top-up authorization:** Reuse valid approval for the current account and task under **Execute Authorized Top-Ups**; do not ask for the same approval again. Authorization does not replace required wallet information or payment checks.

A low-balance warning alone does not prevent calls that the remaining balance can cover within the user's task and budget.

### Explain How to Enable Top-Ups

Offer this authorization walkthrough only when the current API key is confirmed to have been generated through wallet-based account creation and the user has not authorized automatic top-ups. A low balance or a question about automatic top-ups does not establish that eligibility; otherwise follow **Low-Balance Guidance**. Give eligible users an actionable walkthrough in their language. Do not end with only "provide authorization, limits, network, scope, and expiration" or a `topups authorize` command. Keep the completed connection checks unchanged and explain the following next steps:

1. **Choose whether to enable it.** Explain that top-ups are optional: the user can reply "Not now" and use the remaining balance while it covers the requested calls. Enabling means that when the agent checks the balance and finds less than 5 USD, it attempts a top-up with a 10 USD input amount within the approved limits. Fees can change the actual payment or net credit. There is no background monitor, and a currently low balance can trigger a top-up as soon as authorization and setup are complete.
2. **Identify the account and payment wallet.** Reuse the confirmed wallet account information and ask only for any missing payment wallet/network choice. Explain that the network is the blockchain holding the wallet's payment funds, not Wi-Fi or a bank card network. If the user does not know, offer the exact reply "Guide me through setup; I don't know my wallet network." This requests help, not payment permission. Resolve those choices before presenting a ready-to-approve plan; do not choose a network from an address format alone. Use the wallet identity's network support below. If no wallet information is available, direct the user to manual recharge instead of starting wallet setup from a low-balance warning.
3. **Provide a copyable approval message.** Fill only facts already known, explain any remaining fields, and let the user edit them. A single-payment limit caps each actual payment including fees; the total limit caps all payments under this authorization, not the account's monthly API spending limit. Purpose means the specific task whose balance checks may trigger top-ups. Expiration needs a date, time, and time zone. Do not invent approved values or treat an unfilled template, example, silence, or a request for help as consent:

   > I authorize low-balance top-ups for [account to credit], paid from [payment wallet address] on [network]. When you check the balance during [specific task] and it is below 5 USD, use a 10 USD top-up input amount. Each actual payment including fees must not exceed [single-payment limit] USD, and all payments under this authorization must not exceed [total limit] USD. This authorization expires at [date, time, time zone]. If the quote exceeds either limit, stop and ask me. Within these limits, you may complete the required login, record the authorization, and execute top-ups without asking again.

4. **Guide only missing local setup.** After the eligible user chooses to enable top-ups, guide any missing local signing configuration for the known wallet. Tell the user to open their own terminal on the machine/account running OpenAny, run `openany setup --scenario wallet`, and explain the hidden private-key prompt, automatic address display, and stable Agent ID input (for example `openany-main`, retained for later use). Apply the full sensitive-information warning and local-storage guidance from **Missing Configuration: Required User Guidance**. Existing authorized signing tools follow the external-signing flow instead. Ask the user to reply only "Configured" when done; setup completion alone is not payment approval.
5. **Explain what the agent does next.** After explicit approval, check local configuration, complete wallet login, verify the account to credit, record the approved policy with `topups authorize`, and rerun payment preflight with wallet identity and the approved scope. Translate a confirmed network name to its CLI identifier and the approved expiration to an ISO timestamp; clarify any ambiguity. The user does not need to construct CLI flags. Report readiness only after the checks pass; report payment and credit only after their respective confirmations.

Keep the first response manageable: show the approval template and the next applicable action. If the wallet network is unknown, ask for that information first and explain that local configuration follows. A reply such as "Enable it" with missing limits is a request to finish this walkthrough, not unrestricted payment permission. When all required details were already explicitly approved, continue within that approval without asking the user to approve them again.

### Execute Authorized Top-Ups

Use only when the user has explicitly authorized autonomous top-ups. Before creating or paying for an order, complete login and policy setup within that authorization. For wallet identity, complete Wallet Login and API Key Management above before creating an order (first-time wallet: create key → login; existing wallet key: login directly); for email identity, log in and select a workspace first. The API key must belong to the top-up target. Wallet identity supports the EVM networks published by the service; email identity supports Base `eip155:8453` and Solana `solana:5eykt4UsFv8P8NJdTREpY1vzqKqZKvdp`.

A payment preflight requires a login session and a saved payment policy. If it reports `wallet_session` or `email_session` missing, follow the corresponding authorized login flow above. If `payment_policy` is missing, record the user's existing approval with `topups authorize`; ask only when credentials, tools, or approval details are missing. Then rerun the payment check with the target identity and task scope, as shown below. Do not manually edit the credentials file to supply sessions or policies.

Whenever the agent checks the available balance (`availableMicroUsd`) and finds less than **5 USD** (5,000,000 microUSD), initiate a top-up with `--amount-usd 10` if autonomous top-ups are authorized, without asking for confirmation again within that authorization. A balance of exactly 5 USD does not trigger a top-up. The 10 USD is the command input amount, not a guarantee of 10 USD net credit or a target balance. Respect the approved network, actual payment limits (including fees), scope, and expiration; if they do not permit the purchase, ask the user to update the authorization. Resolve any pending or uncertain top-up using its existing intent before creating another, and recheck the balance after confirmed crediting. This is an agent-executed policy; the CLI does not monitor balances or run a background auto-top-up loop.

First record the network, limits, purpose, and expiration already approved by the user, then create and pay for the order. Changing limits or extending the expiration requires additional user authorization:

```bash
openany topups authorize --identity wallet --network eip155:8453 --max-single-usd USER_APPROVED_SINGLE_LIMIT --max-total-usd USER_APPROVED_TOTAL_LIMIT --scope TASK_SCOPE --expires-at USER_APPROVED_ISO_TIME -j
openany status --scenario payment --identity wallet --scope TASK_SCOPE -j
openany topups create --scope TASK_SCOPE --intent UNIQUE_INTENT --amount-usd 10 --network eip155:8453 -j
openany topups pay --intent UNIQUE_INTENT -j
openany topups get --intent UNIQUE_INTENT -j
```

Read the locked quote before signing: `inputAmountMeaning` defines the input amount, `payAmountMicroUsd` is the actual payment, `feeAmountMicroUsd` is the fee, and `creditAmountMicroUsd` is the balance credit. With `add_on`, a 10 USD input credits 10 USD and adds the fee to payment. With `deduct_from_credit`, a 10 USD input pays 10 USD and credits less after the fee. Preserve `--amount-usd 10` for this policy; do not silently increase it to force a net credit of 10 USD. Report the actual quoted and credited amounts.

Amounts are in USD with up to six decimal places. The 10 USD input above is the authorized low-balance policy amount; authorization limits and other placeholders must use values already approved by the user. Limit checks include fees, and pending orders also reserve part of the total allowance. `authorize` records existing authorization locally; running this command does not itself mean the user has approved a top-up.

- Choose and retain the top-up identifier (intent) before creating an order, and retain the returned order ID. The CLI generates and persists the idempotency key before sending the request and reuses it for the same intent; do not read the credentials file to extract it. Always use the same intent, scope, amount, and network for the same top-up. If creation has an unclear outcome, wait for the current request to finish, then resume with the original parameters plus `--resume` to avoid creating duplicate orders.
- `pay` validates the order's locked quote, resource domain, recipient, network, amount, payment wallet, expiration, and authorized budget. EVM uses EIP-3009 signing; for Solana, the CLI validates and signs the transaction returned by the service, which then submits it.
- External signing: `create --prepare` does not require a private key; `pay --prepare` returns the payload to sign. After signing, submit it with `pay --intent NAME --signature-file FILE`. The payment signature file format is `{"signature":"EVM_SIGNATURE"}` or `{"transaction":"BASE64_SIGNED_SOLANA_TRANSACTION"}`. If the signing payload expires, run `pay --prepare` again for the original order.
- After payment has been submitted, timed out, or has an unknown outcome, check status only with `topups get --intent NAME`. Do not pay again, change the idempotency key, or bypass the CLI. Report the funds as credited only after confirming `paymentStatus=paid` and `creditStatus=credited`; wallet identity also requires `accountUsable=true`.
- For email identity, use `topups config` and `topups quote --amount-usd AMOUNT --network CAIP2` to view top-up configuration and estimates first; the order's locked quote governs the actual payment.
- If service domains do not match, pause and ask the user to contact OpenAny support. Do not switch service domains or modify the message to sign.

## Errors and Recovery

On failure, the CLI returns a nonzero exit code and writes `{"error":{"code":"...","message":"..."}}` to standard error (stderr), possibly including `requestId`, `status`, and `missing`. Explain the error to the user and retain the error code and request ID without exposing credentials. Judge success by the final business outcome; HTTP 200, heartbeats, or the start of a stream indicate only request progress.

If login or key management succeeds on the server but cannot be saved locally, the CLI returns the path to a recovery file with restricted access. Use `openany auth recover --file RETURNED_PATH` to recover the original result and avoid creating duplicate keys; import only verified recovery files. For other unclear outcomes, follow the error guidance. If the configuration lock is held, first check whether the associated process is still running. If the process has exited but the lock remains, ask a maintainer to help clear the specified lock; do not automatically delete locks or retry payments.

A balance query verifies only the current key's ability to access the balance. It does not verify wallet signing, email connectivity, or payment permissions. If a required capability is missing, explain exactly what is missing and the next configuration steps. Report task completion based on actual execution results.
