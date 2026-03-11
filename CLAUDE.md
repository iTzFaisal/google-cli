# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## Project Overview

This repository contains **Claude Code skills** for the [Google Workspace CLI](https://github.com/googleworkspace/cli) (`gws`). The `gws` binary is a command-line interface for Google Workspace APIs (Gmail, Calendar, Drive, Sheets, etc.).

This repo does **not** contain the `gws` CLI itself — it only contains skill definitions that help Claude Code use the `gws` CLI effectively.

## Prerequisites

The `gws` binary must be installed and on `$PATH`. See the [gws repository](https://github.com/googleworkspace/cli) for installation options.

## Architecture

### Skill Types

Skills are organized into categories:

1. **Service skills** (`gws-calendar`, `gws-gmail`, `gws-drive`, etc.) — Document API resources and methods for each Google Workspace service. Always read `gws-shared` first for auth, global flags, and security rules.

2. **Helper commands** (`gws-calendar-insert`, `gws-gmail-send`, `gws-drive-upload`) — Higher-level convenience commands that wrap common API operations.

3. **Workflow skills** (`gws-workflow-*`) — Cross-service productivity workflows like `+standup-report`, `+meeting-prep`, `+weekly-digest`.

4. **Persona skills** (`persona-*`) — Role-based skill collections (exec-assistant, project-manager, it-admin, etc.) with specific instructions for each role.

5. **Recipe skills** (`recipe-*`) — Step-by-step guides for common tasks (create-shared-drive, schedule-recurring-event, etc.).

### Skill File Structure

Each skill file contains:

- **YAML frontmatter** with `name`, `version`, `description`, and `metadata.openclaw` fields
- **Markdown content** documenting the skill's purpose, commands, and usage patterns
- **Cross-references** to other skills via relative paths like `[../gws-shared/SKILL.md]`

### Skills Lock File

`skills-lock.json` tracks all installed skills with their source (GitHub repo) and SHA256 hashes. This is managed by Claude Code's skill system — manual editing is not required.

## Common Commands

### Discovery

```bash
# Browse resources and methods for a service
gws calendar --help
gws gmail --help
gws drive --help

# Inspect a method's required params, types, and defaults
gws schema calendar.events.insert
gws schema gmail.messages.send
```

### Authentication

```bash
# Browser-based OAuth (interactive)
gws auth login

# Service Account
export GOOGLE_APPLICATION_CREDENTIALS=/path/to/key.json
```

### Global Flags

| Flag                        | Description                                             |
| --------------------------- | ------------------------------------------------------- |
| `--format <FORMAT>`         | Output format: `json` (default), `table`, `yaml`, `csv` |
| `--dry-run`                 | Validate locally without calling the API                |
| `--sanitize <TEMPLATE>`     | Screen responses through Model Armor                    |
| `--params '{"key": "val"}'` | URL/query parameters                                    |
| `--json '{"key": "val"}'`   | Request body                                            |
| `--page-all`                | Auto-paginate (outputs NDJSON)                          |

## Security Rules

From `gws-shared/SKILL.md`:

- **Never** output secrets (API keys, tokens) directly
- **Always** confirm with user before executing write/delete commands
- Prefer `--dry-run` for destructive operations
- Use `--sanitize` for PII/content safety screening

## Creating New Skills

When creating new skills:

1. **Service skills** should follow the pattern in existing `gws-*` skills — document API resources and methods with `gws schema` references.

2. **Helper commands** should use the `+` prefix convention (e.g., `+insert`, `+send`) and be referenced from parent service skills.

3. **Recipe skills** should be step-by-step with exact command examples.

4. **Persona skills** should declare dependencies in `metadata.openclaw.requires.skills`.

5. **Always include the prerequisite link** to `../gws-shared/SKILL.md` for any skill that calls the `gws` CLI.

## Community

The upstream project is at https://github.com/googleworkspace/cli. For bugs or feature requests, direct users to open issues there.
