# CLI and API Contract

Use this reference for installation, authentication, search invocation, and error handling. The Skill always calls the `wotokol` CLI and never calls the underlying HTTP endpoint directly.

## Environment check

```text
wotokol doctor
```

Important states:

- `ok`: Search can proceed.
- `setup_required`: Browser device authorization is required.
- `error`: Report the problem returned by doctor and stop.

If `deviceLoginEndpoint` is missing or differs from `https://api.wotohub.com/user/skills/device-login`, upgrade `@wotokol/cli@latest` from the official npm registry and run doctor again.

## Browser device authorization

1. Run `wotokol login start --json`.
2. Show the user only `verificationUriComplete` and `userCode`. Retain `loginId` in the current session.
3. Ask the user to open the URL, sign in or register, verify the matching code, and explicitly approve access.
4. After the user confirms approval, run `wotokol login wait <loginId>`, then run doctor again.

Never display or request `deviceCode` or an API Key. Stop when authorization is denied. If authorization expires or the local pending login is missing, start again only when the user explicitly wants to continue.

Registration: [WotoKOL registration for Skills](https://www.wotokol.com/register?registerReferer=skills).

## API Key fallback

Use `wotokol auth --key-stdin` only when device authorization remains unavailable after upgrading the CLI, or when the user has already explicitly supplied an API Key.

- Warn once that the Agent platform may retain conversations and tool inputs.
- Start the command in a persistent process with writable stdin. Send the exact Key followed by a newline, then close stdin.
- Never place the Key in command arguments, shell text, temporary files, logs, or user-facing output.
- If the runtime cannot write to a running process's stdin, explain the limitation and stop instead of using an insecure workaround.

Run doctor after configuration and continue only when it reports `status: ok`.

## Creator search

```text
wotokol creator search --description "<natural-language requirements>" --page <positive integer> --page-size <1-100>
```

Constraints:

- `description`: Required, non-blank after trimming, maximum 2,000 characters.
- `page`: Default 1, minimum 1.
- `page-size`: Default 20, range 1-100.

The CLI uses `https://api.wotohub.com/api-gateway/search/naturalSearch` with the `api-key` request header. This is contract documentation only and does not authorize bypassing the CLI.

## Response structure

CLI stdout contains one compact, complete JSON document:

```json
{
  "creators": [],
  "hasMore": false
}
```

Each creator may contain only these fields:

- `sourceCreatorId`
- `platform`
- `creatorName`
- `creatorHandle`
- `profileUrl`
- `country`
- `niche`
- `followerCount`
- `avgViews`
- `engagementRate`
- `influencerSummary`

The CLI does not return avatars, totals, current-page metadata, or page-size metadata. Never infer or invent missing fields.

## Errors and stopping conditions

- Exit code `2`: Invalid arguments or missing configuration. Correct the reported argument problem once; use the authorization flow for credential problems.
- Exit code `1`: Network, API, or runtime failure. Report the error and stop.
- Empty result: Do not relax requirements, repeat the request, or fabricate creators.
- Authorization denied: Do not automatically restart authorization.
