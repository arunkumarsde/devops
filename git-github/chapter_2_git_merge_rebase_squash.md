# Git Merge, Rebase, Squash, fast-forward and no fast-forward

1. Git Merge

It creates a new merge commit:

```
A---B---C-------M   main
     \         /
      D---E---     feature
```

2. Fast Forward Merge

Only if main has not moved since feature branched.

```
git merge feature

A---B   main
     \
      C---D   feature
```

3. No Fast Forward Merge

Forces merge commit even when FF is possible.

```
git merge --no-ff feature

A---B-------M
     \     /
      C---D
```

4. Git Rebase

```
git checkout feature
git rebase main

or

git merge --rebase feature

A---B---C   main
         \
          D'---E'   feature
```

5. Squash Merge

All feature commits → one single commit. Branch history is not preserved.

```
git checkout main
git merge --squash feature
git commit

A---B---C---S   main
```

6. Rebase + Fast-Forward
   Linear history and No merge commit

```
git checkout feature
git rebase main
git checkout main
git merge feature

A---B---C---D---E
```

![Git Merge, rebase and sqaush](/images/git-github/git-merge-rebase-sqaush.py.png)

## Comparion Table

| Technique    | Merge Commit | Rewrites History | Linear | Best For               |
| ------------ | ------------ | ---------------- | ------ | ---------------------- |
| Fast-forward | ❌           | ❌               | ✅     | small features         |
| Merge commit | ✅           | ❌               | ❌     | team work              |
| --no-ff      | ✅           | ❌               | ❌     | explicit feature merge |
| Rebase       | ❌           | ✅               | ✅     | local cleanup          |
| Squash       | ❌           | ❌               | ✅     | PR merge               |
| Rebase + FF  | ❌           | ❌               | ✅     | GitHub flow            |
