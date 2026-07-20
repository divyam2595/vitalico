# Putting Vitalico v2.0.0 on GitHub

You have two ways to import the project. **Option A (bundle) is recommended** — it preserves the complete commit history, all checkpoints and release tags (v1.0.0, v2.0.0, both rollback checkpoints). Option B is a plain source snapshot.

---

## Option A — full history import (recommended)

Uses `vitalico-v2.0.0-full-history.bundle`. A git *bundle* is an offline copy of an entire repository.

1. Create a new **empty** repository on GitHub (no README, no .gitignore — keep it truly empty). **Make it Private** (see Security below).

2. On your machine (needs git installed):

```bash
# clone the bundle into a working repo
git clone vitalico-v2.0.0-full-history.bundle vitalico
cd vitalico

# (optional) drop in the README so the GitHub front page looks right
#   copy README.md from this package into the repo root, then:
git add README.md && git commit -m "docs: add README"

# point it at your new GitHub repo and push everything
git remote set-url origin https://github.com/<your-username>/<your-repo>.git
git push origin --all      # all branches (main + checkpoint branches)
git push origin --tags     # v1.0.0, v2.0.0, rollback checkpoints
```

3. Verify on GitHub: the repo should show tags `v1.0.0`, `v2.0.0`, `checkpoint-pre-addfood-v2`, `food-logging-refactor-pre-serving-system`, and branches `main`, `checkpoint/pre-addfood-v2`, `checkpoint/pre-serving-system`.

---

## Option B — source snapshot only

Uses `vitalico-v2.0.0-source.zip` (the exact contents of the v2.0.0 tag, no history).

```bash
unzip vitalico-v2.0.0-source.zip -d vitalico
cd vitalico
# add the README.md from this package if you like
git init -b main
git add -A
git commit -m "Vitalico v2.0.0"
git tag v2.0.0
git remote add origin https://github.com/<your-username>/<your-repo>.git
git push -u origin main --tags
```

---

## ⚠️ Security — read before pushing

- **Keep the GitHub repository PRIVATE.** `android/vitalico-release.keystore` is the Android release signing key. Anyone who obtains it (plus its password) can publish updates that install over your app. If you ever want the repo public, remove the `android/` folder first and keep the keystore backed up elsewhere:
  ```bash
  git rm -r android && git commit -m "strip signing material for public release"
  ```
- No passwords or API secrets are inside the repo — Google OAuth secrets and the keystore password live as deployment secrets, not in code.

## What's in this package

| File | Purpose |
|---|---|
| `vitalico-v2.0.0-full-history.bundle` | Complete git history: every commit, branch, checkpoint and tag |
| `vitalico-v2.0.0-source.zip` | Clean source tree of exactly the v2.0.0 release |
| `README.md` | Ready-to-use GitHub front-page README |
| `GITHUB-SETUP.md` | This file |
