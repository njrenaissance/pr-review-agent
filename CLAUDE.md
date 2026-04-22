# CLAUDE.md

## Project overview

PR Code Review Agent — automatically reviews GitHub pull requests
using LangChain + Claude (Anthropic). Single-file Python tool that
can run from CLI or as a GitHub Action.

## Tech stack

- Python 3.11+
- LangChain + langchain-anthropic for LLM orchestration
- PyGithub for GitHub API access
- Claude Sonnet as the review model

## Key files

- `pr_review_agent.py` — main application
  (prompt, LangChain chain, GitHub helpers, CLI)
- `requirements.txt` — Python dependencies

## Environment variables

- `ANTHROPIC_API_KEY` — required, Anthropic API key
- `GITHUB_TOKEN` — required, GitHub personal access token
  (needs `repo` + `pull_requests:read` scope;
  `pull_requests:write` if using `--post`)

## Running

```bash
pip install -r requirements.txt
python pr_review_agent.py <owner/repo> <pr_number> [--post] [--output FILE]
```

## Configuration

Constants at the top of `pr_review_agent.py`:

- `MODEL_NAME` — Claude model to use (default: `claude-sonnet-4-20250514`)
- `MAX_DIFF_CHARS` — truncation limit for large diffs (default: 12000)