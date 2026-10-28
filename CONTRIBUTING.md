# Contributing to OpenAny

Thank you for helping improve OpenAny's agent integration resources.

This repository covers documentation, agent skills, and usage examples. The OpenAny CLI is maintained separately. Start with an issue to discuss new skills or examples before implementing them.

## What to contribute

- Fix unclear instructions, typos, or broken links.
- Improve agent setup guidance with reproducible steps.
- Report differences between the documentation and actual CLI behavior.
- Suggest useful skills or examples with a clear purpose and data source.

## Report an issue

Include the relevant document or command, what you expected, and what happened. For setup or CLI problems, include your operating system, agent name and version, Node.js version, OpenAny CLI version, and minimal reproduction steps.

Share only sanitized output. Remove API keys, wallet private keys, seed phrases, verification codes, tokens, cookies, and personal information from logs, screenshots, and attachments. Never upload a credentials file.

## Keep documentation consistent

`AGENT.md` is synchronized from `public/AGENT.md` in the OpenAny website project and must remain identical to that source. When proposing a change, explain the correction so maintainers can update the source and synchronize both copies. The published instructions are available at [openany.si/AGENT.md](https://openany.si/AGENT.md).

The OpenAny CLI package maintains the integration skill published at [openany.si/SKILL.md](https://openany.si/SKILL.md). Coordinate skill changes with that source rather than creating conflicting instructions in this repository.

Use English for the README, contribution guide, commit messages, and pull request descriptions. Keep any language variants aligned when they exist. Use `openany-cli@latest` in installation instructions and record the actual CLI version when reporting verification results.

## Submit a pull request

1. Fork the repository and create a branch for one focused change.
2. Make the change and review the rendered Markdown.
3. Check links, command syntax, and consistency with the source documents.
4. Use a concise English commit message, such as `docs: clarify agent setup`.
5. Open a pull request explaining the problem, the change, and how you verified it. Link the related issue when applicable.

Keep each file under 10 MB. Use real, sanitized results when documenting successful calls, usage, costs, or payments. Clearly label mock output and unverified behavior. Run paid calls only within an explicitly authorized purpose and budget.

For documentation-only changes, check formatting with `git diff --check`; no application build is required. For command or integration changes, describe the environment and the checks performed. If live verification was not performed, say so.

## License

Contributions to this repository are provided under the [MIT License](LICENSE). Submit only material you have the right to contribute, and retain any required third-party attribution or notices.

This license covers material in this repository. The separately distributed OpenAny CLI, hosted services, and third-party APIs remain subject to their own licenses and terms.
