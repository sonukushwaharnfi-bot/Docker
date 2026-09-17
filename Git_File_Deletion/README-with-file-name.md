# Git File Deletion — Complete Hands-on

This README explains how Git handles file deletion in different situations.

## Topics Covered

* Untracked file deletion
* Tracked file deletion
* Staged file deletion
* `rm` vs `git rm`
* `git add` for deleted files
* `git rm -f`
* Commit and Push
* Safe Git workflow
* Understanding `git status`

---

# 1. Git File States

A file normally moves through these states:

```text
Untracked
    ↓
git add
    ↓
Staged
    ↓
git commit
    ↓
Tracked
```

For a tracked file deletion:

```text
Tracked File
    ↓
rm file
    ↓
Deleted but NOT staged
    ↓
git add file
    ↓
Deletion Staged
    ↓
git commit
    ↓
Deletion Committed
    ↓
git push
    ↓
Deleted from GitHub
```

---

# 2. Case 1 — Untracked File

Create a new file:

```bash
vim test2
```

Check Git status:

```bash
git status
```

Expected:

```text
Untracked files:
    test2
```

This means Git knows the file exists, but Git is **not tracking it yet**.

## Delete the untracked file

```bash
rm test2
```

Check:

```bash
git status
```

Expected:

```text
nothing to commit, working tree clean
```

### Why?

Because `test2` was never added or committed.

Therefore Git does not track its deletion.

### Important

For an untracked file:

```bash
rm test2
```

is enough.

No `git add`, `git commit`, or `git push` is required.

---

# 3. Case 2 — Tracked File Deleted Using `rm`

Create a file:

```bash
vim test1
```

Check:

```bash
git status
```

You will see:

```text
Untracked files:
    test1
```

Stage the file:

```bash
git add test1
```

Check:

```bash
git status
```

Expected:

```text
Changes to be committed:

    new file: test1
```

Commit:

```bash
git commit -m "add test1"
```

Now `test1` is a **tracked file**.

Check:

```bash
git status
```

Expected:

```text
nothing to commit, working tree clean
```

---

## Delete the tracked file

Now use normal Linux `rm`:

```bash
rm test1
```

Check:

```bash
git status
```

Expected:

```text
Changes not staged for commit:

    deleted: test1
```

This means:

> The file has been deleted from the filesystem, but the deletion has not been staged yet.

### Stage the deletion

```bash
git add test1
```

Check:

```bash
git status
```

Expected:

```text
Changes to be committed:

    deleted: test1
```

Commit:

```bash
git commit -m "delete test1"
```

Push:

```bash
git push origin main
```

Now the deletion is also reflected on GitHub.

---

# 4. Important Concept — `git add`

Many beginners think:

```bash
git add
```

means:

> Add a new file.

This is not correct.

`git add` means:

> **Stage a change for the next commit.**

The change can be:

* New file
* Modified file
* Deleted file

Examples:

```bash
git add newfile
```

Stages a new file.

```bash
git add modifiedfile
```

Stages modifications.

```bash
git add deletedfile
```

Stages a deletion.

Therefore:

```bash
git add test1
```

can mean:

> Stage the deletion of `test1`.

---

# 5. Case 3 — Using `git rm`

For a tracked file, Git provides:

```bash
git rm filename
```

Example:

```bash
git rm test4
```

Output:

```text
rm 'test4'
```

This command performs two actions:

```text
Delete file from filesystem
            +
Stage deletion in Git
```

Check:

```bash
git status
```

You will see:

```text
Changes to be committed:

    deleted: test4
```

The deletion is already staged.

You do NOT need:

```bash
git add test4
```

again.

Then:

```bash
git commit -m "delete test4"
git push origin main
```

---

# 6. Why `git rm test2` Does Not Work for an Untracked File

If `test2` is only an untracked file:

```text
Untracked files:
    test2
```

and you run:

```bash
git rm test2
```

Git will not remove it as a tracked file.

For an untracked file, use:

```bash
rm test2
```

---

# 7. Case 4 — Staged but NOT Committed File

Create:

```bash
vim test3
```

Initially:

```text
Untracked
```

Then:

```bash
git add .
```

Now:

```text
Changes to be committed:

    new file: test3
```

At this point `test3` is:

```text
Staged
but
NOT committed
```

If you run:

```bash
git rm test3
```

Git can show:

```text
error: the following file has changes staged in the index:
    test3
(use --cached to keep the file, or -f to force removal)
```

### Why?

Because `test3` is already staged.

Git wants you to explicitly decide what should happen.

---

# 8. `git rm -f`

To forcefully remove the staged file:

```bash
git rm -f test3
```

Output:

```text
rm 'test3'
```

Check:

```bash
git status
```

Expected:

```text
nothing to commit, working tree clean
```

Because `test3` was never committed.

Therefore:

```bash
git commit
```

and:

```bash
git push
```

are not required.

---

# 9. What Does `-f` Mean?

`-f` means:

```text
force
```

So:

```bash
git rm -f test3
```

means:

> Force Git to remove the file even though it has staged changes.

Use this carefully.

---

# 10. `rm` vs `git rm`

## `rm filename`

Example:

```bash
rm test1
```

This is a Linux command.

It removes the file from the filesystem.

For a tracked file, Git then detects:

```text
deleted: test1
```

but the deletion is initially **not staged**.

You then need:

```bash
git add test1
```

---

## `git rm filename`

Example:

```bash
git rm test4
```

This is a Git command.

It performs:

