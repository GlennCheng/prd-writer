# PRD Writer

An interactive PRD (Product Requirements Document) skill for Claude Code.

The AI acts as a **PhD advisor** — first brainstorming with you to challenge the problem framing, then guiding you through structured Clarify rounds to produce a well-formed PRD.

## Features

- 🎯 **Direction Challenge**: AI first brainstorms with you to verify the problem is framed correctly
- 🔄 **Dynamic Clarify Loop**: Multi-round Q&A with Modern Dashboard card UI, each answer shapes the next question
- 🧠 **Integrative Questions**: Questions synthesize multiple dimensions at once (psychology, systems thinking, second-order effects, and more)
- 📋 **Structured PRD**: Based on proven specrity template, extensible with AI-suggested sections
- 💾 **Cross-session State**: Resume from where you left off, anytime
- 🔌 **Zero dependencies**: Works without any tools. Auto-detects Jira, Confluence, Notion, GitHub MCP when available
- 🌐 **Language-adaptive**: Responds in the language you use

## Installation

### Via Claude Code CLI

```bash
claude plugin install github:GlennCheng/prd-writer
```

### Manual

1. Clone this repo
2. Copy to your Claude plugins cache:
   ```bash
   mkdir -p ~/.claude/plugins/cache/GlennCheng-prd-writer/prd-writer/1.0.0
   cp -r .claude-plugin skills ~/.claude/plugins/cache/GlennCheng-prd-writer/prd-writer/1.0.0/
   ```
3. Register in `~/.claude/plugins/installed_plugins.json`:
   ```json
   "prd-writer@GlennCheng-prd-writer": [{
     "scope": "user",
     "installPath": "~/.claude/plugins/cache/GlennCheng-prd-writer/prd-writer/1.0.0",
     "version": "1.0.0",
     "installedAt": "2026-01-01T00:00:00.000Z",
     "lastUpdated": "2026-01-01T00:00:00.000Z"
   }]
   ```
4. Restart Claude Code

## Usage

Just describe what you want to build:

```
幫我寫 XXX 功能的 PRD
create PRD for user authentication flow
write product requirements for the checkout redesign
```

Or pass a Jira ticket ID if you have Atlassian MCP connected:

```
幫我寫 PROJ-123 的 PRD
```

### Resuming a draft

If you've already started a PRD, just mention the feature again and the skill will detect the existing draft and resume.

## File Structure

```
prd-writer/
├── .claude-plugin/
│   └── plugin.json
└── skills/
    └── prd-writer/
        ├── SKILL.md                  # Main flow
        └── references/
            ├── clarify-engine.md     # Clarify round logic & question synthesis
            ├── prd-template.md       # PRD structure template
            ├── quality-check.md      # Quality validation rules
            └── publish-adapters.md   # Confluence / Notion / GitHub publishing
```

## Local Output

Each PRD is saved to your project under `docs/prd/<feature-slug>/` (configurable):

```
docs/prd/user-auth-flow/
├── state.yml          # Progress tracking (phase, version, publish records)
├── clarify-log.md     # Full Q&A history
├── prd.md             # The PRD document (live-updated during Clarify)
└── jira-snapshot.md   # Jira ticket snapshot (if applicable)
```

## Configuration

On first run, the skill creates `.prd-writer.yml` in your project root:

```yaml
prd_root: docs/prd
language: auto          # follows user's language
integrations:
  confluence:
    space_id: ~
  github:
    branch: main
    path: docs/prd
```

## License

MIT
