# Git File Deletion — Complete Hands-on Guide

This README explains how Git handles file deletion in different situations.

The main purpose of this practice is to understand the difference between:

* Untracked file deletion
* Tracked file deletion
* Staged file deletion
* `rm` vs `git rm`
* `git add` for a deleted file
* `git rm -f`
* Commit and Push
* Safe Git workflow

---

## 1. Git File States

A file can be in different states:

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

When a tracked file is deleted:

```text
Tracked
    ↓
rm file
    ↓
Deleted but NOT staged
    ↓
git add file
    ↓
Deletion staged
    ↓
git commit
    ↓
Deletion committed
    ↓
git push
    ↓
File deleted from GitHub
```

---

# 2. Case 1 — Untracked File

Create a new file:

```bash
vim 23
```

Check status:

```bash
git status
```

Output:

```text
Untracked files:
    23
```

This means Git knows that a file exists, but Git is **not tracking it yet**.

### Delete the file

```bash
rm 23
```

Check:

```bash
git status
```

Output:

```text
nothing to commit, working tree clean
```

### Why?

Because `23` was never added or committed.

Git does not care about the deletion of a file that it was never tracking.

### Important

For an untracked file:

```bash
rm 23
```

is enough.

No:

```bash
git add
git commit
git push
```

is required.

---

# 3. Case 2 — Tracked File Deleted Using `rm`

Create a file:

```bash
vim 32
```

Check:

```bash
git status
```

It will show:

```text
Untracked files:
    32
```

Stage it:

```bash
git add 32
```

Check:

```bash
git status
```

It will show:

```text
new file: 32
```

Commit:

```bash
git commit -m "add 32"
```

Now `32` is a **tracked file**.

Check:

```bash
git status
```

Output:

```text
nothing to commit, working tree clean
```

---

## Delete the tracked file

Now:

```bash
rm 32
```

Check:

```bash
git status
```

Output:

```text
Changes not staged for commit:

    deleted: 32
```

This is very important.

The file is deleted from the server filesystem, but Git has not yet staged the deletion.

### Stage the deletion

```bash
git add 32
```

Check:

```bash
git status
```

Now:

```text
Changes to be committed:

    deleted: 32
```

Commit:

```bash
git commit -m "delete 32"
```

Push:

```bash
git push origin main
```

Now the deletion is also reflected on GitHub.

---

# 4. Important Concept — `git add` Does NOT Mean Only "Add File"

Many beginners think:

```bash
git add
```

means:

> Add a new file.

This is not correct.

`git add` means:

> **Stage the changes for the next commit.**

Changes can be:

* New files
* Modified files
* Deleted files

For example:

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

So:

```bash
git add 32
```

can mean:

> Stage the deletion of `32`.

---

# 5. Case 3 — Using `git rm`

For a tracked file, Git provides:

```bash
git rm filename
```

Example:

```bash
git rm 64
```

Output:

```text
rm '64'
```

This command does two things:

```text
Delete file from filesystem
        +
Stage deletion in Git
```

So after:

```bash
git rm 64
```

check:

```bash
git status
```

You will see:

```text
Changes to be committed:

    deleted: 64
```

You do NOT need to run:

```bash
git add 64
```

again.

The deletion is already staged.

Then:

```bash
git commit -m "delete 64"
git push origin main
```

---

# 6. Why `git rm 64` Worked

When we had:

```text
64
```

as a committed/tracked file:

```bash
git rm 64
```

worked successfully.

Git deleted the file and staged the deletion.

Then:

```bash
git status
```

showed:

```text
deleted: 64
```

---

# 7. Why `git rm 23` Failed

When we created:

```text
23
```

it was only:

```text
Untracked
```

It was never committed.

Therefore:

```bash
git rm 23
```

gave:

```text
fatal: pathspec '23' did not match any files
```

Because `git rm` is primarily used for files Git is tracking.

For an untracked file:

```bash
rm 23
```

is enough.

---

# 8. Case 4 — Staged File and `git rm`

This was another important hands-on example.

Create:

```bash
vim 88434
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

    new file: 88434
```

The file is staged but not committed.

If we run:

```bash
git rm 88434
```

Git gives:

```text
error: the following file has changes staged in the index:
    88434
(use --cached to keep the file, or -f to force removal)
```

### Why?

Because `88434` has already been staged.

Git wants you to explicitly tell it what you want to do.

---

# 9. `git rm -f`

To forcefully remove the staged file:

```bash
git rm -f 88434
```

Output:

```text
rm '88434'
```

Then:

```bash
git status
```

Output:

```text
nothing to commit, working tree clean
```

Because `88434` was never committed.

Therefore:

```bash
git commit
```

and:

```bash
git push
```

are NOT required.

---

