# Creator Result Format

## Default format

Summarize the original requirements in one sentence, then list creators in API order:

```text
Based on "<original user requirements>", these creators were returned:

1. <creator name>
   - Platform: <platform>
   - Profile: <profileUrl>
   - Followers: <followerCount>
   - Average views: <avgViews>
   - Engagement rate: <engagementRate>
   - Profile summary: <influencerSummary>
```

Localize labels and surrounding prose to the user's language. When the user writes in Chinese, use concise, natural Simplified Chinese rather than translating field names literally or mixing English prose into the result.

Show only fields that were returned. If a field is missing, omit that line; do not print `unknown` or infer it from other values.

## Presentation rules

- Preserve the original `creators` order. Do not re-sort by followers or engagement.
- If `creatorName` is missing, use `creatorHandle` as the title when available.
- If `profileUrl` is missing, show `creatorHandle` when available. Do not construct a profile URL.
- Numeric grouping separators are allowed, but never change the value's meaning.
- If the scale of `engagementRate` is unclear, preserve the returned representation. Do not multiply it by 100.
- Add `country` or `niche` only when it helps explain the match.
- Do not display `sourceCreatorId`, `hasMore`, page numbers, CLI commands, or raw JSON.
- Prefer a numbered list over a table when summaries are long or the user is on a narrow screen.

## Empty result

Respond in the user's language with this meaning:

```text
The current requirements returned no matching creators. I did not relax your criteria automatically; tell me which requirement you want to change.
```

## No more results

Respond in the user's language with this meaning:

```text
There are no more results for the current search.
```

## Error

Respond in the user's language with this meaning:

```text
WotoKOL search failed: <sanitized error>. No creator results were generated or inferred.
```
