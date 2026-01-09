# Claude PIV Skeleton

**Universal PIV (Prime-Investigate-Verify) methodology skeleton for Claude Code projects**

A technology-agnostic, extensible skeleton repository for implementing the PIV methodology with Claude Code. Provides comprehensive documentation, rules, and templates to accelerate development while maintaining code quality and consistency.

---

## What is PIV?

PIV (Prime-Implement-Validate) is a development methodology designed for AI-assisted software development:

- **Prime**: Load and understand codebase context
- **Implement**: Plan features and execute implementation
- **Validate**: Automatically test, verify, and validate

This methodology ensures Claude Code has proper context before making changes, creates detailed plans for complex features, and automatically validates implementations thoroughly.

---

## Quick Start

### For New Projects

```bash
# Clone this repository
git clone https://github.com/galando/claude-piv-skeleton.git my-project
cd my-project

# Remove git history to start fresh
rm -rf .git
git init

# Install your technology stack
# (Follow technology-specific guides in technologies/ directory)
```

### For Existing Projects ✨ NEW

Add PIV to your existing project with the interactive installer:

```bash
# Option 1: Clone and run installer
git clone https://github.com/galando/claude-piv-skeleton.git /tmp/piv
cd your-project
/tmp/piv/scripts/install-piv.sh

# Option 2: Download and run (single command)
curl -sSL https://raw.githubusercontent.com/galando/claude-piv-skeleton/main/scripts/install-piv.sh | bash

# The installer will:
# • Detect your technology stack
# • Install PIV commands and rules
# • Preserve your existing configuration
# • Create automatic backup
```

**See [Installing to Existing Projects](docs/getting-started/04-installing-to-existing-projects.md) for detailed guide.**

### Your First Feature

1. **Prime the workspace** - Load codebase context
   ```
   Ask Claude: "Run /piv_loop:prime to load the project context"
   ```

2. **Plan your feature** - Create detailed implementation plan
   ```
   Ask Claude: "Use /piv_loop:plan-feature to create a plan for adding user authentication"
   ```

3. **Execute** - Implement from plan
   ```
   Ask Claude: "Use /piv_loop:execute to implement the plan"
   ```

4. **Validate** - Automatic validation runs after execute
   ```
   No manual step - /validation:validate runs automatically!
   ```

5. **Bug Fixes** - When bugs occur
   ```
   Ask Claude: "Run /bug_fix:rca for issue #123"
   Ask Claude: "Use /bug_fix:implement-fix to fix the bug"
   ```

---

## Features

### Technology Agnostic Core
- Universal PIV methodology applicable to any technology stack
- Modular design allows picking only what you need
- Progressive enhancement - add technologies as needed

### Modular Rules System
- **Universal Rules**: Apply to all projects (git, testing, documentation)
- **Technology Rules**: Auto-loaded based on your stack
- **Path-based Loading**: Rules load automatically when working on specific file types

### Command Infrastructure
Standardized Claude Code commands for PIV workflow:
- `/piv_loop:prime` - Load context and prime workspace
- `/piv_loop:plan-feature "description"` - Create detailed feature plans
- `/piv_loop:execute [plan]` - Execute from plan (auto-validates)
- `/validation:validate` - Run validation pipeline (automatic)
- `/validation:code-review` - Optional detailed code review
- `/validation:code-review-fix` - Fix issues from code review
- `/validation:execution-report` - View execution report
- `/validation:system-review` - Analyze implementation vs plan
- `/bug_fix:rca` - Root cause analysis for bugs
- `/bug_fix:implement-fix` - Implement bug fix from RCA

### Technology Templates
Pre-built templates for popular technologies:
- **Backend**: Spring Boot, Node.js/Express, Python/FastAPI
- **Frontend**: React with TypeScript
- **Database**: PostgreSQL
- **DevOps**: Docker

Each template includes:
- Technology-specific rules
- Best practices reference
- Code examples
- Integration guides

### Comprehensive Documentation
- Methodology guides for each PIV phase
- Getting started tutorials
- Extending the skeleton
- Real-world examples

---

## Repository Structure

