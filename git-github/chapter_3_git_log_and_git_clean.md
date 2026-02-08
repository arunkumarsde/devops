# Git Log and Git Clean

A point-to-point reference for viewing and filtering commit history with `git log`, and cleaning untracked files with `git clean`.

---

## Git Log — Basics

- **`git log`** — Shows the commit history for the current branch. Displays full commit hash, author, date, and message. Use **Space** to scroll, **q** to quit.
- **`git log --oneline`** — One line per commit: short hash and subject. Compact view for quick scanning.

---

## Custom Format

- **`git log --pretty=format:"%h - %an, %ar : %s"`** — Custom output:
  - **%h** — Short commit hash
  - **%an** — Author name
  - **%ar** — Author date, relative (e.g. "2 weeks ago")
  - **%s** — Commit subject (first line of message)  
    Example: `a1b2c3d - Jane Doe, 3 days ago : Add login feature`

---

## Graph and All Branches

- **`git log --oneline --graph --all`** — Shows a text graph of branches and merges (**--graph**) for **all** branches (**--all**), one line per commit (**--oneline**). Useful to see how branches diverge and merge.

---

## Filtering by Author

- **`git log --author="Author Name"`** — Lists only commits by the given author. Matching is case-insensitive and can be a substring (e.g. `"Jane"` matches "Jane Doe").

---

## Filtering by Date

- **`git log --since="2023-01-01" --until="2023-12-31"`** — Commits between the two dates (inclusive of start, exclusive of end). Also accepts relative dates, e.g. `--since="2 weeks ago"`, `--until="yesterday"`.

---

## Filtering by Message (Grep)

- **`git log --grep="keyword"`** — Shows only commits whose message contains **keyword**. Use **--all-match** with multiple **--grep** to require all patterns; without it, any pattern match is enough.

---

## Filtering by File

- **`git log -- <file-path>`** — Shows only commits that changed **&lt;file-path&gt;**. The `--` separates options from paths. Use for file-specific history.

---

## Combining Options

You can combine filters and format:

```bash
git log --oneline --graph --all --author="Jane" --since="2024-01-01"
git log --oneline --pretty=format:"%h %s" -- path/to/file
```

---

## Quick Reference — git log

| Goal              | Command                                             |
| ----------------- | --------------------------------------------------- |
| Compact one-liner | `git log --oneline`                                 |
| Custom format     | `git log --pretty=format:"%h - %an, %ar : %s"`      |
| Graph + all refs  | `git log --oneline --graph --all`                   |
| By author         | `git log --author="Author Name"`                    |
| By date range     | `git log --since="2023-01-01" --until="2023-12-31"` |
| By message        | `git log --grep="keyword"`                          |
| History of a file | `git log -- <file-path>`                            |

---

## Common format placeholders

| Placeholder | Meaning                           |
| ----------- | --------------------------------- |
| %h          | Short hash                        |
| %H          | Full hash                         |
| %an         | Author name                       |
| %ae         | Author email                      |
| %ar         | Author date (relative)            |
| %ad         | Author date (format with --date=) |
| %s          | Subject                           |
| %b          | Body                              |

# Git Clean

git clean removes untracked files and directories from the working directory to keep the repository clean.

- Removes untracked files from the working directory.
- Does not affect tracked or staged files.
- -d removes untracked directories.
- -n performs a dry run (shows what would be deleted).
- -f is required to force deletion.
- -x removes ignored and untracked files.
- -fx force-removes both ignored and untracked files.
- Commonly used before branch switching or fresh builds.

```
Note: git clean is destructive and irreversible, so always use -n first to preview changes.
```

- git clean -n -> This lists all untracked files and directories that would be removed.
- git clean -f -> force clean untracked files
- git clean -fd -> force remove untracked files and directories
- git clean -i -> clean in interactive mode or git clean --interactive
- git clean -fX -> To remove only ignored files:
- git clean -fx -> To remove both ignored and untracked files.
