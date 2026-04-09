# llm-code-analysis

Automated LLM-based code review using OpenAI, triggered via GitHub Actions on every pull request and push.
Part of a bachelor thesis comparing LLM code review quality against traditional static analysis tools.

## How it works

```
Pull Request / Push
       │
       ▼
GitHub Actions workflow (.github/workflows/llm-code-review.yml)
       │
       ├─ Computes git diff (PR base ↔ head  or  before ↔ after SHA)
       ├─ Sends the diff to the OpenAI Chat Completions API (gpt-4o by default)
       │
       ├─ Posts the feedback as a comment on the pull request  (PR events only)
       └─ Uploads a Markdown feedback file as a GitHub Actions artifact
```

## Setup

### 1. Add your OpenAI API key as a repository secret

1. Go to **Settings → Secrets and variables → Actions**.
2. Click **New repository secret**.
3. Name: `OPENAI_API_KEY`  
   Value: your OpenAI API key (starts with `sk-…`).

### 2. (Optional) Override the model

By default the workflow uses **`gpt-4o`**.  
To use a different model, add a repository *variable* (not a secret):

1. Go to **Settings → Secrets and variables → Actions → Variables**.
2. Click **New repository variable**.
3. Name: `OPENAI_MODEL`  
   Value: e.g. `gpt-4-turbo`, `gpt-3.5-turbo`, …

### 3. Required permissions

The workflow requests these permissions automatically:

| Permission | Reason |
|---|---|
| `contents: read` | Check out the repository |
| `pull-requests: write` | Post the review as a PR comment |
| `issues: write` | Required by the GitHub API for PR comments |

Make sure **Actions → General → Workflow permissions** in your repository settings
is set to *Read and write permissions* (or at minimum allows the two write scopes above).

## Artifacts

Every workflow run uploads a Markdown file to the **Artifacts** section of the run:

```
feedback/<event>_<sha>_<timestamp>.md
```

Artifacts are retained for **90 days** and can be downloaded from the
**Actions** tab of the repository for offline analysis and thesis data collection.

## Repository structure

```
.
├── .github/
│   └── workflows/
│       └── llm-code-review.yml   # GitHub Actions workflow
├── scripts/
│   └── analyze_code.py           # OpenAI integration & GitHub API helper
├── requirements.txt              # Python dependencies
└── README.md
```