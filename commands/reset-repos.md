---
description: "Reset all repositories to clean state - restore modified files, manage untracked files, switch to develop branch"
allowed-tools: ["Bash", "TodoWrite"]
---

# Reset All Repositories

## Instructions for Claude Code

1. **Discover repositories**:
   ```bash
   find /home/autonomous-dev/Projects/Ideascale -maxdepth 2 -name ".git" -type d | grep -v "/.claude/.git" | sed 's|/.git||' | sort
   ```

2. **For each repository**:
   - Check status: `cd [repo] && git status --porcelain && git rev-parse --abbrev-ref HEAD`
   - **Handle untracked files**:
     - Auto-preserve: `.serena/`, `.claude/knowledge-base/`
     - For other untracked files: Ask "Found untracked files in [repo]: [list]. Keep these? (y/n)"
     - Remove files user chooses not to keep
   - Restore modified files: `git restore .` (and `git reset --hard HEAD` if staged)
   - Switch to develop: `git checkout develop` (if not already on develop)
   - Report results for this repository

3. **Provide summary**: Repositories processed, files restored, branches switched, untracked files handled, errors