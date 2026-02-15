---
name: git-last-commit-reviewer
description: Performs a code review specifically for the changes introduced in the most recent git commit. Use this skill to ensure the latest changes meet quality standards, follow best practices, and don't introduce regressions.
license: MIT
compatibility: Requires git CLI and access to the local repository.
---

# Last Commit Code Review

This skill guides the agent through reviewing only the changes introduced in the latest commit (`HEAD`).

## Instructions

1.  **Analyze Context**: If the diff is unclear, examine the commit message using `git log -1` to understand the intent.
2.  **Review Criteria**:
    *   **Logic**: Check for off-by-one errors, null pointers, or flawed business logic.
    *   **Style**: Ensure the code matches the project's existing patterns.
    *   **Security**: Look for hardcoded secrets or injection vulnerabilities.
    *   **Testing**: Verify if corresponding tests were added or updated in the same commit.
3.  **Feedback**: Provide a concise summary of findings, categorized by "Critical," "Suggestion," and "Positive."

## Changes to review

**Log message**

!`git log -1`

**Code changes**

!`git diff HEAD~1 HEAD`

## Edge Cases
*   **Initial Commit**: If there is only one commit in the repo, `HEAD~1` will fail. In this case, diff against the empty tree: `git diff 4b825dc642cb6eb9a060e54bf8d69288fbee4904 HEAD`.
*   **Merge Commits**: Merge commits may show large diffs; focus on resolving conflicts or integration issues.
