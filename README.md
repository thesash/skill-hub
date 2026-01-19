# Skill Hub

Centralized skill management for distributing skills across AI agents (Claude Code, Codex, Clawdbot).

## Installation

### Homebrew (macOS)

```bash
brew tap thesash/tap
brew install skill-hub
```

### Manual Install

```bash
# Clone the repo
git clone https://github.com/thesash/skill-hub.git
cd skill-hub

# Add to PATH (add to ~/.zshrc or ~/.bashrc)
export PATH="$PWD/scripts:$PATH"

# Install gum for interactive features (optional but recommended)
brew install gum
```

### First-Time Setup

```bash
skill-hub init                # Creates ~/.skill-hub/
skill-hub new my-first-skill  # Create your first skill
skill-hub sync                # Sync skills to agents
```

## Quick Start

```bash
skill-hub matrix              # Interactive matrix (arrows, space to toggle, enter to save)
skill-hub i                   # Interactive menu (requires gum)
skill-hub status              # Detailed status
```

## Commands

### Setup & Creation

```bash
skill-hub init                # Initialize ~/.skill-hub/ data directory
skill-hub new <name>          # Create a new skill from template
skill-hub path                # Print data directory path
```

### View & Edit

```bash
skill-hub matrix              # Interactive matrix - arrows to navigate, space to toggle, enter to save
skill-hub i                   # Interactive menu (requires gum)
skill-hub status              # Show skills and agent distribution
skill-hub list                # List all skills
```

### Skill Distribution

```bash
skill-hub set <agent> <skills>      # Set skills ("all" or comma-separated)
skill-hub enable <agent> <skill>    # Add a skill to an agent
skill-hub disable <agent> <skill>   # Remove a skill from an agent
skill-hub copy <from> <to>          # Copy skills from one agent to another
skill-hub reset <agent>             # Reset agent to all skills
skill-hub sync [agent]              # Apply config to filesystem (create/update symlinks)
```

### Presets

```bash
skill-hub preset create <name> <skills>   # Create a preset skill bundle
skill-hub preset list                     # List all presets
skill-hub preset show <name>              # Show skills in a preset
skill-hub preset apply <agent> <preset>   # Apply preset to an agent
skill-hub preset delete <name>            # Delete a preset
```

### Agent Management

```bash
skill-hub add-agent <name> <path> [skills]     # Add agent target
skill-hub link-project <skill> <project-path>  # Link skill to project repo
skill-hub deps [install]                       # Check/install dependencies
```

## Configuration

Edit `~/.skill-hub/agents.conf`:

```bash
# Format: name:path:skills
# skills: "all" or comma-separated list

claude-code:~/.claude/skills:all
codex:~/.codex/skills:bird,ynab,spreadsheet
clawdbot:~/.clawdbot/skills:all
```

- `all` = symlink entire directory (agent gets everything)
- `skill1,skill2` = individual symlinks (agent only gets listed skills)

## Creating Skills

Skills are stored in `~/.skill-hub/skills/`. Each skill needs a `SKILL.md` file with YAML frontmatter:

```bash
skill-hub new my-skill   # Creates ~/.skill-hub/skills/my-skill/SKILL.md
```

### SKILL.md Format

```yaml
---
name: my-skill
description: Brief description
allowed-tools: Bash, Read, Write, Edit
---

# My Skill

Instructions for the AI agent...
```

### Project-Linked Skills

Keep skills versioned with their project repos:

```bash
skill-hub link-project ynab-review ~/p/ynab-review/skill
```

### Skill Dependencies

Add a `metadata:` line in SKILL.md to declare dependencies:

```
metadata: {"clawdbot":{"requires":{"bins":["yt-dlp","ffmpeg"],"env":["API_KEY"]}}}
```

## Architecture

```
~/.skill-hub/                        # User data directory
├── skills/                          # Your skills
│   ├── my-skill/
│   │   └── SKILL.md
│   └── another-skill/
├── agents.conf                      # Agent configuration
└── presets.conf                     # Saved presets

~/.claude/skills/my-skill -> ~/.skill-hub/skills/my-skill   # Agent symlinks
~/.codex/skills/my-skill -> ~/.skill-hub/skills/my-skill
```

## Version Control Your Skills

Your skills live in `~/.skill-hub/` and can be version controlled independently:

```bash
cd ~/.skill-hub
git init
git add -A && git commit -m "My skills"
gh repo create my-skills --private --source=.
```

## Environment Variables

- `SKILL_HUB_DATA` - Override the data directory (default: `~/.skill-hub`)

## License

MIT
