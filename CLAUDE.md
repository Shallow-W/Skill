# Skill Collection Repository

This repo manages Claude Code skills as git submodules. GitHub: `https://github.com/Shallow-W/Skill.git`

The user's GitHub star list "skill" (`https://github.com/stars/Shallow-W/lists/skill`) is the source of truth for which repos belong to the skill collection. GitHub does not have a public API for star lists, so use web scraping to read it.

## Auto-sync on session start

When a new session starts in this directory, automatically:

1. **Fetch the skill list** by scraping `https://github.com/stars/Shallow-W/lists/skill` using the webReader MCP tool (return_format: text). Parse repo names from lines matching `owner / repo-name`.
2. Read current submodules from `.gitmodules`.
3. For any repo in the skill list NOT yet in `.gitmodules`, auto add it:
   ```bash
   git submodule add https://github.com/<owner>/<repo>.git <repo-name>
   ```
4. Update `skills.txt` to match the skill list.
5. If new submodules were added, commit and push:
   ```bash
   git add .gitmodules skills.txt <new-submodule-paths>
   git commit -m "add <names> submodules"
   git push origin main
   ```

## When user provides a skill repo URL

When the user gives a GitHub repo URL (e.g. `https://github.com/foo/bar-skill.git`):

1. **Add to GitHub star list "skill":** Visit `https://github.com/foo/bar-skill` and star it, then add to the "skill" list via browser. (Or use `gh api -X PUT "user/starred/foo/bar-skill"` to star it.)
2. **Add as submodule:**
   ```bash
   git submodule add https://github.com/foo/bar-skill.git bar-skill
   ```
3. **Update skills.txt** — append `foo/bar-skill`
4. **Commit and push:**
   ```bash
   git add .gitmodules skills.txt <submodule-name>
   git commit -m "add <name> submodule"
   git push origin main
   ```

## Conventions

- `skills.txt`: one `owner/repo` per line, kept in sync with the GitHub star list "skill"
- Submodule directory name: use the repo name (strip `.git` suffix)
- Commit messages: short, list the added submodule names
- Always push to `origin/main`
- Use Co-Authored-By trailer in commits
