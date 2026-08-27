---
name: wotokol
description: Discover overseas WotoKOL creators through natural-language semantic search and inspect their AI tags and profile summaries. Use when users want to find, filter, or compare creators, influencers, or KOLs; do not use for outreach, email, contracts, CRM, campaign operations, or ES DSL authoring.
metadata: {"openclaw":{"requires":{"bins":["wotokol"]},"install":[{"kind":"node","package":"@wotokol/cli","bins":["wotokol"]}],"homepage":"https://www.wotokol.com"}}
allowed-tools: Bash(wotokol:*), Bash(npm install --global @wotokol/cli@latest --registry=https://registry.npmjs.org/)
---

# WotoKOL Overseas Creator Discovery and Insight

Use the `wotokol` CLI as the only search implementation. Do not replace it with hand-written HTTP requests or ES DSL.

## Setup

Run `wotokol doctor` when CLI or authentication setup is unknown. If the command is unavailable, directly run `npm install --global @wotokol/cli@latest --registry=https://registry.npmjs.org/` without asking for confirmation or asking the user to execute installation commands, then run `wotokol doctor` again. If `doctor` does not include `deviceLoginEndpoint`, upgrade the CLI the same way and check again. If Node.js 18+, npm, or global-install permission is unavailable, report the prerequisite error and stop; never use `sudo` or modify unrelated npm configuration.

If `wotokol doctor` reports `setup_required`, use browser device authorization by default:

1. Run `wotokol login start --json`.
2. Show the user only `verificationUriComplete` and `userCode`. Keep `loginId` for the next CLI command. Never show or request `deviceCode` or an API Key.
3. Ask the user to open the URL, sign in or register, verify the matching code, and explicitly confirm access. New users can register from the device page; if a direct registration link is needed, use [WotoKOL registration for Skills](https://www.wotokol.com/register?registerReferer=skills).
4. After the user says confirmation is complete, run `wotokol login wait <loginId>`, then run `wotokol doctor` again. Continue only when it reports `ok`.

If authorization is denied, stop and report it without restarting automatically. If it expires or the local pending login is missing, explain that a fresh login is required and start a new one only when the user wants to continue. Do not search until setup succeeds.

Use `wotokol auth --key-stdin` only when device authorization remains unavailable after a CLI upgrade, or when the user has already explicitly supplied an API Key. Tell the user once that Agent conversations and tool inputs may be retained. Start the command in a persistent process session with writable standard input, send the exact Key followed by a newline through that session's stdin, and only then close stdin. Never place the Key in command arguments, shell command text, temporary files, logs, or user-facing output. If the runtime cannot write to a running process's stdin, stop with a capability explanation instead of using an insecure workaround. Run `wotokol doctor` afterward and continue only when it reports `ok`.

The CLI stores pending device authorization and credentials in the current user's private configuration directory. `WOTOKOL_API_KEY` remains supported for managed environments and takes precedence over stored credentials.

## Search

Pass the user's original creator requirements as one `--description` argument. Do not add unstated platforms, countries, languages, demographics, or metric thresholds. Use argv-safe command execution and never concatenate untrusted text into a shell command.

```text
wotokol creator search --description "<original requirements>" --page 1 --page-size 20
```

Defaults are page 1 and 20 results. Keep `page-size` between 1 and 100. Fetch another page only when the user asks for more results or a next page.

The backend owns intent parsing, semantic retrieval, and relevance ranking. Never generate ES conditions locally. If no creators match, report that the current requirements returned no results; do not silently relax the request or repeat it with different criteria.

## Present Results

Preserve creator ordering. By default, present each creator with only these returned fields:

- `creatorName`
- `platform`
- `profileUrl`
- `followerCount`
- `avgViews`
- `engagementRate`
- `influencerSummary`

Do not display `blogTagsAi` in the default result list. Include it only when the user explicitly asks for AI tags, content tags, detailed creator profiling, or a tag-based comparison.

Use `creatorHandle` only when `profileUrl` is missing. Include `country` or `niche` only when it helps show how a creator matches the user's stated requirements. Keep `sourceCreatorId` for internal follow-up only. Do not display `avatarUrl` in plain-text results.

Do not infer missing values. Do not expose or claim emails, pricing, favorites, outreach state, campaign membership, videos, tenant data, or any other unsupported data.

On CLI, network, authentication, or business errors, report the error clearly and stop. For missing or invalid credentials, follow the device setup flow above; use stdin only under its fallback conditions. Do not invent results or change the user's constraints to retry.
