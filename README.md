# 📖 your-glossary

A personal glossary tool for [Claude Code](https://docs.anthropic.com/en/docs/claude-code). Look up unfamiliar terms, save them locally, and build your own developer dictionary — all without leaving your terminal.

## Features

- `/word add <term>` — Look up a term and save it to your glossary
- `/word list` — View all saved terms
- `/word remove <term>` — Remove a term from your glossary
- `/word search <keyword>` — Search through saved terms
- `/word export` — Export your glossary as a Markdown file

**Language-aware**: Definitions are generated in the language you're working in (English, Japanese, etc.)

## Quick Start

### 1. Copy the command files

Copy the `.claude/commands/` directory into your project:

```bash
# Clone the repo
git clone https://github.com/YOUR_USERNAME/your-glossary.git

# Copy commands to your project
cp -r your-glossary/.claude/commands/ /path/to/your-project/.claude/commands/
```

Or manually download the files from `.claude/commands/` and place them in your project's `.claude/commands/` directory.

### 2. Start using it

Open Claude Code in your project and try:

```
/word add middleware
```

Claude will explain the term and save it to `.your-glossary/glossary.json` in your project.

## Commands

### Add a term

```
/word add decorator
```

```
✅ "decorator" added
---
decorator: A syntax for modifying or extending the behavior of functions
or classes. In Python, decorators use the @ symbol before a function definition.
```

### List all terms

```
/word list
```

```
📖 Glossary (3 terms)
---
1. decorator — A syntax for modifying or extending the behavior of...
2. middleware — A layer that processes requests between client and server...
3. idempotent — A property where performing the same operation multiple times...
```

### Remove a term

```
/word remove decorator
```

```
🗑️ "decorator" removed
```

### Search terms

```
/word search function
```

### Export to Markdown

```
/word export
```

Generates a `glossary.md` file you can share or reference.

## Data Storage

Your glossary is saved locally at:

```
<project-root>/.your-glossary/glossary.json
```

### Git tracking

Choose what works for you:

- **Track it** (share glossary with your team): commit `.your-glossary/` as-is
- **Ignore it** (keep it personal): add `.your-glossary/` to your `.gitignore`

## Requirements

- [Claude Code](https://docs.anthropic.com/en/docs/claude-code) installed and configured

## License

[MIT](./LICENSE)
