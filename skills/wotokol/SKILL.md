---
name: wotokol
description: Discover overseas WotoKOL creators through natural-language semantic search and inspect their AI tags and profile summaries. Use when users want to find, filter, or compare creators, influencers, or KOLs; do not use for outreach, email, contracts, CRM, campaign operations, or ES DSL authoring.
metadata: {"openclaw":{"requires":{"bins":["wotokol"]},"install":[{"kind":"node","package":"@wotokol/cli","bins":["wotokol"]}],"homepage":"https://www.wotokol.com"}}
allowed-tools: Bash(wotokol:*), Bash(npm install --global @wotokol/cli@latest --registry=https://registry.npmjs.org/)
---

# WotoKOL Overseas Creator Discovery and Insight

Use the `wotokol` CLI as the only search implementation. Do not replace it with hand-written HTTP requests or ES DSL.

## Setup

Run `wotokol doctor` when CLI or API Key setup is unknown. If the command is unavailable or `cliVersion` is below `0.2.0`, explain that the WotoKOL CLI must be installed or updated and ask for confirmation before changing the user's global npm environment. After confirmation, run `npm install --global @wotokol/cli@latest --registry=https://registry.npmjs.org/`, then run `wotokol doctor` again. Do not ask the user to execute installation commands. If Node.js 18+, npm, or global-install permission is unavailable, report the prerequisite error and stop; never use `sudo` or modify unrelated npm configuration.

If `wotokol doctor` reports `setup_required`, direct the user to the [WotoKOL Skills registration page](https://www.wotokol.com/register?registerReferer=skills) to register and obtain an API Key. Do not search until setup succeeds.

Tell the user once that Agent conversations and tool inputs may be retained. After the user explicitly provides the Key, start `wotokol auth --key-stdin` and write it only to the process standard input. Never place the Key in command arguments or shell command text, and never echo, repeat, or include it in user-facing output. After the CLI reports success, run `wotokol doctor` again and continue only when it reports `ok`.

The CLI stores the Key in the current user's configuration directory. `WOTOKOL_API_KEY` remains supported for managed environments and takes precedence over the stored Key.

## Search

Pass the user's original creator requirements as one `--description` argument. Do not add unstated platforms, countries, languages, demographics, or metric thresholds. Use argv-safe command execution and never concatenate untrusted text into a shell command.

```text
wotokol creator search --description "<original requirements>" --page 1 --page-size 20
```

Defaults are page 1 and 20 results. Keep `page-size` between 1 and 100. Fetch another page only when the user asks for more results or a next page.

The backend owns intent parsing, semantic retrieval, and relevance ranking. Never generate ES conditions locally. If no creators match, report that the current requirements returned no results; do not silently relax the request or repeat it with different criteria.

## Present Results

Preserve creator ordering. Report `totalCount`, `currentPage`, and `pageSize`. By default, present each creator with only these returned fields:

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

On CLI, network, authentication, or business errors, report the error clearly and stop. For a missing Key, follow the setup flow above. For an invalid Key or `user.notLogin.error`, ask the user to obtain a valid Key and replace it through the same stdin flow. Do not invent results or change the user's constraints to retry.
