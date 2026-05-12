# Skill Collection Repository

This repo manages Claude Code skills as git submodules. GitHub: `https://github.com/Shallow-W/Skill.git`

The GitHub star list "skill" is the single source of truth, managed via GraphQL API.

## Key constants

- **Skill list ID**: `UL_kwDOBZd3Fs4AehUO`
- **Read skill list repos**:
  ```bash
  gh api graphql -f query='{ node(id: "UL_kwDOBZd3Fs4AehUO") { ... on UserList { name items(first: 50) { nodes { ... on Repository { nameWithOwner } } } } } }' -q '.data.node.items.nodes[].nameWithOwner'
  ```
- **List all lists**:
  ```bash
  gh api graphql -f query='{ viewer { lists(first: 20) { nodes { id name slug } } } }'
  ```

## Auto-sync on session start

When a new session starts in this directory, automatically:

1. **Read skill list** via GraphQL (command above).
2. Read current submodules from `.gitmodules`.
3. For any repo in the skill list NOT yet in `.gitmodules`, auto add it:
   ```bash
   git submodule add https://github.com/<owner>/<repo>.git <repo-name>
   ```
4. If new submodules were added, commit and push:
   ```bash
   git add .gitmodules <new-submodule-paths>
   git commit -m "add <names> submodules"
   git push origin main
   ```

## When user provides a skill repo URL

When the user gives a GitHub repo URL (e.g. `https://github.com/foo/bar-skill.git`):

1. **Star the repo:**
   ```bash
   gh api -X PUT "user/starred/foo/bar-skill"
   ```
2. **Add to skill list** via GraphQL:
   ```bash
   REPO_ID=$(gh api graphql -f query='{ repository(owner: "foo", name: "bar-skill") { id } }' -q '.data.repository.id')
   gh api graphql -f query="mutation { updateUserListsForItem(input: { itemId: \"$REPO_ID\", listIds: [\"UL_kwDOBZd3Fs4AehUO\"] }) { clientMutationId } }"
   ```
3. **Add as submodule:**
   ```bash
   git submodule add https://github.com/foo/bar-skill.git bar-skill
   ```
4. **Commit and push:**
   ```bash
   git add .gitmodules <submodule-name>
   git commit -m "add <name> submodule"
   git push origin main
   ```

## Conventions

- Submodule directory name: use the repo name (strip `.git` suffix)
- Commit messages: short, list the added submodule names
- Always push to `origin/main`
- Use Co-Authored-By trailer in commits