```text
Delete file
    +
Stage deletion
```

So you can directly:

```bash
git commit -m "delete test4"
```

---

# 11. Comparison of File Deletion

| Situation               | Command           | Result                               |
| ----------------------- | ----------------- | ------------------------------------ |
| Untracked file          | `rm test2`        | Deletes locally                      |
| Tracked file            | `rm test1`        | Deletes locally, deletion not staged |
| Deleted tracked file    | `git add test1`   | Stages deletion                      |
| Tracked file            | `git rm test4`    | Deletes + stages deletion            |
| Staged uncommitted file | `git rm -f test3` | Force removes                        |
| Committed deletion      | `git push`        | Updates GitHub                       |

---

# 12. Understanding `git status`

`git status` is one of the most important Git commands.

Always use:

```bash
git status
```

before important Git operations.

## Untracked File

```text
Untracked files:
    test2
```

Means:

> Git is not tracking this file.

---

## Staged New File

```text
Changes to be committed:

    new file: test3
```

Means:

> The new file is staged and will be included in the next commit.

---

## Deleted but NOT Staged

```text
Changes not staged for commit:

    deleted: test1
```

Means:

> The file has been deleted locally, but the deletion is not staged.

Use:

```bash
git add test1
```

---

## Deleted and Staged

```text
Changes to be committed:

    deleted: test4
```

Means:

> The deletion is ready for the next commit.

Use:

```bash
git commit -m "delete test4"
```

---

## Clean Repository

```text
nothing to commit, working tree clean
```

Means:

> There are no pending changes in the working directory or staging area.

---

# 13. Important — `git add .`

This is one of the most important Git concepts.

When you run:

```bash
git add .
```

it does NOT mean:

> Add only new files.

It means:

> **Stage all changes under the current directory.**

This includes:

```text
New files
Modified files
Deleted files
```

Therefore, if you accidentally delete tracked files and then run:

```bash
git add .
```

the deletions can also become staged.

If you then run:

```bash
git commit
git push
```

those deletions can reach GitHub.

---

# 14. Safe Git Workflow

Before staging changes:

```bash
git status
```

Then stage only the changes you actually want.

For one file:

```bash
git add filename
```

For one directory:

```bash
git add directory/
```

Then verify:

```bash
git status
```

Only after checking the staged changes:

```bash
git commit -m "meaningful message"
```

Then:

```bash
git push origin main
```

---

# 15. Recommended Daily Workflow

## Step 1 — Check status

```bash
git status
```

## Step 2 — Stage only intended changes

```bash
git add filename
```

or:

```bash
git add directory/
```

## Step 3 — Verify staged changes

```bash
git status
```

## Step 4 — Commit

```bash
git commit -m "meaningful message"
```

## Step 5 — Push

```bash
git push origin main
```

---

# 16. Golden Rule

Before using:

```bash
git add .
```

always check:

```bash
git status
```

After using:

```bash
git add .
```

check again:

```bash
git status
```

Look carefully at:

```text
Changes to be committed
```

Make sure only the changes you actually want are listed.

---

# 17. Final Mental Model

Think about Git like this:

```text
WORKING DIRECTORY
       ↓
    git add
       ↓
STAGING AREA
       ↓
   git commit
       ↓
 LOCAL REPOSITORY
       ↓
    git push
       ↓
    GITHUB
```

For deletion using `rm`:

```text
Tracked File
     ↓
  rm file
     ↓
Deleted locally
     ↓
git add file
     ↓
Deletion staged
     ↓
git commit
     ↓
Deletion saved in Git history
     ↓
git push
     ↓
File deleted from GitHub
```

For direct Git deletion:

```text
Tracked File
     ↓
git rm file
     ↓
Delete + Stage
     ↓
git commit
     ↓
git push
```

---

# 18. Practical Examples Used

During this hands-on practice, we used the following demo names:

### `test1`

Tracked file deletion using:

```bash
rm test1
git add test1
git commit
git push
```

### `test2`

Untracked file deletion using:

```bash
rm test2
```

### `test3`

Staged but uncommitted file deletion using:

```bash
git rm -f test3
```

### `test4`

Tracked file deletion using:

```bash
git rm test4
git commit
git push
```

---

# 19. Quick Cheat Sheet

### Create a file

```bash
vim test1
```

### Check status

```bash
git status
```

### Stage one file

```bash
git add test1
```

### Stage everything

```bash
git add .
```

### Delete untracked file

```bash
rm test2
```

### Delete tracked file

```bash
git rm test4
```

### Force delete staged file

```bash
git rm -f test3
```

### Stage deletion after using `rm`

```bash
git add test1
```

### Commit

```bash
git commit -m "message"
```

### Push

```bash
git push origin main
```

### Check history

```bash
git log --oneline
```

---

# 20. Main Lessons

Remember these points:

1. **Git tracks changes, not just files.**
2. A file deletion is also a Git change.
3. `rm file` only deletes the file from the filesystem.
4. `git rm file` deletes the file and stages the deletion.
5. `git add file` can stage a deletion.
6. `git rm -f file` forcefully removes a staged file.
7. An untracked file does not need a Git commit when deleted.
8. `git add .` stages new, modified, and deleted files.
9. Always run `git status` before and after staging.
10. Commit only the changes you actually want to save.
11. Push only after verifying the commit.

## Golden Rule

```text
git status
     ↓
Stage only what you want
     ↓
git status
     ↓
git commit
     ↓
git push
```

**Git is all about understanding what state your files are in.**

