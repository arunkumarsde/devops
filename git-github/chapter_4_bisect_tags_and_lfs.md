# Git Bisect

Used to find the first commit that introduced a bug.

```
git bisect start
git bisect bad <commit>-> if no commit passed, head is marked as bad
git bisect good <commit>
```

Then check pointer will move to the middle commit between good and bad.
Then you can mark check pointer as `bad` or `good`.

# Git tag

```
git tag <tagname> <commit>
git tag v1.0 -> head will have the tag v1.0
git tag -a <tagname> -m "Tag message" -> Creates an annotated tag
git show <tagname> -> to see the changes of commit having the following tag
```
