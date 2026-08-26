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

When the Skill is running in a compatible Agent, it detects a missing or outdated CLI, asks before changing the global npm environment, and performs the installation for the user. The user does not need to run the installation command manually.

Register at [wotohub.com](https://www.wotohub.com) to obtain an API Key. The Skill can configure it through the CLI standard-input flow, then verify the environment:

```bash
wotokol auth --key-stdin
wotokol doctor
```

The Key must be written only to the command's standard input, never passed as a command-line argument. Agent conversations and tool inputs may be retained by the Agent platform; use a revocable Key and rotate it after unexpected exposure.

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

Default results show the creator name, platform, profile URL, follower count, average views, engagement rate, and AI summary. The larger `blogTagsAi` profile is shown only when the user explicitly asks for AI tags or detailed creator profiling.

## Validate

Before publishing, run the target agent's Skill validator against `skills/wotokol` and validate [`evals.json`](evals/wotokol/evals.json) as JSON.

## License

Licensed under the [Apache License 2.0](LICENSE).

This license covers only the source code and Skill content in this repository. Use of the WotoKOL API, creator data, and hosted services is governed separately by WotoKOL's service terms. The license does not grant permission to use WotoKOL trademarks or logos except as allowed by the license.