# 10. `git rm -f` Meaning

The `-f` means:

```text
force
```

So:

```bash
git rm -f filename
```

means:

> Force Git to remove the file even when it has staged changes.

Use this carefully.

---

# 11. `rm` vs `git rm`

## `rm filename`

Normal Linux command:

```bash
rm 32
```

It removes the file from the filesystem.

Git then detects:

```text
deleted: 32
```

but the deletion is initially **not staged**.

You need:

```bash
git add 32
```

---

## `git rm filename`

Git command:

```bash
git rm 32
```

It:

```text
Deletes the file
+
Stages the deletion
```

So you can directly commit:

```bash
git commit -m "delete 32"
```

---

# 12. Important Comparison

| Situation               | Command          | Result                               |
| ----------------------- | ---------------- | ------------------------------------ |
| Untracked file          | `rm file`        | Deletes locally                      |
| Tracked file            | `rm file`        | Deletes locally, deletion not staged |
| Tracked file            | `git add file`   | Stages deletion                      |
| Tracked file            | `git rm file`    | Deletes + stages deletion            |
| Staged uncommitted file | `git rm -f file` | Force removes                        |
| Committed deletion      | `git push`       | Updates GitHub                       |

---

# 13. What Does `git status` Tell Us?

Always use:

```bash
git status
```

before doing important Git operations.

### Untracked

```text
Untracked files:
    23
```

Means:

> Git is not tracking this file.

---

### Staged New File

```text
Changes to be committed:

    new file: 88434
```

Means:

> File is staged and will be included in the next commit.

---

### Deleted but Not Staged

```text
Changes not staged for commit:

    deleted: 32
```

Means:

> File has been deleted locally, but deletion is not staged yet.

Use:

```bash
git add 32
```

---

### Deleted and Staged

```text
Changes to be committed:

    deleted: 64
```

Means:

> Deletion is ready for the next commit.

Use:

```bash
git commit -m "delete 64"
```

---

### Clean

```text
nothing to commit, working tree clean
```

Means:

> Working directory and staging area have no pending changes.

---

# 14. The Most Important Rule — Be Careful With `git add .`

We previously faced an important situation.

We had deleted multiple tracked Docker practice files.

Then:

```bash
git add .
```

was executed.

Git staged:

```text
Deleted files
```

along with any other changes.

Then:

```bash
git commit
git push
```

caused those deletions to appear on GitHub.

### Remember:

```bash
git add .
```

does NOT mean:

> Add only new files.

It means:

> **Stage all changes under the current directory.**

That includes:

```text
New files
Modified files
Deleted files
```

---

# 15. Safe Git Workflow

Before staging:

```bash
git status
```

Then stage only what you actually want.

For one file:

```bash
git add filename
```

For one directory:

```bash
git add directory/
```

Then always verify:

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

# 16. Recommended Daily Workflow

```bash
git status
```

### Step 1 — Check changes

```bash
git status
```

### Step 2 — Stage only intended changes

```bash
git add filename
```

or:

```bash
git add directory/
```

### Step 3 — Verify

```bash
git status
```

### Step 4 — Commit

```bash
git commit -m "meaningful message"
```

### Step 5 — Push

```bash
git push origin main
```

---

# 17. Golden Rule

Before using:

```bash
git add .
```

**Always run:**

```bash
git status
```

And after:

```bash
git add .
```

**Run again:**

```bash
git status
```

Check exactly what is under:

```text
Changes to be committed
```

Only then commit.

---

# 18. Quick Cheat Sheet

### Create a file

```bash
vim file
```

### Check status

```bash
git status
```

### Stage one file

```bash
git add file
```

### Stage everything

```bash
git add .
```

### Delete untracked file

```bash
rm file
```

### Delete tracked file

```bash
git rm file
```

### Delete staged file forcefully

```bash
git rm -f file
```

### Stage deletion after using `rm`

```bash
git add file
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

# 19. Final Mental Model

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

For deletion:

```text
rm file
   ↓
Working directory file deleted
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

Or directly:

```text
git rm file
   ↓
Delete + Stage
   ↓
git commit
   ↓
git push
```

---

## Practice Completed

During this hands-on practice we worked with:

* `32` → tracked file deletion using `rm` + `git add`
* `23` → untracked file deletion using normal `rm`
* `88434` → staged but uncommitted file deletion using `git rm -f`
* `64` → tracked file deletion using `git rm`
* `git status` → checking Git state
* `git add .` → understanding that all changes, including deletions, can be staged
* `git commit` → saving changes in Git history
* `git push origin main` → sending commits to GitHub

### Main Lesson

> **Git tracks changes, not just files.**

A deletion is also a Git change.

And:

> **`git add` means "stage this change", not simply "add this file".**

