# Skill Collection Repository

This repo manages Claude Code skills as git submodules. GitHub: `https://github.com/Shallow-W/Skill.git`

The file `skills.txt` tracks which repos belong to the skill collection (one `owner/repo` per line). It is the single source of truth for what counts as a "skill".

## Auto-sync on session start

When a new session starts in this directory, automatically:

1. Read `skills.txt` to get the skill collection list
2. Read current submodules from `.gitmodules`
3. For any repo in `skills.txt` NOT yet in `.gitmodules`, auto add it as a submodule:
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

1. **Add to skill collection** (append to `skills.txt`):
   ```
   foo/bar-skill
   ```
2. **Add as submodule:**
   ```bash
   git submodule add https://github.com/foo/bar-skill.git bar-skill
   ```
3. **Star the repo on GitHub:**
   ```bash
   gh api -X PUT /user/starred/foo/bar-skill
   ```
4. **Commit and push:**
   ```bash
   git add .gitmodules skills.txt <submodule-name>
   git commit -m "add <name> submodule"
   git push origin main
   ```

## Conventions

- `skills.txt`: one `owner/repo` per line, no comments, no blank lines needed
- Submodule directory name: use the repo name (strip `.git` suffix)
- Commit messages: short, list the added submodule names
- Always push to `origin/main`
- Use Co-Authored-By trailer in commits
