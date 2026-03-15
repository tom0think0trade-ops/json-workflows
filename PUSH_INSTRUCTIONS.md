# Push these workflows to GitHub

Your repo is ready. Run these commands **in a terminal** from this folder (`to0mooo-workflows`) to upload everything to [tom0think0trade-ops/json-workflows](https://github.com/tom0think0trade-ops/json-workflows).

**Prerequisites:** [Git](https://git-scm.com/download/win) installed and (optional) [GitHub CLI](https://cli.github.com/) or GitHub login for `git push`.

---

## One-time setup and push

Open **PowerShell** or **Command Prompt**, then:

```bash
cd "c:\Users\thoma\Downloads\to0mooo-workflows"

git init
git branch -M main
git remote add origin https://github.com/tom0think0trade-ops/json-workflows.git

git add .
git commit -m "Add 6 n8n workflows, README, and .gitignore"

# If the repo already has commits (e.g. .gitkeep), pull first:
git pull origin main --allow-unrelated-histories --no-edit

git push -u origin main
```

If the repo is **empty** (no other commits), skip the `git pull` line and run only:

```bash
git push -u origin main
```

---

## If Git asks for credentials

- **HTTPS:** Use a [Personal Access Token](https://github.com/settings/tokens) as the password when prompted.
- **SSH:** Use `git@github.com:tom0think0trade-ops/json-workflows.git` as the remote instead of the `https://` URL, and ensure your SSH key is added to GitHub.

After a successful push, you’ll see all 6 workflow JSONs, `README.md`, and `.gitignore` on GitHub.
