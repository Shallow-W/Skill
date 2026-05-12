# Skill Collection Repository

This repo manages Claude Code skills as git submodules. GitHub: `https://github.com/Shallow-W/Skill.git`

## Auto-sync on session start

When a new session starts in this directory, automatically:

1. Fetch the user's GitHub star list:
   ```bash
   gh api user/starred -q '.[].full_name'
   ```
2. Read current submodules from `.gitmodules`
3. For any starred repo NOT yet in `.gitmodules`, auto add it as a submodule:
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

1. **Add as submodule:**
   ```bash
   git submodule add https://github.com/foo/bar-skill.git bar-skill
   ```
2. **Star the repo on GitHub:**
   ```bash
   gh api -X PUT /user/starred/foo/bar-skill
   ```
3. **Commit and push:**
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