```
claude-piv-skeleton/
├── .claude/                     # Claude Code configuration
│   ├── CLAUDE.md                # Root project instructions
│   ├── PIV-METHODOLOGY.md       # Core methodology documentation
│   ├── commands/                # PIV command definitions
│   │   ├── piv_loop/            # Core PIV workflow commands
│   │   │   ├── prime.md         # Prime phase command
│   │   │   ├── plan-feature.md  # Plan phase command
│   │   │   └── execute.md       # Execute phase command
│   │   ├── validation/          # Validation phase commands
│   │   │   ├── validate.md      # Full validation pipeline
│   │   │   ├── code-review.md   # Technical code review
│   │   │   ├── code-review-fix.md # Fix code review issues
│   │   │   ├── execution-report.md # Implementation report
│   │   │   └── system-review.md # Process improvement analysis
│   │   └── bug_fix/             # Bug fix workflow commands
│   │       ├── rca.md           # Root cause analysis
│   │       └── implement-fix.md # Implement bug fix
│   ├── rules/                   # Coding rules (Claude loads from here)
│   │   ├── backend/             # Technology-specific rules
│   │   │   ├── 10-api-design.md # Spring Boot API patterns
│   │   │   └── 20-database.md   # Spring Boot database patterns
│   │   ├── 00-general.md        # General development principles
│   │   ├── 10-git.md            # Git workflow rules
│   │   ├── 20-testing.md        # Testing philosophy
│   │   ├── 21-testing.md        # Testing guidelines (Given-When-Then)
│   │   ├── 30-documentation.md  # Documentation standards
│   │   └── 40-security.md       # Security guidelines
│   ├── agents/                  # Agent artifact directories (auto-generated)
│   │   ├── context/             # Prime phase context artifacts
│   │   ├── plans/               # Plan phase artifacts
│   │   ├── reports/             # Execution and validation reports
│   │   └── reviews/             # Code review and RCA artifacts
│   └── reference/               # Best practices and patterns
│       └── patterns/            # Design patterns reference
├── technologies/                # Technology-specific templates
│   ├── backend/                 # Backend frameworks
│   │   ├── spring-boot/         # Java/Kotlin + Spring Boot
│   │   │   ├── README.md        # Technology overview
│   │   │   ├── reference/       # Best practices reference
│   │   │   └── examples/        # Code examples
│   │   ├── node-express/        # Node.js + Express
│   │   └── python-fastapi/      # Python + FastAPI
│   ├── frontend/                # Frontend frameworks
│   │   └── react/               # React + TypeScript
│   ├── database/                # Databases
│   │   └── postgresql/          # PostgreSQL
│   └── devops/                  # DevOps tools
│       └── docker/              # Docker
├── docs/                        # Comprehensive documentation
│   ├── getting-started/         # Getting started guides
│   │   ├── 01-installation.md   # Installation guide
│   │   ├── 02-quick-start.md    # Quick start guide
│   │   └── 03-your-first-feature.md  # First feature walkthrough
│   ├── extending/               # Extension guides
│   │   └── 01-adding-technologies.md  # Adding new technologies
│   ├── methodology/             # Methodology deep dives
│   └── examples/                # Real-world examples
├── scripts/                     # Utility scripts
├── .github/                     # GitHub configuration
│   ├── workflows/               # CI/CD workflows
│   └── ISSUE_TEMPLATE/          # Issue templates
├── README.md                    # This file
├── CONTRIBUTING.md              # Contributing guidelines
└── LICENSE                      # MIT License
```

---

## Supported Technologies

### Backend
| Technology | Status | Description |
|------------|--------|-------------|
| Spring Boot | ✅ Stable | Java/Kotlin backend framework |
| Node.js + Express | ✅ Stable | JavaScript/TypeScript backend |
| Python + FastAPI | ✅ Stable | Modern Python async framework |

### Frontend
| Technology | Status | Description |
|------------|--------|-------------|
| React + TypeScript | ✅ Stable | React with strict TypeScript |

### Database
| Technology | Status | Description |
|------------|--------|-------------|
| PostgreSQL | ✅ Stable | Relational database |

### DevOps
| Technology | Status | Description |
|------------|--------|-------------|
| Docker | ✅ Stable | Containerization |

---

## Documentation

### Getting Started
- [Installation Guide](docs/getting-started/01-installation.md) - How to install and set up
- [Quick Start](docs/getting-started/02-quick-start.md) - Get started in 5 minutes
- [Your First Feature](docs/getting-started/03-your-first-feature.md) - Walkthrough of first feature

### Methodology
- [PIV Methodology](.claude/PIV-METHODOLOGY.md) - Complete methodology guide
- [Project Instructions](.claude/CLAUDE.md) - Quick reference for Claude Code

### Contributing
- [Contributing Guidelines](CONTRIBUTING.md) - How to contribute to the skeleton
- [Adding Technologies](docs/extending/01-adding-technologies.md) - Add new technology templates

---

## Contributing

We welcome contributions! This skeleton is designed to be community-driven and extensible.

### Ways to Contribute
1. **Add new technologies** - Follow the [Adding Technologies guide](docs/extending/01-adding-technologies.md)
2. **Improve documentation** - Fix typos, clarify explanations, add examples
3. **Share examples** - Add real-world implementation examples
4. **Report issues** - Found a bug or have a suggestion? [Open an issue](https://github.com/galando/claude-piv-skeleton/issues)
5. **Submit PRs** - Pull requests are welcome!

See [CONTRIBUTING.md](CONTRIBUTING.md) for detailed guidelines.

---

## Origins & Attribution

**The PIV Methodology was created by [Cole Medin (coleam00)](https://github.com/coleam00)**

Cole Medin is a Generative AI specialist who developed the PIV (Prime-Implement-Validate) methodology as part of his work on [context engineering](https://github.com/coleam00/context-engineering-intro) and AI-assisted development workflows. The methodology is demonstrated in his [habit-tracker](https://github.com/coleam00/habit-tracker) project.

This skeleton is an implementation of Cole's PIV methodology, adapted from:
- [habit-tracker](https://github.com/coleam00/habit-tracker) - PIV loop demonstration project
- [context-engineering-intro](https://github.com/coleam00/context-engineering-intro) - Context engineering template
- [woningscoutje.nl](https://woningscoutje.nl) - Production-ready implementation using PIV

### Cole Medin's Other Work
- [Archon](https://github.com/coleam00/Archon) - Knowledge and task management backbone for AI assistants
- [ottomator-agents](https://github.com/coleam00/ottomator-agents) - Open source AI agents
- [ai-agents-masterclass](https://github.com/coleam00/ai-agents-masterclass) - AI agents educational content

**Thank you, Cole, for creating and sharing the PIV methodology with the community!** 🙌

---

## License

MIT License - see [LICENSE](LICENSE) for details.

---

## Star History

[![Star History Chart](https://api.star-history.com/svg?repos=galando/claude-piv-skeleton&type=Date)](https://star-history.com/#galando/claude-piv-skeleton&Date)

---

**Made with ❤️ for the Claude Code community**
