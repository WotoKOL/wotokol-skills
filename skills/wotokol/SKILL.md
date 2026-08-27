---
name: wotokol
description: Search WotoKOL overseas creators from natural-language requirements and present creator basics with AI-generated profile summaries. Use for creator discovery, filtering, pagination, and comparison; not for outreach, email, contracts, CRM, campaign operations, or unsupported creator data.
metadata: {"openclaw":{"requires":{"bins":["wotokol"]},"install":[{"kind":"node","package":"@wotokol/cli","bins":["wotokol"]}],"homepage":"https://www.wotokol.com"}}
allowed-tools: Bash(wotokol:*), Bash(npm install --global @wotokol/cli@latest --registry=https://registry.npmjs.org/)
---

# WotoKOL Overseas Creator Discovery and Insights

Use the `wotokol` CLI as the only search implementation. Do not replace it with direct HTTP requests.

## Capability Boundary

This Skill can:

- Submit the user's Chinese or English requirements unchanged to WotoKOL semantic search.
- Find creators by the platforms, regions, content themes, audience size, or other criteria the user explicitly provides.
- Present creator basics and `influencerSummary`, preserving the returned order.
- Compare returned creators and fetch another page when more results are available.

This Skill cannot:

- Retrieve emails, prices, collaboration status, campaign relationships, video lists, or other unsupported data.
- Infer missing values, fabricate creators, or relax requirements without the user's explicit instruction.
- Perform outreach, send email, accept terms, make payments, or execute creator operations.

## Workflow

### Step 1: Confirm the environment

- Input: Unknown CLI or authentication state.
- Action: Run `wotokol doctor`. If the command is unavailable, run `npm install --global @wotokol/cli@latest --registry=https://registry.npmjs.org/`, then run doctor again.
- Output: Continue to search only when doctor returns `status: ok`. For any other state, follow [CLI and API contract](references/api-contract.md) for installation, browser authorization, or error handling.

Do not use `sudo`, change unrelated npm configuration, or ask the user to run CLI installation commands.

### Step 2: Prepare the search input

- Input: The user's original requirements and any explicit result count or page request.
- Action: Pass the full original request as one `--description` value. Do not add platforms, countries, languages, demographics, or metric thresholds that the user did not provide.
- Output: `description`, `page`, and `page-size`. Defaults are page 1 and 20 results; page size must be between 1 and 100.

If the user requests more than 100 creators, fetch at most 100 on the current page and explain the per-page limit. Fetch another page only when the user asks for more.

### Step 3: Run the search

- Input: The parameters prepared in Step 2.
- Action: Execute the command with argv-safe tooling. Never concatenate untrusted user text into a shell command string.
- Output: One complete JSON document from the CLI.

```text
wotokol creator search --description "<original user requirements>" --page 1 --page-size 20
```

The backend owns intent parsing, semantic retrieval, and relevance ranking. Do not locally rewrite the requirements or generate additional filters.

### Step 4: Validate the result

- Input: CLI JSON.
- Action: Confirm that `creators` is an array, preserve its order, and use only fields that are actually returned.
- Output: A creator list ready for presentation, or a clear empty-result or error message.

If `creators` is empty, state that the current requirements returned no creators. On CLI, network, authentication, or business errors, report the error and stop. Do not invent results or retry with changed requirements.

### Step 5: Present the creators

- Input: The validated creator array.
- Action: Follow [creator result format](references/output-format.md). Respond in the user's language; use natural Simplified Chinese labels and phrasing when the user writes in Chinese.
- Output: A concise, localized creator list. Do not paste raw JSON or expose `hasMore`, page numbers, or other technical metadata.

Prioritize creator name, platform, profile link, follower count, average views, engagement rate, and AI profile summary. Use `creatorHandle` only when `profileUrl` is missing. Include `country` or `niche` only when it helps explain the match. Keep `sourceCreatorId` for internal result alignment only.

### Step 6: Handle pagination

- Input: The user explicitly asks for the next page or more creators, and the previous response had `hasMore: true`.
- Action: Reuse the exact same `description` and `page-size`; increment only `page`.
- Output: The next creator list.

If the previous response had `hasMore: false`, explain that no more results are available and do not call search again.

## Examples

The examples below describe behavior only and do not represent real creator data.

### Example 1: First search

Input:

```text
Find US TikTok beauty tutorial creators with at least 100K followers. Return 20.
```

Action:

```text
wotokol creator search --description "Find US TikTok beauty tutorial creators with at least 100K followers. Return 20." --page 1 --page-size 20
```

Output: Present up to 20 creators in relevance order, using only returned creator basics and profile summaries.

### Example 2: Next page

Input:

```text
Show the next page.
```

Output: If the previous response had `hasMore: true`, reuse the original requirements and request page 2. Otherwise state that no more results are available.

### Example 3: Unsupported data

Input:

```text
Also give me these creators' emails and prices.
```

Output: Explain that this Skill does not provide emails or prices. Do not issue another search or infer those values.
