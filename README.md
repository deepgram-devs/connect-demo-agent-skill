# Deepgram Connect Demo Agent Skill

A [Claude Code](https://claude.com/claude-code) skill that generates every deployment file needed
to stand up an **Amazon Connect AI Agent demo** for any industry vertical. Give it a one-line
scenario and it produces a complete, ready-to-deploy file set tailored to that use case.

## What it generates

For each demo, the skill creates a `{vertical}-demo/` folder containing:

| File | Purpose |
|------|---------|
| `demo-guide.md` | Presenter's reference — system prompt, demo script, sample data, and test cases |
| `{tool-name}_schema.json` | One per tool — uploaded to the Bedrock Agent Core gateway targets |
| `{vertical}_template.yaml` | CloudFormation template that deploys all the Lambda functions |
| `connect-system-prompt.yaml` | Pasted into the Connect AI Agent prompt editor |
| `connect-flow-template.json` | Imported into Connect as a contact flow |

## Repository layout

```text
SKILL.md                      # the skill definition Claude follows
references/
├── examples.md               # exact formats for every generated file
├── energy_template.yaml      # reference demo (energy vertical)
└── pharmacy_template.yaml     # reference demo (pharmacy vertical)
```

## Installation

Claude Code loads skills from `~/.claude/skills/`. Clone this repo and symlink it in:

```bash
git clone git@github.com:deepgram-devs/connect-demo-agent-skill.git
ln -s "$(pwd)/connect-demo-agent-skill" ~/.claude/skills/connect-demo-generator
```

The skill's name (`connect-demo-generator`) comes from the `name:` field in `SKILL.md`, so the
symlink directory name is your choice.

## Usage

Inside Claude Code, describe the demo you want in plain language. For example:

- `create a demo for an insurance use case — customer calling about a claim`
- `make me a banking call center demo where the customer wants to transfer funds`
- `I need a telecom demo for a subscriber upgrading their plan`

Claude detects the skill, designs 2–3 tools for the scenario (identity verification, data lookup,
and an action), invents realistic sample data, and writes the full file set into a new
`{vertical}-demo/` folder. You can then iterate — e.g. *"rename the customers"*, *"make the agent's
summary shorter"*, or *"add a third tool for scheduling a payment."*

### Deploying a generated demo

Once the files are generated:

1. Deploy `{vertical}_template.yaml` with CloudFormation (creates the Lambda functions).
2. Upload each `*_schema.json` to its Bedrock Agent Core gateway target.
3. Paste `connect-system-prompt.yaml` into the Connect AI Agent prompt editor and publish a version.
4. Import `connect-flow-template.json` as a contact flow and replace the placeholder ARNs.

See `references/examples.md` for the exact format of each file, and `demo-guide.md` (generated per
demo) for the scenario-specific script and test cases.

## Getting an API Key

🔑 To access the Deepgram API you will need a [free Deepgram API Key](https://console.deepgram.com/signup?jump=keys).

## Documentation

You can learn more about the Deepgram API at [developers.deepgram.com](https://developers.deepgram.com/docs).

## Development and Contributing

Interested in contributing? We ❤️ pull requests!

To make sure our community is safe for all, be sure to review and agree to our
[Code of Conduct](./CODE_OF_CONDUCT.md). Then see the
[Contribution](./CONTRIBUTING.md) guidelines for more information.

## Getting Help

We love to hear from you so if you have questions, comments or find a bug in the
project, let us know! You can either:

- [Open an issue in this repository](https://github.com/deepgram-devs/connect-demo-agent-skill/issues/new)
- [Join the Deepgram Github Discussions Community](https://github.com/orgs/deepgram/discussions)
- [Join the Deepgram Discord Community](https://discord.gg/xWRaCDBtW4)

[license]: LICENSE
