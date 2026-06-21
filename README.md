> [!NOTE]
> **This skill has moved.** It now ships as the `codex-review` package in the Netcracker APM
> marketplace:
> [Netcracker/qubership-ai-packages → agent-packages/codex-review](https://github.com/Netcracker/qubership-ai-packages/tree/main/agent-packages/codex-review).
> This repository is archived and read-only; further work happens there.

# codex-review

A Claude Code skill that runs cross-review of your code changes using [Codex CLI](https://github.com/openai/codex) (GPT-powered), then automatically fixes the issues found — all without leaving your editor.

## How it works

1. You invoke the skill with a review target (branch diff, uncommitted changes, or a specific commit)
2. Codex CLI reviews the code and produces findings
3. Claude classifies each finding (valid vs false positive) and applies fixes
4. The cycle repeats (up to 3 iterations) until the review is clean

## Usage

```
/codex-review --base main
/codex-review --uncommitted
/codex-review --commit abc1234
```

## Review modes

When invoked, the skill asks you to choose a mode:

- **Automatic** — Claude decides what to fix and what to skip, shows a summary table before applying changes
- **Interactive** — for each finding, you choose: fix as suggested, skip, or fix differently

## Installation

### Via Tessl Registry

```bash
tessl install vlsi/codex-review
```

### Manual

Copy `SKILL.md` to `.claude/commands/codex-review.md` in your project.

## Prerequisites

- [Codex CLI](https://github.com/openai/codex) installed and configured
- [Claude Code](https://docs.anthropic.com/en/docs/claude-code) CLI
