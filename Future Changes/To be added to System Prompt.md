<!-- Filename: To be added to System Prompt.md -->
<!-- Version: 1 -->
<!-- Date: 2025-12-23T21:20:00Z -->
<!-- Author: Claude -->
<!-- Purpose: Tracking proposed changes to be incorporated into Lucky's AI system prompt.
     Serves as staging area for system prompt improvements discovered during sessions. -->
<!-- Usage: Review entries periodically and integrate into main system prompt -->

# Future System Prompt Additions

Tracking proposed changes to be incorporated into Lucky's AI system prompt.

---

## GitHub CLI (gh) Usage

**Date Added**: 2025-12-23
**Priority**: Medium
**Section**: Tool Selection / Version Control

**Change**: Inform AI that `gh` (GitHub CLI) is installed and should be used for GitHub operations instead of plain git commands.

**Rationale**: 
- `gh` provides better GitHub integration (PRs, issues, releases, gists)
- Handles authentication automatically
- More ergonomic for GitHub-specific operations

**Proposed Text**:
```
Lucky has GitHub CLI (`gh`) installed on both Windows 11 and Linux servers. For GitHub operations (push, pull, PR creation, issue management, releases), prefer `gh` over plain `git` commands when GitHub-specific functionality is needed. Plain `git` remains appropriate for local operations (commit, branch, merge, rebase).

Examples:
- Push: `gh repo sync` or `git push` (both acceptable)
- Create PR: `gh pr create` (preferred over manual web workflow)
- Clone: `gh repo clone user/repo` (preferred, handles auth)
- Issues: `gh issue list/create/view` (gh only)
```

**Notes**: 
- Don't force `gh` for pure git operations (commit, branch, merge)
- Use `gh` when GitHub API/features are involved
- `gh` handles authentication via saved tokens
