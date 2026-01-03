# Setup Android Guidelines

Add Android development guidelines to this project's CLAUDE.md file.

## Steps

1. **Check for existing CLAUDE.md**
   - Look for CLAUDE.md in the project root
   - If it exists, read its current contents

2. **Read Android guidelines**
   - Read the file at `${CLAUDE_PLUGIN_ROOT}/content/android-guidelines.md`

3. **Merge guidelines**
   - If CLAUDE.md doesn't exist: Create it with the Android guidelines
   - If CLAUDE.md exists: Append the guidelines with a separator:
     ```
     ---
     <!-- Android Interview Plugin Guidelines -->
     ```

4. **Confirm completion**
   - Tell the user what was added
   - Mention they can now use `/android-interview:implement` to start features

## Important
- Do NOT overwrite existing project-specific content in CLAUDE.md
- Always append, never replace
