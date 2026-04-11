# Offline Git Workflow Guide

Use this guide when working offline and syncing later, without breaking history.

## 1) One-Time Setup (run once)

```powershell
git config --global pull.rebase false
git config --global rebase.autoStash true
git config --global fetch.prune true
```

## 2) Start of Every Session

```powershell
git status -sb
git branch backup-$(Get-Date -Format "yyyyMMdd-HHmm")
```

This gives you a restore point before any risky operation.

## 3) Work Offline

```powershell
git add .
git commit -m "your message"
```

## 4) Sync Safely When Internet Is Back

```powershell
git switch main
git fetch origin
git merge origin/main
```

If there are conflicts:

1. Open conflicted files.
2. Remove conflict markers:
   - `<<<<<<<`
   - `=======`
   - `>>>>>>>`
3. Keep the final content you want.
4. Finish:

```powershell
git add .
git commit
```

Then push:

```powershell
git push origin main
```

## 5) Emergency Recovery Commands

### Rebase is stuck

```powershell
git rebase --abort
```

### Merge is stuck

```powershell
git merge --abort
```

### Detached HEAD

```powershell
git switch main
```

### Unrelated histories error

```powershell
git merge origin/main --allow-unrelated-histories
```

### Find previous good state

```powershell
git reflog -n 20
```

If you must hard reset (destructive):

```powershell
git reset --hard <good_commit_hash>
```

## 6) Vim Prompt During Git Commit

When Git opens commit message editor:

- Save and exit: `Esc`, `:wq`, `Enter`
- Exit without saving: `Esc`, `:q!`, `Enter`

## 7) Rules to Avoid Breakage

1. Avoid `git pull --rebase` unless you fully understand rebase conflict flow.
2. Create a backup branch before major sync.
3. Prefer `fetch + merge` for safety.
4. Before push, always confirm clean state:

```powershell
git status -sb
```

Expected clean output:

```text
## main
```

