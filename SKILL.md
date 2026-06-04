---
name: connect-demo-generator
description: >
  Generate all deployment files for an Amazon Connect AI Agent demo from a brief scenario description.
  Use this skill whenever the user wants to create a new Connect demo, build a demo for a vertical/use case,
  generate Connect AI agent files, or mentions "Connect demo" in any context. Also trigger when the user
  asks to create demo tools, Lambda functions for a call center scenario, or Bedrock Agent Core gateway tools
  for a customer service use case. Even if they just say something like "make me a demo for insurance" or
  "I need a banking call center demo" — this is the skill to use.
---

# Connect AI Agent Demo Generator

You generate all the deployment files needed to stand up an Amazon Connect AI Agent demo for any industry vertical. The user gives you a brief description like "create a demo for an insurance use case — customer calling about a claim" and you produce a complete, ready-to-deploy file set.

## What you're building

Amazon Connect AI Agents use tools backed by Lambda functions, exposed through a Bedrock Agent Core MCP gateway. For each demo, you need to generate:

1. **Demo guide** (`demo-guide.md`) — the presenter's reference document
2. **Schema JSON files** (one per tool) — uploaded to Bedrock Agent Core gateway targets
3. **CloudFormation template** (`{vertical}_template.yaml`) — deploys all Lambda functions
4. **Connect system prompt** (`connect-system-prompt.yaml`) — pasted into the AI Agent prompt editor
5. **Connect flow template** (`connect-flow-template.json`) — imported into Connect as a contact flow

## Step-by-step process

### 1. Understand the scenario

From the user's description, determine:
- **Company name** — invent a realistic one if not provided (e.g., "Summit Energy", "Horizon Insurance")
- **Vertical** — the industry (energy, insurance, banking, healthcare, travel, etc.)
- **Customer scenario** — what the caller is trying to accomplish
- **Tools needed** — 2-3 tools that cover: identity verification, data lookup, and an action. Think about what a real customer service agent would need access to for this scenario.

If the user's description is vague, make reasonable creative decisions rather than asking a bunch of questions. You can always iterate.

### 2. Read the reference examples

Read `references/examples.md` in this skill's directory. It contains the exact formats for every file type, derived from the working pharmacy and energy demos. Follow these formats precisely — they've been validated against the actual AWS services.

Also read these existing demos in the project root for inspiration on data patterns and Lambda code structure:
- `references/energy_template.yaml`
- `references/pharmacy_template.yaml`

### 3. Design the tools

Every demo needs 2-3 tools. Common patterns:

| Pattern | Tool 1 (Auth/Lookup) | Tool 2 (Detail) | Tool 3 (Action) |
|---------|---------------------|-----------------|-----------------|
| Energy | account-lookup | usage-history | create-payment-plan |
| Pharmacy | authenticate-user | get-prescriptions | — |
| Insurance | policy-lookup | get-claims | file-claim |
| Banking | account-verify | transaction-history | transfer-funds |
| Travel | booking-lookup | flight-status | change-booking |
| Telecom | subscriber-lookup | plan-details | upgrade-plan |

For each tool, decide:
- **Name** (hyphenated, alphanumeric only — Bedrock enforces `([0-9a-zA-Z][-]?){1,100}`)
- **Input parameters** and their types
- **What it returns** — keep response payloads concise since the LLM reasons over them
- **Hard-coded sample data** — 3-5 records with realistic values

### 4. Generate all files

Create a subfolder in the project root named `{vertical}-demo` (e.g., `insurance-demo`).

Generate each file following the exact formats in `references/examples.md`:

#### Schema JSON files
- One file per tool: `{tool-name}_schema.json`
- Tool names use hyphens in the schema `name` field
- Keep descriptions clear — the LLM uses these to decide when to call the tool

#### CloudFormation template (`{vertical}_template.yaml`)
- `LambdaExecutionRole` named `{Vertical}-LambdaExecutionRole`
- Lambda `FunctionName` uses underscores: `{vertical}_{tool_name}`
- `GatewayLambdaInvokePolicy` named `AgentCoreGatewayLambdaInvoke-{Vertical}`
- All data hard-coded as Python dicts inside the Lambda ZipFile
- Lambda code pattern: validate required fields → lookup from dict → return result or error
- Include `import traceback` in the first Lambda (auth/lookup tool)

#### Connect system prompt (`connect-system-prompt.yaml`)
- Concise, scenario-specific — NOT the giant generic template
- Include the call flow (greet → verify → lookup → help)
- Mention what's out of scope and should be transferred
- Include the formatting requirements (`<message>` and `<thinking>` tags)
- Include core behavior, security rules, tool instructions placeholder, and system variables

#### Connect flow template (`connect-flow-template.json`)
- Based on the energy demo flow structure
- Use placeholder ARNs: `REPLACE_WITH_ASSISTANT_ARN`, `REPLACE_WITH_AI_AGENT_VERSION_ARN`, `REPLACE_WITH_TTS_SECRET_ARN`, `REPLACE_WITH_LEX_BOT_ALIAS_ARN`
- Set the greeting text to match the demo scenario
- Name the flow `{Company} {Vertical} Demo Flow`

#### Demo guide (`demo-guide.md`)
- Agent system prompt (the natural language part)
- Step-by-step demo script showing the conversation flow
- Sample data JSON for each tool showing what gets returned
- Test cases table: customer name, account/ID, verification details, expected scenario

### 5. Present the results

After generating all files, give the user a summary:
- List all generated files and what each one is for
- Highlight the primary demo customer (the one in the demo script)
- Mention key test cases they can try
- Point them to your team's Connect demo setup guide for the deploy-and-configure steps

## Important constraints

- **Tool names in schemas**: hyphens only, no underscores (Bedrock Agent Core validation)
- **Lambda function names in CloudFormation**: underscores are fine (AWS resource names)
- **Lambda code**: simple hard-coded dict lookups — no DynamoDB, no S3, no external calls. This gives the lowest possible latency for demos.
- **Sample data**: use realistic but obviously fake data (realistic names, addresses, amounts — but clearly not real people)
- **System prompt**: keep it short and specific. The generic Connect template is 200+ lines — yours should be under 60 lines of actual instruction.
- **3-5 sample customers**: enough variety to demo different scenarios (e.g., one with unpaid balance, one paid up, one with special circumstances)
