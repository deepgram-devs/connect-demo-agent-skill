# Connect Demo Agent Skill

 [![Discord](https://dcbadge.vercel.app/api/server/xWRaCDBtW4?style=flat)](https://discord.gg/xWRaCDBtW4)

A [Claude Code](https://claude.com/claude-code) skill that generates every deployment file needed
to stand up an **Amazon Connect AI Agent demo** for any industry vertical. Give it a short scenario
("create a banking demo — customer calling about a transfer") and it produces a complete,
ready-to-deploy file set.

## What it generates

For each demo, the skill produces:

1. **Demo guide** (`demo-guide.md`) — the presenter's reference document
2. **Schema JSON files** (one per tool) — uploaded to Bedrock Agent Core gateway targets
3. **CloudFormation template** (`{vertical}_template.yaml`) — deploys all Lambda functions
4. **Connect system prompt** (`connect-system-prompt.yaml`) — pasted into the AI Agent prompt editor
5. **Connect flow template** (`connect-flow-template.json`) — imported into Connect as a contact flow

## Repository layout

```
SKILL.md                      # the skill definition (instructions Claude follows)
references/
├── examples.md               # exact file formats for every generated artifact
├── energy_template.yaml      # reference CloudFormation demo (energy vertical)
└── pharmacy_template.yaml     # reference CloudFormation demo (pharmacy vertical)
```

## Installation

This skill is loaded by Claude Code from `~/.claude/skills/`. Symlink this repo into that
directory:

```bash
ln -s "$(pwd)" ~/.claude/skills/connect-demo-generator
```

The skill's name (`connect-demo-generator`) comes from the `name:` field in `SKILL.md`, not the
directory name, so the symlink name is your choice.

## Usage

Inside Claude Code, just describe the demo you want — for example:

> create a demo for an insurance use case — customer calling about a claim

Claude will detect the skill and generate the full file set in a new `{vertical}-demo/` folder.
See `references/examples.md` for the exact format of each generated file.

## Getting an API Key

🔑 To access the Deepgram API you will need a [free Deepgram API Key](https://console.deepgram.com/signup?jump=keys).

## Documentation

You can learn more about the Deepgram API at [developers.deepgram.com](https://developers.deepgram.com/docs).

## Development and Contributing

Interested in contributing? We ❤️ pull requests!

To make sure our community is safe for all, be sure to review and agree to our
[Code of Conduct](./.github/CODE_OF_CONDUCT.md). Then see the
[Contribution](./.github/CONTRIBUTING.md) guidelines for more information.

## Getting Help

We love to hear from you so if you have questions, comments or find a bug in the
project, let us know! You can either:

- [Open an issue in this repository](https://github.com/deepgram-devs/connect-demo-agent-skill/issues/new)
- [Join the Deepgram Github Discussions Community](https://github.com/orgs/deepgram/discussions)
- [Join the Deepgram Discord Community](https://discord.gg/xWRaCDBtW4)

[license]: LICENSE
