# Usage Guide: Implementing GitHub Repository Rulesets

This guide explains how to apply the rulesets from this repository to your GitHub repositories.

## Table of Contents

1. [Prerequisites](#prerequisites)
2. [Method 1: GitHub Web Interface](#method-1-github-web-interface)
3. [Method 2: GitHub CLI](#method-2-github-cli)
4. [Method 3: GitHub API](#method-3-github-api)
5. [Customizing Rulesets](#customizing-rulesets)
6. [Testing Rulesets](#testing-rulesets)
7. [Troubleshooting](#troubleshooting)

## Prerequisites

- Repository admin access
- GitHub Enterprise, Team, or Free/Pro with branch protection
- (Optional) GitHub CLI installed for CLI method
- (Optional) API access token for API method

## Method 1: GitHub Web Interface

### Step 1: Navigate to Repository Settings
1. Go to your repository on GitHub
2. Click **Settings** tab
3. Select **Rules** → **Rulesets** from the left sidebar

### Step 2: Create New Ruleset
1. Click **New ruleset** → **New branch ruleset**
2. Name your ruleset (e.g., "main-branch-protection")

### Step 3: Configure Ruleset
1. Set **Enforcement status** to "Active"
2. Under **Target branches**, add your branch patterns (e.g., `main`, `release/*`)
3. Scroll through available rules and enable/configure as shown in the JSON

### Step 4: Map JSON to UI Settings

**Pull Request Settings:**
```json
"type": "pull_request",
"parameters": {
  "required_approving_review_count": 2,
  "require_code_owner_review": true
}
```
→ Enable "Require a pull request before merging"
→ Set "Required approvals" to 2
→ Check "Require review from Code Owners"

**Status Checks:**
```json
"type": "required_status_checks",
"parameters": {
  "required_status_checks": ["build", "test"]
}
```
→ Enable "Require status checks to pass"
→ Search and add: build, test

**Commit Message Pattern:**
```json
"type": "commit_message_pattern",
"parameters": {
  "pattern": "^(feat|fix|docs).*"
}
```
→ Enable "Require commit message to match pattern"
→ Enter pattern in regex field

## Method 2: GitHub CLI

### Installation
```bash
# Install GitHub CLI (if not already installed)
# macOS
brew install gh

# Windows
winget install --id GitHub.cli

# Linux
sudo apt install gh  # Debian/Ubuntu
```

### Authenticate
```bash
gh auth login
```

### Create Ruleset from JSON

Unfortunately, GitHub CLI doesn't directly support importing rulesets from JSON files yet. However, you can use it to manage existing rulesets:

```bash
# List current rulesets
gh api repos/{owner}/{repo}/rulesets

# View specific ruleset
gh api repos/{owner}/{repo}/rulesets/{ruleset_id}
```

For now, use Method 3 (API) for JSON import.

## Method 3: GitHub API

### Prerequisites
```bash
# Set your GitHub token
export GITHUB_TOKEN="your_personal_access_token"
```

Your token needs `repo` scope for private repositories or `public_repo` for public ones.

### Create Ruleset from JSON File

```bash
# Using curl
curl -X POST \
  -H "Accept: application/vnd.github+json" \
  -H "Authorization: Bearer $GITHUB_TOKEN" \
  -H "X-GitHub-Api-Version: 2022-11-28" \
  https://api.github.com/repos/OWNER/REPO/rulesets \
  -d @rulesets/backend/lambda.json
```

### Using a Script

Create `apply-ruleset.sh`:

```bash
#!/bin/bash

OWNER="your-username"
REPO="your-repo"
RULESET_FILE="$1"

if [ -z "$RULESET_FILE" ]; then
  echo "Usage: ./apply-ruleset.sh <path-to-ruleset.json>"
  exit 1
fi

if [ -z "$GITHUB_TOKEN" ]; then
  echo "Error: GITHUB_TOKEN environment variable not set"
  exit 1
fi

curl -X POST \
  -H "Accept: application/vnd.github+json" \
  -H "Authorization: Bearer $GITHUB_TOKEN" \
  -H "X-GitHub-Api-Version: 2022-11-28" \
  https://api.github.com/repos/$OWNER/$REPO/rulesets \
  -d @"$RULESET_FILE"
```

Usage:
```bash
chmod +x apply-ruleset.sh
./apply-ruleset.sh rulesets/backend/lambda.json
```

## Customizing Rulesets

### 1. Choose a Base Template
Select from:
- `examples/basic-protection.json` - Minimal protection
- `examples/open-source.json` - Community projects
- `examples/enterprise-strict.json` - Maximum security
- Or any category-specific ruleset from `rulesets/`

### 2. Modify for Your Needs

```json
{
  "name": "my-custom-ruleset",  // ← Change name
  "target": "branch",
  "enforcement": "active",  // or "evaluate" for testing
  "conditions": {
    "ref_name": { 
      "include": ["main", "develop"],  // ← Your branches
      "exclude": ["sandbox/*"]
    }
  },
  "rules": [
    // Add, remove, or modify rules as needed
  ]
}
```

### 3. Common Modifications

**Adjust Review Count:**
```json
"required_approving_review_count": 1  // Change to 1, 2, 3, etc.
```

**Add/Remove Status Checks:**
```json
"required_status_checks": [
  "build",
  "test",
  "your-custom-check"  // Add your checks
]
```

**Modify Commit Pattern:**
```json
"pattern": "^(feat|fix|docs|custom-type).*"  // Add your types
```

## Testing Rulesets

### 1. Use Evaluation Mode
Set `"enforcement": "evaluate"` to test without blocking:

```json
{
  "enforcement": "evaluate",  // Logs violations but doesn't block
  ...
}
```

### 2. Test on a Feature Branch
Apply the ruleset to a test branch first:

```json
"conditions": {
  "ref_name": { "include": ["test-protection"] }
}
```

### 3. Verify Rules
1. Create a test PR that violates rules
2. Check that appropriate errors appear
3. Verify legitimate PRs can merge
4. Review audit logs in Settings → Rules → Rulesets

## Troubleshooting

### Rule Not Working
- **Check enforcement status**: Must be "active"
- **Verify branch matching**: Ensure branch name matches pattern
- **Status checks**: Check exact naming (case-sensitive)
- **Permissions**: Ensure you have admin access

### API Errors

**401 Unauthorized**
```bash
# Check token has correct scopes
gh auth status

# Refresh authentication
gh auth refresh -s repo
```

**422 Validation Failed**
- Validate JSON syntax: `cat ruleset.json | jq`
- Check all required fields are present
- Verify rule parameter formats match schema

### Performance Issues
- Limit status checks to essential ones
- Use `"strict_required_status_checks_policy": false` for flexibility
- Avoid overly complex regex patterns

### Bypass Options

Organization owners and repository admins can:
1. Settings → Rules → Rulesets
2. Click ruleset name
3. Add bypass actors (users, teams, apps)

```json
"bypass_actors": [
  {
    "actor_id": 1,
    "actor_type": "Team",
    "bypass_mode": "always"
  }
]
```

## Best Practices

1. **Start simple** - Begin with basic protection, add rules gradually
2. **Test first** - Use evaluate mode before enforcing
3. **Document custom rules** - Add metadata explaining your choices
4. **Version your rulesets** - Increment `metadata.version` when updating
5. **Review regularly** - Audit effectiveness quarterly
6. **Team alignment** - Ensure team understands the rules
7. **Exceptions process** - Define how to request bypasses

## Additional Resources

- [GitHub Rulesets Documentation](https://docs.github.com/en/repositories/configuring-branches-and-merges-in-your-repository/managing-rulesets)
- [Repository Rules API](https://docs.github.com/en/rest/repos/rules)
- [Schema Reference](../schema/ruleset-schema.json)
- [Conventional Commits](https://www.conventionalcommits.org/)

## Getting Help

- Check [GitHub Community Discussions](https://github.com/orgs/community/discussions)
- Review [GitHub Status](https://www.githubstatus.com/) for API issues
- File issues in this repository for ruleset-specific questions
