# Git and GitHub — Commands and Concepts

A point-to-point reference for branch management, rebase, tracking, stash, history, and related commands.

---

## Branch — Create and Delete

- **`git branch <branch>`** — Creates a new branch with the given name. Does not switch to it.
- **`git branch -d <branch>`** — Deletes the specified branch only if it has been fully merged into its upstream branch, so no unmerged changes are lost.
- **`git branch -D <branch>`** — Force-deletes the specified branch, even if it has unmerged changes. Use with care.

---

## Rebase

- **`git rebase <branch>`** — Rebases the current branch onto `<branch>`. Replays your commits on top of the latest commit of `<branch>`.

---

## Listing Branches

- **`git branch`** — Lists all local branches. Current branch is marked with `*`.
- **`git branch -r`** — Lists all remote-tracking branches (e.g. `origin/main`).
- **`git branch -a`** — Lists both local and remote branches.

---

## Searching and Filtering Branches

- **`git branch --list "*feature*"`** — Lists branches whose names match the given pattern (e.g. containing `feature`).
- **`git branch --sort=-committerdate`** — Lists branches sorted by most recent commit (recently active first).
- **`git branch --merged`** — Lists branches that have been merged into the current branch.
- **`git remote prune origin`** — Removes remote-tracking branches that no longer exist on the remote (e.g. after someone deletes them on GitHub).

---

## Listing and Managing Tracked Files

- **`git ls-files`** — Lists all files currently tracked by Git in the repository.
- **`git rm <filename>`** — Removes the file from the working directory and stages the removal for the next commit.
- **`git rm -r <directoryname>`** — Recursively removes the directory and all its contents from the working tree and stages the removal.
- **`git rm --cached <filename>`** — Stops tracking the file (removes from the index) but keeps it on disk. The file becomes untracked.

---

## Switch vs Checkout

- **`git switch <branch>`** — Switches to the given branch. Purpose-built for branch switching; does not touch files by default.
- **`git checkout <branch>`** — Can switch branches and also restore files (e.g. `git checkout -- file`). More overloaded.
- **Summary:** Prefer **`git switch`** for changing branches; use **`git checkout`** when you need to restore files or use legacy workflows.

---

## Pull with Rebase and Merge Strategies

- **`git pull origin main --rebase`** — Fetches from `origin` and rebases your current branch on top of `origin/main` instead of merging. Keeps history linear.
- **`git merge --ff-only`** — Merges only if it can be done as a fast-forward (no merge commit). Fails otherwise; useful to avoid accidental merge commits.

---

## Common Ancestor

- **`git merge-base main feature`** — Prints the best common ancestor commit of `main` and `feature`. Useful for scripts and understanding divergence.

---

## Unstage and Untrack

- **`git restore --staged .` or `git restore --staged <file_path>`** — Unstages the given path(s). Leaves files in the working directory; they remain tracked.
- **`git rm --cached .` or `git rm --cached <file_path>`** — Removes the given path(s) from the index (stops tracking). Files stay on disk and become untracked.

---

## Stash

- **`git stash apply 'stash@{1}'`** — Applies the stash at index `1` to the working tree. The stash entry is kept in the stash list.
- **`git stash drop 'stash@{1}'`** — Removes the stash entry at index `1` from the stash list without applying it.
- **`git stash pop`** — Applies the most recent stash and then drops it (equivalent to apply + drop for `stash@{0}`).

---

## git log vs git reflog

- **`git log`** — Shows commit history along branch structure. Only includes commits reachable from the current branch (and refs); “normal” history view.
- **`git reflog`** — Shows a log of where HEAD (and other refs) have been: every checkout, reset, merge, rebase, etc. Helps recover “lost” commits after a reset or rebase.

---

## Amend and Rebase Interactive

- **`git commit --amend`** — Opens the editor to change the last commit message (or add more changes if you stage and then amend). Replaces the previous commit.
- **`git rebase -i HEAD~3`** — Starts an interactive rebase over the last 3 commits. In the todo list you can **pick**, **reword**, **edit**, **squash**, or **drop** commits. Use **reword** to change a specific commit message.
- After rewriting history (amend or rebase), update the remote with: **`git push origin main --force`** (or `--force-with-lease` for safety).

---

## Inspecting Commits and Diffs

- **`git show <commit>`** — Shows the commit metadata and the diff introduced by that commit.
- **`git diff <commit1> <commit2>`** — Shows the diff between two commits (e.g. two commit hashes or branch tips).
- **`gitk`** — GUI tool to browse commit history, branches, and diffs. Run with **`gitk`** in the repo (if installed).

---

## Quick Reference

| Task                     | Command / approach                          |
|--------------------------|---------------------------------------------|
| Create branch            | `git branch <branch>`                       |
| Delete merged branch     | `git branch -d <branch>`                    |
| Force delete branch      | `git branch -D <branch>`                    |
| Rebase onto branch       | `git rebase <branch>`                       |
| List local branches      | `git branch`                                |
| List remote branches     | `git branch -r`                             |
| List all branches        | `git branch -a`                             |
| List by pattern          | `git branch --list "*feature*"`             |
| Recently active branches | `git branch --sort=-committerdate`          |
| Merged branches          | `git branch --merged`                       |
| Prune stale remote refs  | `git remote prune origin`                   |
| List tracked files       | `git ls-files`                              |
| Remove from disk + stage | `git rm <file>` / `git rm -r <dir>`         |
| Untrack, keep on disk     | `git rm --cached <file>`                    |
| Unstage                  | `git restore --staged .` or `<file_path>`   |
| Pull with rebase         | `git pull origin main --rebase`             |
| Merge only fast-forward  | `git merge --ff-only`                       |
| Common ancestor          | `git merge-base main feature`               |
| Stash apply (keep)       | `git stash apply 'stash@{1}'`               |
| Stash drop               | `git stash drop 'stash@{1}'`                |
| Stash apply + drop       | `git stash pop`                             |
| Change last commit       | `git commit --amend`                        |
| Edit last N commits      | `git rebase -i HEAD~N` then push `--force`  |
| Show one commit          | `git show <commit>`                         |
| Diff two commits         | `git diff <commit1> <commit2>`              |
| History GUI              | `gitk`                                      |
