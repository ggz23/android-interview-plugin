# Android Interview Plugin for Claude Code

A Claude Code plugin that provides Android development guidelines, architecture patterns, and interview-mode workflows for use in any Android codebase.

## Installation

```bash
# Add the marketplace
/plugin marketplace add ggz23/android-interview-plugin

# Install the plugin
/plugin install android-interview
```

## Quick Start

After installation, run setup to add guidelines to the project:

```bash
/android-interview:setup
```

Then start implementing features with proper requirements clarification:

```bash
/android-interview:implement <feature description>
```

## Available Commands

| Command | Description |
|---------|-------------|
| `/android-interview:setup` | Add Android guidelines to project's CLAUDE.md |
| `/android-interview:implement` | Implement feature with requirements clarification |
| `/android-interview:add-screen` | Create screen + ViewModel |
| `/android-interview:add-api` | Create Retrofit API interface |
| `/android-interview:add-entity` | Create Room entity + DAO |
| `/android-interview:add-tests` | Create ViewModel/Repository tests |
| `/android-interview:commit` | Create conventional commit |

## Skills (Auto-activated)

- **android-architecture**: MVI state management, repository pattern guidance
- **interview-mode**: Ensures clarifying questions before coding

## What's Included

### Architecture Patterns
- MVI state management
- Repository pattern (single source of truth)
- Clean architecture layers

### Implementation Guidelines
- `suspend fun` vs `Flow<T>` best practices
- Serialization patterns
- Testing patterns with MockK and Turbine

### Interview Mode
- Structured requirements clarification
- Edge case identification
- Scope confirmation before coding

## Local Development

Test the plugin locally:

```bash
claude --plugin-dir ./android-interview-plugin
```

## License

MIT
