# antisycophancy

Three agents that exploit AI sycophancy instead of fighting it.

## How it works

AI agents are sycophantic: they try to please you. If you ask "find bugs," they'll find bugs that don't exist. If you ask "is this good?" they'll say yes. System prompts like "be critical" don't fix this.

This system makes sycophancy the mechanism, not the problem:

1. **Finder** is rewarded for finding issues (+1/+5/+10 by severity). It over-finds on purpose. This is the superset of all possible problems.
2. **Adversary** is rewarded for disproving issues, but penalized 2x if it's wrong. It aggressively filters but can't gamble. This is the subset of real problems.
3. **Referee** is told you already have the correct answers. It rules carefully because it thinks you're checking.

Three sycophants, three directions. The biases cancel out.

## Usage

Run them sequentially on anything: code, strategy docs, content, product specs.

```
Step 1: Give the Finder your material → get scored issue list
Step 2: Give the Adversary the issue list + original material → get filtered list
Step 3: Give the Referee both reports + original material → get final verdict
```

The Referee's confirmed issues are your action list.

### Claude Code

Drop the files into `.claude/agents/` and add YAML frontmatter to each:

```yaml
---
name: finder
description: Finds all possible issues in any material. Step 1 of antisycophancy pipeline.
tools: Read, Write
---
```

```yaml
---
name: adversary
description: Disproves issues from the finder. Step 2 of antisycophancy pipeline.
tools: Read, Write, WebSearch, WebFetch
---
```

```yaml
---
name: referee
description: Final verdict on disputed issues. Step 3 of antisycophancy pipeline.
tools: Read, Write
---
```

Then ask Claude to run them in sequence:

```
Review my-file.py using the finder agent, then the adversary agent,
then the referee agent. Save the final verdict.
```

Or invoke them manually one at a time for more control.

### Codex / OpenAI

Use each file as the system prompt for a separate agent. Chain them:

```
1. Create agent with finder.md as system prompt → send your material → save output
2. Create agent with adversary.md as system prompt → send finder output + original material → save output
3. Create agent with referee.md as system prompt → send both outputs + original material → get verdict
```

### Web interfaces and any other LLM (ChatGPT, Claude.ai, Gemini, etc.)

No CLI needed. These are just prompts. Three separate chat conversations:

```
Conversation 1: Paste finder.md as first message. Then paste your material. Save the response.
Conversation 2: Paste adversary.md as first message. Then paste the finder's response + your original material. Save the response.
Conversation 3: Paste referee.md as first message. Then paste both previous responses + your original material. The response is your verdict.
```

Start a new conversation for each step. This keeps context clean and prevents agents from seeing each other's reasoning.

Note: web interfaces don't support multi-agent pipelines, so you chain the steps manually. It takes ~5 minutes of copy-pasting but produces the same result. CLI tools (Claude Code, Codex) can automate the full sequence.

## Example

```
FINDER → 40 issues (6 critical, 16 medium, 18 low). Score: 158.
ADVERSARY → 13 disproved, 15 reduced, 8 upheld. 4 [FACT CHECK] claims verified via web search.
REFEREE → 8 confirmed, 15 dismissed, 17 downgraded. Action list: 8 real issues.
```

You inspect the 8. That's your todo list.

## Why it works

Every agent is sycophantic. That's the point.

The Finder wants to please you by finding everything. The Adversary wants to please you by disproving everything. The Referee wants to please you by being accurate. You're not fighting the bias. You're aiming it.
