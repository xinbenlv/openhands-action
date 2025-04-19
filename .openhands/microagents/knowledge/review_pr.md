---
name: review_pr
type: task
version: 1.0.1
agent: CodeActAgent
triggers:
  - review pr
  - review pull request
  - pr review
  - code review
inputs:
  - name: PR_NUMBER
    description: 'The number of the pull request to review (e.g., 158)'
    required: true
---

# PR Review Task

This task guides you through reviewing pull requests in ${{ SELECTED_REPO }} according to project standards.

## Finding file changes
When reviewing file changes, instead of diffing with default branch such as `main` please diff against the base branch of the PR.

## Review Steps and Guidelines for Each Step

A high quality code review addresses 3 aspects:

1. is it the appropriate problem (goal)? (Pass 1)
2. is it the concise and optimal solution? (Pass 2)
3. is it the high quality implementation? (Pass 3)

For each pass, if needed, you will submit a PR-level comment and multiple line-level suggested changes via Github API.

When everything is done, you will submit a Github pull request review to approve the PR via Github API.

### **Review Pass 1: Is it the appropriate problem (goal)?**:
- Review the PR description and linked issues, 
- Summarize the files changes and see if the PR description matches the changes
- If the PR description is not aligned with the changes, ask the author to clarify the goal of the PR
- If the PR description is aligned with the changes, proceed based on the understanding of the goal and the overall repository, 
comment on whether the goal is the right goal. The right goal of a PR should be
  - aligned with the overall repository goals, 
  - not duplicating an existing functionality
  - should leverage the existing code and architecture unless it's intended for a upgrade/replacement/refactor
  - should not reinvent the wheel: suggest using popular open source libraries if they exist
  - should not over engineer: suggest using simple solutions if they exist
  - should not over generalize such as extracting a piece of code logic into a library / component when they are only being used in one place
  - should have proper sizing (not too big, not too small).

Send *PR-level comment* to ask for clarification on the goal of the PR. ONLY proceed to next Review Pass if the goal is appropriate, otherwise wait for the author to respond.

### **Review Pass 2: Is it the concise and optimal solution?**:
   - Understand whether the commit is a small change that fixes a bug or a larger change that implements a new feature/component or refactors code.
   - If it's a small change, skip architectural feedback, proceed to next step.
   - If it's a larger change, provide feedback on the architectural choices made.
      - Summarize the key goal and the solution to that key goal implemented in the PR
      - Check if the author provided documentations about major design decisions
      - Summarize the design decisions made by author
      - Check completenesss: suggest alternative approaches if applicable, and create a table to compare the pros and cons of each approach

Send *PR-level comment* to ask for clarification on the solution of the PR. ONLY proceed to next Review Pass if the solution is concise and optimal, otherwise wait for the author to respond.

### **Review Pass 3: Is it the high quality implementation?**:
- Verify changes follow `.coderabbit.yaml` rules
- Suggest the right implementation if the current implementation is not the right one
- Suggest improvements to the code structure, naming conventions, and documentation
- Check for proper error handling
- Ensure consistent formatting
- Check completeness: suggest other part of the code that needs to be updated to complete the implementation.

- **Documentation**:
   - Verify README/docstring updates
   - Check for new configuration needs

- **Testing**:
   - Confirm adequate test coverage
   - If unit tests should be added, suggest the right tests to add
   - Verify tests pass

Send *line-level suggested change* to ask for fixing the implementation of the PR. ONLY proceed to next Review Pass if the implementation is high quality, otherwise wait for the author to respond.

## Using Github API

Please note: use markdown in the comment or reply for better readability if it's more than a plain text.

### This is how you send *PR-level comment* with Github API

```bash
curl -L \
  -X POST \
  -H "Accept: application/vnd.github+json" \
  -H "Authorization: Bearer <YOUR-TOKEN>" \
  -H "X-GitHub-Api-Version: 2022-11-28" \
  https://api.github.com/repos/OWNER/REPO/pulls/PULL_NUMBER/comments \
-d '{ "body": "[COMMENT]", "commit_id": "'$(git rev-parse HEAD)'", "path": "[FILE_PATH]", "start_line": [START_LINE_NUMBER],"start_side":"[START_SIDE]","line": [LINE_NUMBER],"side":"[SIDE]"}'
```

Note: even for PR-level comment, you still need to provide the `commit_id`, `path`, and `line` to specify the comment location so that the comment will be shown in the PR page in the most relevant location.

### This is how you send a *line-level suggested change* with github API

```bash
curl -L \
  -X POST \
  -H "Accept: application/vnd.github+json" \
  -H "Authorization: Bearer <YOUR-TOKEN>" \
  -H "X-GitHub-Api-Version: 2022-11-28" \
  https://api.github.com/repos/OWNER/REPO/pulls/PULL_NUMBER/comments \
-d '{ "body": "suggestion\n[SUGGESTED_CODE_WITH_ESCAPED_NEWLINES]\n", "commit_id": "'$(git rev-parse HEAD)'", "path": "[FILE_PATH]", "start_line": [START_LINE_NUMBER],"start_side":"[START_SIDE]","line": [LINE_NUMBER],"side":"[SIDE]"}'
```

### This is how you make a reply to a review comment

```bash
curl -L \
  -X POST \
  -H "Accept: application/vnd.github+json" \
  -H "Authorization: Bearer <YOUR-TOKEN>" \
  -H "X-GitHub-Api-Version: 2022-11-28" \
  https://api.github.com/repos/OWNER/REPO/pulls/PULL_NUMBER/comments/COMMENT_ID/replies \
  -d '{"body":"[REPLY]"}'
```

### This is how you request changes to a PR

```bash
# Example, use your own data
curl -L \
  -X POST \
  -H "Accept: application/vnd.github+json" \
  -H "Authorization: Bearer <YOUR-TOKEN>" \
  -H "X-GitHub-Api-Version: 2022-11-28" \
  https://api.github.com/repos/OWNER/REPO/pulls/PULL_NUMBER/reviews \
  -d '{"commit_id":"ecdd80bb57125d7ba9641ffaa4d7d2c19d3f3091","body":"This is close to perfect! Please address the suggested inline change.","event":"REQUEST_CHANGES","comments":[{"path":"file.md","position":6,"body":"Please add more information here, and fix this typo."}]}'
```

### This is how you approve a PR

```bash
# Example, use your own data
curl -L \
  -X POST \
  -H "Accept: application/vnd.github+json" \
  -H "Authorization: Bearer <YOUR-TOKEN>" \
  -H "X-GitHub-Api-Version: 2022-11-28" \
  https://api.github.com/repos/OWNER/REPO/pulls/PULL_NUMBER/reviews \
  -d '{"commit_id":"ecdd80bb57125d7ba9641ffaa4d7d2c19d3f3091","body":"Great stuff!","event":"APPROVE","comments":[...]}'
```

## Reference Documents
- `./coderabbit.yaml` for review standards
- [Github API: Create a review comment](https://docs.github.com/en/rest/pulls/comments?apiVersion=2022-11-28#create-a-review-comment)
- [Github API: Create a reply for a review comment](https://docs.github.com/en/rest/pulls/comments?apiVersion=2022-11-28#create-a-reply-for-a-review-comment)
- [Create a review](https://docs.github.com/en/rest/pulls/reviews?apiVersion=2022-11-28#create-a-review)
