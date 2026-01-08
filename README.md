# Claude PIV Skeleton

**Universal PIV (Prime-Investigate-Verify) methodology skeleton for Claude Code projects**

A technology-agnostic, extensible skeleton repository for implementing the PIV methodology with Claude Code. Provides comprehensive documentation, rules, and templates to accelerate development while maintaining code quality and consistency.

---

## What is PIV?

PIV is a development methodology designed for AI-assisted software development:

- **Prime**: Load and understand codebase context
- **Investigate**: Plan, design, and architecture decisions
- **Verify**: Implement, test, and validate
- **Iterate**: Review, refine, and improve

This methodology ensures Claude Code has proper context before making changes, creates detailed plans for complex features, and validates implementations thoroughly.

---

## Quick Start

### Installation

```bash
# Clone this repository
git clone https://github.com/YOUR_USERNAME/claude-piv-skeleton.git my-project
cd my-project

# Remove git history to start fresh
rm -rf .git
git init

# Install your technology stack
# (Follow technology-specific guides in technologies/ directory)
```

### Your First Feature

1. **Prime the workspace** - Load codebase context
   ```
   Ask Claude: "Run /piv:prime to load the project context"
   ```

2. **Plan your feature** - Create detailed implementation plan
   ```
   Ask Claude: "Use /piv:plan to create a plan for adding user authentication"
   ```

3. **Implement** - Execute the plan
   ```
   Ask Claude: "Use /piv:implement to execute the plan"
   ```

4. **Verify** - Validate the implementation
   ```
   Ask Claude: "Run /piv:verify to validate the implementation"
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
Standardized Claude Code skills for PIV workflow:
- `/piv:prime` - Load context and prime workspace
- `/piv:plan [feature]` - Create detailed feature plans
- `/piv:implement [plan]` - Implement from plan
- `/piv:verify` - Run validation pipeline
- `/piv:iterate` - Review and refine

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
│   ├── rules/                   # Universal rules
│   ├── agents/                  # Agent artifact templates
│   └── reference/               # Best practices
├── technologies/                # Technology-specific extensions
│   ├── backend/
│   │   ├── spring-boot/
│   │   ├── node-express/
│   │   └── python-fastapi/
│   ├── frontend/
│   │   └── react/
│   ├── database/
│   │   └── postgresql/
│   └── devops/
│       └── docker/
├── docs/                        # Comprehensive documentation
│   ├── methodology/
│   ├── getting-started/
│   ├── extending/
│   └── examples/
└── scripts/                     # Utility scripts
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
- [Installation Guide](docs/getting-started/01-installation.md)
- [Quick Start](docs/getting-started/02-quick-start.md)
- [Your First Feature](docs/getting-started/03-your-first-feature.md)
- [Customization](docs/getting-started/04-customization.md)

### Methodology
- [What is PIV?](docs/methodology/01-what-is-piv.md)
- [Prime Phase](docs/methodology/02-prime-phase.md)
- [Investigate Phase](docs/methodology/03-investigate-phase.md)
- [Verify Phase](docs/methodology/04-verify-phase.md)
- [Iteration Cycle](docs/methodology/05-iteration-cycle.md)
- [Best Practices](docs/methodology/06-best-practices.md)

### Extending
- [Adding Technologies](docs/extending/01-adding-technologies.md)
- [Creating Rules](docs/extending/02-creating-rules.md)
- [Writing Commands](docs/extending/03-writing-commands.md)
- [Contributing](docs/extending/04-contributing.md)

---

## Contributing

We welcome contributions! This skeleton is designed to be community-driven and extensible.

### Ways to Contribute
1. **Add new technologies** - Follow the [Adding Technologies guide](docs/extending/01-adding-technologies.md)
2. **Improve documentation** - Fix typos, clarify explanations, add examples
3. **Share examples** - Add real-world implementation examples
4. **Report issues** - Found a bug or have a suggestion? [Open an issue](https://github.com/YOUR_USERNAME/claude-piv-skeleton/issues)
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

[![Star History Chart](https://api.star-history.com/svg?repos=YOUR_USERNAME/claude-piv-skeleton&type=Date)](https://star-history.com/#YOUR_USERNAME/claude-piv-skeleton&Date)

---

**Made with ❤️ for the Claude Code community**
