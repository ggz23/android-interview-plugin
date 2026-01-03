# Commit

Create a git commit following the Conventional Commits specification.

## Steps

1. Run `git status` to see all staged and unstaged changes
2. Run `git diff --cached` to see staged changes (if any)
3. Run `git diff` to see unstaged changes
4. Run `git log --oneline -5` to see recent commit style

5. Analyze the changes and determine:
   - **type**: feat, fix, docs, style, refactor, perf, test, build, ci, chore
   - **scope** (optional): module or component affected
   - **breaking**: whether this is a breaking change (add ! after type/scope)
   - **description**: imperative, lowercase, no period

6. Stage relevant files if not already staged using git add

7. Create the commit using this format:

```
<type>[optional scope][!]: <description>

[optional body]

[optional footer(s)]
```

## Conventional Commits Types

| Type | When to use |
|------|-------------|
| feat | New feature |
| fix | Bug fix |
| docs | Documentation only |
| style | Formatting, no code change |
| refactor | Code change that neither fixes a bug nor adds a feature |
| perf | Performance improvement |
| test | Adding or fixing tests |
| build | Build system or dependencies |
| ci | CI configuration |
| chore | Other changes (e.g., .gitignore) |

## Examples

```bash
# Simple feature
git commit -m "feat: add user login endpoint"

# With scope
git commit -m "feat(auth): add password reset flow"

# Breaking change
git commit -m "feat(api)!: change response format to JSON:API"

# With body
git commit -m "fix(parser): handle empty input arrays

Previously the parser would crash when given an empty array.
Now it returns an empty result set."
```

## Rules

- Description MUST be lowercase
- Description MUST be imperative mood ("add" not "added" or "adds")
- Description MUST NOT end with a period
- Body MUST be separated from description by a blank line
- Do NOT include any Claude Code attribution or co-author footers
