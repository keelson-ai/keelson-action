# Keelson GitHub Action

Automated AI agent security testing in your CI/CD pipeline. Runs [Keelson](https://github.com/keelson-ai/keelson) scans against your AI endpoints and uploads results to GitHub Code Scanning.

> **Authorized use only.** Only use this action against AI systems you own or have explicit written permission to test. See [Keelson LEGAL.md](https://github.com/keelson-ai/keelson/blob/main/LEGAL.md) for full terms.

## Quick Start

```yaml
# .github/workflows/ai-security.yml
name: AI Agent Security

on:
  push:
    branches: [main]
  pull_request:
  schedule:
    - cron: '0 6 * * 1'  # Weekly Monday 6am

jobs:
  security-scan:
    runs-on: ubuntu-latest
    permissions:
      security-events: write  # Required for SARIF upload
    steps:
      - uses: keelson-ai/keelson-action@v1
        with:
          target-url: ${{ vars.AGENT_ENDPOINT }}
          api-key: ${{ secrets.AGENT_API_KEY }}
```

## Inputs

| Input | Required | Default | Description |
|-------|----------|---------|-------------|
| `target-url` | **Yes** | — | AI agent endpoint URL |
| `api-key` | No | — | API key for target (use `${{ secrets.* }}`) |
| `model` | No | `default` | Model name |
| `adapter` | No | `openai` | Adapter: `openai`, `anthropic`, `langgraph`, `mcp`, `a2a`, `http` |
| `category` | No | all | Filter: `goal-adherence`, `tool-safety`, `memory-integrity`, etc. |
| `tier` | No | `fast` | `fast` (1 trial) or `deep` (10 trials per test) |
| `fail-on-vuln` | No | `true` | Fail the workflow step on vulnerabilities |
| `upload-sarif` | No | `true` | Upload SARIF to GitHub Code Scanning |
| `python-version` | No | `3.12` | Python version |
| `keelson-version` | No | latest | Pin a specific Keelson version |

## Outputs

| Output | Description |
|--------|-------------|
| `vulnerable-count` | Number of vulnerabilities found |
| `safe-count` | Number of safe results |
| `total-count` | Total tests run |
| `sarif-file` | Path to SARIF results file |

## Examples

### Deep scan on a specific category

```yaml
- uses: keelson-ai/keelson-action@v1
  with:
    target-url: ${{ vars.AGENT_ENDPOINT }}
    api-key: ${{ secrets.AGENT_API_KEY }}
    tier: deep
    category: permission-boundaries
```

### Non-blocking scan (report only, don't fail)

```yaml
- uses: keelson-ai/keelson-action@v1
  with:
    target-url: ${{ vars.AGENT_ENDPOINT }}
    api-key: ${{ secrets.AGENT_API_KEY }}
    fail-on-vuln: 'false'
```

### Anthropic adapter

```yaml
- uses: keelson-ai/keelson-action@v1
  with:
    target-url: https://api.anthropic.com/v1/messages
    api-key: ${{ secrets.ANTHROPIC_API_KEY }}
    adapter: anthropic
    model: claude-sonnet-4-20250514
```

## How It Works

1. Installs Python and `keelson-ai` from PyPI
2. Runs a security scan against your AI agent endpoint
3. Generates a SARIF report and uploads to GitHub Code Scanning
4. Uploads full scan results as a workflow artifact
5. Optionally fails the workflow if vulnerabilities are found

Results appear in the **Security** tab of your repository under **Code Scanning**.

## License

Copyright 2026 Othentic Labs LTD. Apache License 2.0.
