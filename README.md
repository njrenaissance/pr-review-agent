# 🤖 PR Code Review Agent

Automatically reviews GitHub pull requests using LangChain + Claude.

## Quick Start

### 1. Install dependencies

```bash
pip install -r requirements.txt
```

### 2. Set environment variables

```bash
export ANTHROPIC_API_KEY="sk-ant-..."
export GITHUB_TOKEN="ghp_..."        # needs repo + pull_requests:read scope
```

### 3. Run a review

```bash
# Print review to terminal
python pr_review_agent.py octocat/Hello-World 42

# Save review to a markdown file
python pr_review_agent.py octocat/Hello-World 42 --output review.md

# Post review as a GitHub comment on the PR
python pr_review_agent.py octocat/Hello-World 42 --post
```

---

## GitHub Actions (automatic reviews on every PR)

1. Copy `.github/workflows/ai-code-review.yml` into your target repo.
2. Add `ANTHROPIC_API_KEY` as a repository secret  
   _(GitHub repo → Settings → Secrets → Actions → New repository secret)_
3. `GITHUB_TOKEN` is provided automatically by Actions — no extra setup needed.
4. Open (or update) any PR — the bot will post a structured review within ~30 seconds.

---

## Review format

Every review contains:

| Section | What it covers |
| --- | --- |
| **Summary** | What the PR does |
| **Issues Found** | Bugs, security holes, bad patterns — with severity |
| **Code Quality** | Readability, naming, tests, best practices |
| **Positive Highlights** | What was done well |
| **Verdict** | Approve / Request Changes / Comment, with justification |

---

## Configuration

Edit the constants at the top of `pr_review_agent.py`:

| Constant | Default | Purpose |
| --- | --- | --- |
| `MODEL_NAME` | `claude-sonnet-4-20250514` | Claude model to use |
| `MAX_DIFF_CHARS` | `12000` | Truncation limit for large diffs |

---

## GitHub Token scopes required

| Scope | Why |
| --- | --- |
| `repo` (or `public_repo`) | Read PR diff and metadata |
| `pull-requests: write` | Post review comment (only if using `--post`) |