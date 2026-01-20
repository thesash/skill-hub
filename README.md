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
skill-hub                     # Launch interactive menu (requires gum)
skill-hub status              # Detailed status
```

The interactive menu lets you:
- Configure which skills each agent gets
- Manage skills (view info, migrate to project repos)
- Create and apply skill presets

## Commands

### Setup & Creation

```bash
skill-hub init                # Initialize ~/.skill-hub/ data directory
skill-hub new <name>          # Create a new skill from template
skill-hub path                # Print data directory path
```

### View & Edit

```bash
skill-hub                     # Interactive menu (requires gum) - default when no command
skill-hub i                   # Same as above
skill-hub status              # Show skills and agent distribution
skill-hub list                # List all skills (shows central vs project-linked)
skill-hub info <skill>        # Show detailed info about a skill
```

### Skill Distribution

```bash
skill-hub set <agent> <skills>      # Set skills ("all" or comma-separated)
skill-hub enable <agent> <skill>    # Add a skill to an agent
skill-hub disable <agent> <skill>   # Remove a skill from an agent
skill-hub copy <from> <to>          # Copy skills from one agent to another
skill-hub reset <agent>             # Reset agent to all skills
skill-hub sync [agent]              # Apply config to filesystem (create/update symlinks)
skill-hub uninstall [agent]         # Detach agents from hub (copy skills locally)
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
skill-hub migrate <skill> [path]               # Move skill to its own project repo
skill-hub deps [install]                       # Check/install dependencies
skill-hub config [key] [value]                 # Get/set configuration
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
- `skills/.system` holds Codex system skills; it is included when `skills: all` is used (directory symlink) but is hidden from explicit skill lists

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

Skills can live in two places:
- **Central** (`~/.skill-hub/skills/`) - Simple, all skills together
- **Project-linked** (your own repo) - Skill versions with its project

#### Migrating a Skill to Its Own Repo

When a skill grows complex enough to warrant its own repo:

```bash
# Configure your project directory (during init, or manually)
skill-hub config project_dir ~/p
skill-hub config git_init true

# Migrate a skill to its own repo
skill-hub migrate my-skill
# Creates: ~/p/my-skill/SKILL.md
# Symlink: ~/.skill-hub/skills/my-skill -> ~/p/my-skill

# Then push to GitHub if desired
cd ~/p/my-skill
gh repo create my-skill --private --source=. --push
```

#### Linking an Existing Repo

If you already have a skill in a project repo:

```bash
skill-hub link-project ynab-review ~/p/ynab-review
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
│   ├── .system/                     # Codex system skills
│   ├── my-skill/                    # Central skill (directory)
│   │   └── SKILL.md
│   └── ynab-review -> ~/p/ynab-review  # Project-linked skill (symlink)
├── agents.conf                      # Agent configuration
├── presets.conf                     # Saved presets
└── settings.conf                    # User settings (project_dir, git_init)

~/p/ynab-review/                     # Project repo for a skill
├── SKILL.md                         # Skill definition at repo root
└── ...                              # Other project files

~/.claude/skills/my-skill -> ~/.skill-hub/skills/my-skill   # Agent symlinks
~/.codex/skills/my-skill -> ~/.skill-hub/skills/my-skill
```

## Detaching (Uninstall)

To return to per-agent, local skill folders:

```bash
skill-hub uninstall          # All agents
skill-hub uninstall codex    # Single agent
```

This replaces directory or per-skill symlinks with local copies in each agent's skills directory. Project-linked skills remain symlinks to their repos.
Running `skill-hub sync` later will re-apply whatever `agents.conf` specifies.
Note: `uninstall` has not been tested yet.

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
