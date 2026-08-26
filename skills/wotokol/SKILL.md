---
name: wotokol
description: Discover overseas WotoKOL creators through natural-language semantic search and inspect their AI tags and profile summaries. Use when users want to find, filter, or compare creators, influencers, or KOLs; do not use for outreach, email, contracts, CRM, campaign operations, or ES DSL authoring.
---

# WotoKOL Overseas Creator Discovery and Insight

Use the `wotokol` CLI as the only search implementation. If the command is unavailable, ask the user to install `@wotokol/cli`; do not replace it with hand-written HTTP requests or ES DSL.

## Setup

Run `wotokol doctor` when API Key setup is unknown. If it reports `setup_required`, direct the user to [wotohub.com](https://www.wotohub.com) to register and configure `WOTOKOL_API_KEY` in their environment before searching.

Never ask the user to paste an API Key into the conversation. Never place it in command arguments, output, or logs. The CLI reads the environment variable and sends it through the `api-key` header.

## Search

Pass the user's original creator requirements as one `--description` argument. Do not add unstated platforms, countries, languages, demographics, or metric thresholds. Use argv-safe command execution and never concatenate untrusted text into a shell command.

```text
wotokol creator search --description "<original requirements>" --page 1 --page-size 20
```

Defaults are page 1 and 20 results. Keep `page-size` between 1 and 100. Fetch another page only when the user asks for more results or a next page.

The backend owns intent parsing, semantic retrieval, and relevance ranking. Never generate ES conditions locally. If no creators match, report that the current requirements returned no results; do not silently relax the request or repeat it with different criteria.

## Present Results

Preserve creator ordering. Report `totalCount`, `currentPage`, and `pageSize`, then present only fields returned by the CLI:

- creator name, platform, handle, country, niche, avatar, and profile URL
- follower count, average views, and engagement rate
- `blogTagsAi` and `influencerSummary`

Do not infer missing values. Do not expose or claim emails, pricing, favorites, outreach state, campaign membership, videos, tenant data, or any other unsupported data.

On CLI, network, authentication, or business errors, report the error clearly and stop. For a missing Key or `user.notLogin.error`, provide the registration URL above. Do not invent results or change the user's constraints to retry.
