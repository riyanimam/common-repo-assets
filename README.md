# Common Repository Assets

A curated collection of GitHub repository rulesets and branch protection configurations for various project types. These reusable configurations help enforce best practices, security standards, and consistent workflows across your repositories.

## 📋 Table of Contents

- [Overview](#overview)
- [Repository Structure](#repository-structure)
- [Quick Start](#quick-start)
- [Available Rulesets](#available-rulesets)
- [Usage](#usage)
- [Customization](#customization)
- [Contributing](#contributing)
- [License](#license)

## 🎯 Overview

This repository provides pre-configured GitHub rulesets for different project types, helping you quickly establish robust protection rules without starting from scratch. Each ruleset is designed with industry best practices and can be easily customized for your specific needs.

### What are Repository Rulesets?

GitHub repository rulesets allow you to enforce branch protection rules, commit requirements, and other policies across your repositories. They help maintain code quality, security, and consistent development practices.

### Why Use This Repository?

- ✅ **Save time** - Pre-configured rulesets ready to use
- ✅ **Best practices** - Based on industry standards and security guidelines
- ✅ **Consistent** - Standardize protection across multiple repositories
- ✅ **Customizable** - Easily modify to fit your specific needs
- ✅ **Well-documented** - Comprehensive guides and examples
- ✅ **Version controlled** - Track changes and maintain configurations as code

## 📁 Repository Structure

```
common-repo-assets/
├── rulesets/                    # Categorized ruleset configurations
│   ├── backend/                 # Backend service rulesets
│   │   └── lambda.json         # AWS Lambda backend protection
│   ├── bots/                    # Bot application rulesets
│   │   ├── discord-bot.json    # Discord bot with Go testing
│   │   └── signal-bot.json     # Signal bot with secret protection
│   ├── infrastructure/          # Infrastructure-as-Code rulesets
│   │   └── terraform-module.json # Terraform module protection
│   ├── web/                     # Web application rulesets
│   │   ├── documentation-site.json # Documentation sites
│   │   └── static-sites.json   # Static website protection
│   └── tools/                   # Utility and script rulesets
│       └── tools-and-scripts.json # General tools protection
├── examples/                    # Example configurations and templates
│   ├── basic-protection.json   # Minimal protection example
│   ├── enterprise-strict.json  # High-security example
│   ├── open-source.json        # Open-source project example
│   ├── usage-guide.md          # Detailed implementation guide
│   └── README.md               # Examples documentation
├── schema/                      # JSON schema for validation
│   └── ruleset-schema.json     # Ruleset configuration schema
├── .editorconfig               # Editor configuration
├── .gitattributes              # Git attributes for consistency
├── LICENSE                     # License information
└── README.md                   # This file
```

## 🚀 Quick Start

### 1. Choose a Ruleset

Browse the [rulesets](rulesets) directory and select a configuration that matches your project type:

```bash
# For a Lambda backend
rulesets/backend/lambda.json

# For a Discord bot
rulesets/bots/discord-bot.json

# For a Terraform module
rulesets/infrastructure/terraform-module.json
```

### 2. Review and Customize

Open the selected JSON file and adjust:
- `name` - Set a unique name for your ruleset
- `conditions.ref_name.include` - Specify your branch names
- `rules` - Add, remove, or modify rules as needed

### 3. Apply to Your Repository

**Option A: GitHub Web Interface**
1. Navigate to Repository Settings → Rules → Rulesets
2. Create new ruleset and configure using the JSON as a guide

**Option B: GitHub API**
```bash
curl -X POST \
  -H "Authorization: Bearer $GITHUB_TOKEN" \
  -H "Accept: application/vnd.github+json" \
  https://api.github.com/repos/OWNER/REPO/rulesets \
  -d @rulesets/backend/lambda.json
```

For detailed instructions, see [examples/usage-guide.md](examples/usage-guide.md).

## 📚 Available Rulesets

### Backend Services

#### [Lambda Backend](rulesets/backend/lambda.json)
**Use case:** AWS Lambda serverless backends  
**Protection level:** Strict  
**Features:**
- 2 required approvals with code owner review
- Build, lint, and test status checks
- Conventional commit messages
- Force push prevention

### Bot Applications

#### [Discord Bot](rulesets/bots/discord-bot.json)
**Use case:** Discord bot applications  
**Protection level:** Moderate  
**Features:**
- Go test and vet requirements
- Secret file restrictions
- Conventional commits
- Last push approval

#### [Signal Bot](rulesets/bots/signal-bot.json)
**Use case:** Signal messaging bots  
**Protection level:** Lightweight  
**Features:**
- Basic commit message hygiene
- Comprehensive secret file protection
- Single approval requirement

### Infrastructure

#### [Terraform Module](rulesets/infrastructure/terraform-module.json)
**Use case:** Terraform infrastructure modules  
**Protection level:** Strict  
**Features:**
- 2 required approvals with code owners
- fmt, validate, and plan checks
- Release branch protection
- Conventional commits

### Web Applications

#### [Documentation Site](rulesets/web/documentation-site.json)
**Use case:** Documentation websites  
**Protection level:** Moderate  
**Features:**
- Docs build verification
- Documentation-focused commit patterns
- Last push approval

#### [Static Sites](rulesets/web/static-sites.json)
**Use case:** Static websites and portfolios  
**Protection level:** Flexible  
**Features:**
- Build and test requirements
- Single approval
- Flexible status checks

### Tools & Scripts

#### [Tools and Scripts](rulesets/tools/tools-and-scripts.json)
**Use case:** Utility tools and automation scripts  
**Protection level:** Standard  
**Features:**
- Lint and test requirements
- Single approval
- Main and dev branch protection

## 💡 Usage

### Basic Example

```json
{
  "name": "my-project-protection",
  "target": "branch",
  "enforcement": "active",
  "conditions": {
    "ref_name": { "include": ["main"], "exclude": [] }
  },
  "rules": [
    {
      "type": "pull_request",
      "parameters": {
        "required_approving_review_count": 1
      }
    },
    {
      "type": "non_fast_forward"
    }
  ]
}
```

### Rule Types Reference

Common rule types included in our rulesets:

- **pull_request** - Require PR reviews before merging
- **required_status_checks** - Require CI/CD checks to pass
- **non_fast_forward** - Prevent force pushes
- **commit_message_pattern** - Enforce commit message formats
- **file_path_restriction** - Block sensitive file commits
- **required_signatures** - Require signed commits
- **creation** / **deletion** - Control branch creation/deletion

See [schema/ruleset-schema.json](schema/ruleset-schema.json) for complete schema definition.

## 🔧 Customization

### Metadata

All rulesets include metadata for better organization:

```json
{
  "metadata": {
    "version": "1.0.0",
    "created": "2026-02-05",
    "updated": "2026-02-05",
    "description": "Protection rules for...",
    "tags": ["backend", "lambda", "aws"]
  }
}
```

### Common Customizations

**Adjust approval count:**
```json
"required_approving_review_count": 2  // Change to 1, 2, 3, etc.
```

**Add custom status checks:**
```json
"required_status_checks": [
  "build",
  "test",
  "my-custom-check"  // Add your checks
]
```

**Modify commit patterns:**
```json
"pattern": "^(feat|fix|docs|custom)(\\(.+\\))?: .+"
```

**Add more branches:**
```json
"ref_name": { "include": ["main", "develop", "staging"] }
```

## 🧪 Testing Rulesets

Before enforcing rules, test them in evaluation mode:

```json
{
  "enforcement": "evaluate"  // Logs violations without blocking
}
```

Or apply to a test branch first:

```json
{
  "conditions": {
    "ref_name": { "include": ["test-protection"] }
  }
}
```

## 📖 Documentation

- **[Usage Guide](examples/usage-guide.md)** - Detailed implementation instructions
- **[Examples](examples/)** - Sample configurations and templates
- **[Schema](schema/ruleset-schema.json)** - JSON schema for validation

## 🤝 Contributing

Contributions are welcome! To add a new ruleset or improve existing ones:

1. Fork this repository
2. Create a feature branch
3. Add your ruleset to the appropriate category
4. Include metadata with description and tags
5. Update documentation as needed
6. Submit a pull request

### Ruleset Guidelines

- Use descriptive names
- Include comprehensive metadata
- Follow the schema structure
- Add comments for complex rules
- Test before submitting

## 📄 License

This project is licensed under the MIT License - see the [LICENSE](LICENSE) file for details.

## 🔗 Additional Resources

- [GitHub Rulesets Documentation](https://docs.github.com/en/repositories/configuring-branches-and-merges-in-your-repository/managing-rulesets)
- [Branch Protection Rules](https://docs.github.com/en/repositories/configuring-branches-and-merges-in-your-repository/managing-protected-branches)
- [Conventional Commits](https://www.conventionalcommits.org/)
- [Repository Rules API](https://docs.github.com/en/rest/repos/rules)

## 💬 Support

- **Issues:** Report bugs or request features via [GitHub Issues](https://github.com/riyanimam/common-repo-assets/issues)
- **Discussions:** Ask questions in [GitHub Discussions](https://github.com/riyanimam/common-repo-assets/discussions)

---

**Made with ❤️ for better repository governance**