# 🤖 AI Code Review

Automatically reviews GitHub pull requests using Claude AI and LangChain.

Use as a **reusable GitHub workflow** or run manually as a **Python script**.

## Usage

### Add to your repository

Create `.github/workflows/ai-review.yml` in your repository:

```yaml
name: AI Code Review

on:
  pull_request:
    types: [opened, synchronize]

jobs:
  ai-review:
    uses: njrenaissance/pr-review-agent/.github/workflows/ai-code-review.yml@main
    secrets:
      anthropic-api-key: ${{ secrets.ANTHROPIC_API_KEY }}
```

Or with a custom model:

```yaml
jobs:
  ai-review:
    uses: njrenaissance/pr-review-agent/.github/workflows/ai-code-review.yml@main
    secrets:
      anthropic-api-key: ${{ secrets.ANTHROPIC_API_KEY }}
    with:
      model: claude-opus-4-7
```

### Setup

1. **Add the secret** to your repository
   - Go to **Settings → Secrets and variables → Actions**
   - Create `ANTHROPIC_API_KEY` with your [Anthropic API key](https://console.anthropic.com)
   - `GITHUB_TOKEN` is provided automatically by GitHub Actions

2. **The workflow runs automatically** on:
   - New pull requests
   - Updates to pull requests (new commits)

3. **The bot posts a review** as a comment on the PR within ~30 seconds

---

## Review Format

Every review includes:

| Section | Content |
| --- | --- |
| **Summary** | Overview of what the PR does |
| **Issues Found** | Bugs, security concerns, patterns — with severity (🔴 Critical / 🟠 High / 🟡 Medium / 🟢 Low) |
| **Code Quality** | Readability, naming, structure, test coverage, best practices |
| **Positive Highlights** | What was done well |
| **Verdict** | ✅ Approve / 🔄 Request Changes / 💬 Comment Only |

---

## Local Usage

### Install

```bash
uv sync
```

### Run

```bash
# Review a public PR (no token needed)
python pr_review_agent.py owner/repo 42

# Review a private PR (requires GITHUB_TOKEN)
export GITHUB_TOKEN="ghp_..."
python pr_review_agent.py owner/repo 42

# Save review to file
python pr_review_agent.py owner/repo 42 --output review.md

# Post review as GitHub comment
export ANTHROPIC_API_KEY="sk-ant-..."
python pr_review_agent.py owner/repo 42 --post
```

### Arguments

```text
usage: pr_review_agent.py [-h] [--post] [--output FILE] repo pr_number

positional arguments:
  repo              owner/repo (e.g. octocat/Hello-World)
  pr_number         Pull request number

optional arguments:
  --post            Post review as a GitHub comment on the PR
  --output FILE     Save review to a markdown file
```

---

## Reusable Workflow Inputs

When calling the workflow, configure these:

| Name | Type | Required | Default | Purpose |
| --- | --- | --- | --- | --- |
| `anthropic-api-key` | secret | Yes | — | Your Anthropic API key |
| `model` | input | No | `claude-sonnet-4-6` | Claude model (e.g., `claude-opus-4-7`, `claude-haiku-4-5`) |

Example with custom model:

```yaml
jobs:
  ai-review:
    uses: njrenaissance/pr-review-agent/.github/workflows/ai-code-review.yml@main
    secrets:
      anthropic-api-key: ${{ secrets.ANTHROPIC_API_KEY }}
    with:
      model: claude-opus-4-7
```

---

## Local Configuration

Edit constants in `pr_review_agent.py` or set environment variables:

| Variable | Default | Purpose |
| --- | --- | --- |
| `MODEL_NAME` | `claude-sonnet-4-6` | Claude model to use |
| `MAX_DIFF_CHARS` | `12000` | Truncate large diffs to fit in context |

Example:

```bash
export MODEL_NAME=claude-opus-4-7
python pr_review_agent.py owner/repo 42
```

---

## How it works

1. **Fetches PR metadata** from GitHub (title, description, files, diff)
2. **Sends to Claude** with a structured review prompt
3. **Parses the response** into sections (issues, quality, verdict)
4. **Posts or saves** the review

Works with:

- ✅ Public and private repositories
- ✅ Any language/framework
- ✅ Large diffs (intelligently truncated)
- ✅ Draft PRs (skipped by default)

---

## Development

### Requirements

- Python 3.13+
- `uv` for dependency management

### Setup

```bash
git clone https://github.com/njrenaissance/pr-review-agent
cd pr-review-agent
uv sync

# Install dev dependencies (optional)
uv sync --group dev
```

### Test locally

```bash
export ANTHROPIC_API_KEY="sk-ant-..."
python pr_review_agent.py your-org/test-repo 1
```

### Contributing

PRs welcome! This workflow is designed to be straightforward and easy to customize:

- Modify `SYSTEM_PROMPT.md` to change review style
- Add new output formats or integrations
- Keep it simple — the value is in the reusability
