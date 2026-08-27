# WotoKOL Skills

Agent Skill for discovering overseas creators through WotoKOL natural-language semantic search and presenting returned AI tags and profile summaries.

## Repository contents

```text
skills/wotokol/
├── SKILL.md
└── agents/openai.yaml

evals/wotokol/
└── evals.json
```

The Skill contains routing and result-presentation instructions only. API requests, authentication, validation, and error handling are implemented by the separately published `@wotokol/cli` package.

## Prerequisites

Install the CLI:

```bash
npm install --global @wotokol/cli
```

When the Skill is running in a compatible Agent, it automatically installs or updates a missing or outdated CLI from the official npm registry. The user does not need to confirm the installation or run the command manually. The host Agent may still request permission according to its own command-execution policy.

Authentication uses browser device authorization by default. The Agent starts the login and shows the returned verification URL and user code:

```bash
wotokol login start --json
wotokol login wait <login_id>
wotokol doctor
```

Open the verification URL, sign in or register, verify the matching code, and explicitly confirm access. New users can register from that flow or use the [WotoKOL Skills registration page](https://www.wotokol.com/register?registerReferer=skills). The API Key is delivered only to the CLI and is not shown in the browser or Agent conversation.

`wotokol auth --key-stdin` remains a fallback when device authorization is unavailable or the user has already supplied a Key. The Agent must keep that process open, send the Key plus a newline through writable stdin, and only then close stdin. The Key must never be placed in command arguments, shell text, temporary files, logs, or user-facing output.

## Install the Skill

Use the open Skills CLI to install from GitHub:

```bash
# Auto-detect a supported Agent
npx skills add https://github.com/WotoKOL/wotokol-skills --skill wotokol

# Cursor
npx skills add https://github.com/WotoKOL/wotokol-skills --skill wotokol --agent cursor

# Claude Code
npx skills add https://github.com/WotoKOL/wotokol-skills --skill wotokol --agent claude-code

# OpenClaw
npx skills add https://github.com/WotoKOL/wotokol-skills --skill wotokol --agent openclaw

# OpenAI Codex
npx skills add https://github.com/WotoKOL/wotokol-skills --skill wotokol --agent codex
```

Alternatively, copy [`skills/wotokol`](skills/wotokol) into the target agent's local Skill directory. The `wotokol` executable must be available on `PATH`.

After installation, start a new Agent session and ask for creators in natural language, for example:

```text
Find US TikTok beauty tutorial creators with at least 100K followers.
```

Default results show only creator information: creator name, platform, profile URL, follower count, average views, engagement rate, and AI summary. When the user explicitly asks for AI tags or detailed creator profiling, the Skill adds `--include-ai-tags`; otherwise the CLI omits the larger `blogTagsAi` profile from its JSON output.

## Validate

Before publishing, run the target agent's Skill validator against `skills/wotokol` and validate [`evals.json`](evals/wotokol/evals.json) as JSON.

## License

Licensed under the [Apache License 2.0](LICENSE).

This license covers only the source code and Skill content in this repository. Use of the WotoKOL API, creator data, and hosted services is governed separately by WotoKOL's service terms. The license does not grant permission to use WotoKOL trademarks or logos except as allowed by the license.
