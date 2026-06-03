# superpowers-opencode (patcher)

Automated fork of [obra/superpowers](https://github.com/obra/superpowers) with patches applied for OpenCode-specific features.

Based on [AlexMKX/superpowers-opencode](https://github.com/AlexMKX/superpowers-opencode) with additional enhancements.

## What this does

Applies the following patches on top of upstream superpowers releases:

### Agent registration in plugin

The superpowers plugin registers dedicated agents with appropriate model tiers via the `config` hook:

| Agent | Role | Default Model |
|-------|------|---------------|
| `@implementer-sp` | Implements tasks from plan, writes code, tests, commits | Sonnet |
| `@spec-reviewer-sp` | Verifies implementation matches spec | Sonnet |
| `@code-reviewer-sp` | Deep code review — architecture, quality, security | Opus |

On first startup, Superpowers scaffolds `~/.config/opencode/superpowers.jsonc`. Edit agent models there:

```json
{
  "agent": {
    "implementer-sp": { "model": "anthropic/claude-haiku-4-5" }
  }
}
```

### Subagent routing

All skill prompt templates route dispatches to named agents (`@implementer-sp`, `@code-reviewer-sp`) with soft fallback to `general-purpose` when agents aren't configured.

### Architecture review criterion

Adds "Architecture" to the spec document reviewer checklist: units with clear boundaries, well-defined interfaces, independently understandable and testable.

### Enhanced subagent-driven development

Updated SDD skill with agent-aware graphviz diagrams and instructions.

## Installation

In `~/.config/opencode/opencode.json`:

**Option A — always current (recommended):**

```json
{
  "plugin": [
    "superpowers@git+https://github.com/Arsolitt/superpowers-opencode.git#latest"
  ]
}
```

The `latest` tag is moved to the newest release on every workflow run.

**Option B — pin to a specific release:**

```json
{
  "plugin": [
    "superpowers@git+https://github.com/Arsolitt/superpowers-opencode.git#v5.1.0-202606030000"
  ]
}
```

Pick a version from [Releases](https://github.com/Arsolitt/superpowers-opencode/releases).

**Agent config (same for both options):**

Models can be overridden in `~/.config/opencode/superpowers.jsonc` (scaffolded on first run).

## How it works

The CI workflow (`release.yml`) clones the upstream `obra/superpowers` at a given tag, applies all patches from `patches/*.patch` in order, and publishes the result as an orphan branch + tag + GitHub Release.

Version format: `{upstream_tag}-{YYYYMMDDHHMM}` (UTC).

## Updating

To create a new release targeting the latest upstream tag, push changes to `patches/` or manually trigger the workflow.
