# CI Self-Heal Demo

A demonstration of GitHub Agentic Workflows that automatically detects, analyzes, and fixes CI test failures using AI.

## 🎯 Overview

This project showcases how GitHub Agentic Workflows can automatically respond to CI failures by:
1. Detecting when CI tests fail
2. Analyzing the failure logs
3. Identifying the root cause
4. Creating a fix
5. Opening a draft Pull Request with the solution

## 🤖 What are GitHub Agentic Workflows?

GitHub Agentic Workflows are AI-powered automation workflows that use natural language instructions to perform complex tasks. Unlike traditional GitHub Actions that follow rigid step-by-step scripts, agentic workflows:

- **Use AI reasoning** (powered by GitHub Copilot) to understand context and make decisions
- **Write instructions in markdown** instead of YAML
- **Adapt dynamically** to different situations
- **Interact with GitHub APIs** safely through controlled outputs
- **Run in sandboxed environments** with network restrictions for security

### How They Work

1. **Trigger**: Workflows are triggered by GitHub events (like a failed CI run)
2. **AI Agent**: GitHub Copilot analyzes the situation using the instructions you provide
3. **Tools**: The agent uses tools to read logs, create branches, and open PRs
4. **Safety**: All actions are validated and run through "safe outputs" to prevent unintended changes

## 📋 Prerequisites

- GitHub account with **GitHub Copilot Pro** or **Copilot Business** subscription
- Repository with GitHub Actions enabled
- Node.js project (this demo uses a simple JavaScript project)

## 🚀 Setup Instructions

### 1. Clone or Fork This Repository

```bash
git clone https://github.com/rajeevchandra/ci-self-heal-demo.git
cd ci-self-heal-demo
```

### 2. Install Dependencies

```bash
npm install
```

### 3. Configure GitHub Token

Create a Fine-Grained Personal Access Token with the following permissions:

**Go to:** https://github.com/settings/personal-access-tokens/new

**Repository permissions:**
- Contents: Read and write
- Pull requests: Read and write
- Workflows: Read and write

**Account permissions:**
- Copilot Requests: Read and write

**Add the token as a repository secret:**

```bash
gh secret set COPILOT_GITHUB_TOKEN
```

### 4. Enable Workflow Permissions

Go to your repository settings:
- **Settings** → **Actions** → **General** → **Workflow permissions**
- Select **"Read and write permissions"**
- Check **"Allow GitHub Actions to create and approve pull requests"**
- Click **Save**

### 5. Install gh-aw Extension (Optional for local testing)

```bash
gh extension install github/gh-aw
```

## 🧪 How to Run the Demo

### Trigger a CI Failure

The project intentionally contains a bug in `sum.js`:

```javascript
function sum(a, b) {
  return a - b; // <-- intentionally wrong (should be a + b)
}
```

To trigger the self-heal workflow:

```bash
git commit --allow-empty -m "Trigger CI test"
git push
```

### Watch the Magic Happen

1. **CI runs and fails** (test expects `sum(1, 2) = 3` but gets `-1`)
2. **Self-heal workflow triggers automatically**
3. **AI agent analyzes the failure logs**
4. **Agent identifies the bug** (subtraction instead of addition)
5. **Agent creates a fix** and pushes a new branch
6. **Agent opens a draft PR** with the solution

### Monitor Progress

```bash
# Watch workflow runs
gh run watch

# Check agent logs
gh aw logs

# List pull requests
gh pr list

```

## 📁 Project Structure

```
ci-self-heal-demo/
├── .github/
│   └── workflows/
│       ├── ci.yml                    # Standard CI workflow (runs tests)
│       ├── ci-self-heal.md           # Agentic workflow (AI instructions)
│       └── ci-self-heal.lock.yml     # Compiled workflow (auto-generated)
├── sum.js                            # Function with intentional bug
├── sum.test.js                       # Jest test that fails
├── package.json                      # Node.js configuration
└── README.md                         # This file
```

## 🔧 Workflow Configuration

The agentic workflow is defined in [.github/workflows/ci-self-heal.md](.github/workflows/ci-self-heal.md):

```markdown
---
name: ci-self-heal
description: Investigate failed CI runs and open a draft PR for safe fixes.

on:
  workflow_run:
    workflows: ["CI"]
    types: [completed]
    branches:
      - main

if: ${{ github.event.workflow_run.conclusion == 'failure' }}

permissions: read-all
timeout-minutes: 12
network: defaults

safe-outputs:
  create-pull-request:
    draft: true
  create-issue:
    title-prefix: "CI Failure Doctor"
---

# CI Failure Doctor

You respond to failed CI runs.

## What to do
1) Read the workflow run metadata and logs
2) Identify the real failing step and core error
3) If it's a simple fix, create a minimal patch and open a draft PR
4) Otherwise create an issue with details and suggestions
```

## 🎓 Key Concepts

### Safe Outputs

Agentic workflows use "safe outputs" to control what actions the AI can take:

- `create-pull-request`: AI can create PRs (automatically as drafts for review)
- `create-issue`: AI can create issues to report complex problems

### Network Restrictions

The workflow runs in a sandboxed environment with restricted network access for security.

### Copilot Engine

The workflow uses GitHub Copilot as the AI engine, which requires:
- Active Copilot subscription
- Fine-grained token with "Copilot Requests" permission

## 🐛 Troubleshooting

### "Authentication failed" Error

Ensure your `COPILOT_GITHUB_TOKEN` has:
- Valid Copilot subscription
- "Copilot Requests" account permission
- Not expired

### "Not permitted to create pull requests" Error

Enable in repository settings:
- Settings → Actions → General
- Check "Allow GitHub Actions to create and approve pull requests"

### Workflow Doesn't Trigger

Verify:
- CI workflow name matches `"CI"` in the trigger
- CI actually failed (not just warnings)
- Workflow is on the `main` branch

## 📚 Learn More

- [GitHub Agentic Workflows Documentation](https://github.github.com/gh-aw/)
- [GitHub Copilot](https://github.com/features/copilot)
- [gh-aw CLI Extension](https://github.com/github/gh-aw)

## 📝 License

ISC

## 🤝 Contributing

This is a demo project. Feel free to fork and experiment with your own agentic workflows!
