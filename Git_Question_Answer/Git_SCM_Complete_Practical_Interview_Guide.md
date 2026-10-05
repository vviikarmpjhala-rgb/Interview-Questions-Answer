# Git SCM – Complete Practical & Interview Guide

## 1. Git Basics

### What is Git?
Git is a distributed version control system used to track code changes, maintain history, work with branches, collaborate with teams, and safely manage releases.

### Git architecture

```text
Working Directory
       |
       | git add
       v
Staging Area
       |
       | git commit
       v
Local Repository
       |
       | git push
       v
Remote Repository
```

Remote changes generally come back through:

```text
Remote Repository
       |
       | git fetch
       v
Remote-tracking branch
       |
       | merge / rebase / pull
       v
Local branch
```

---

# 2. Git Installation & Configuration

## Check Git version

```bash
git --version
```

**Use when:** You want to verify Git is installed.

## Configure username

```bash
git config --global user.name "Vviikarm P Jhala"
```

**Use when:** Setting the author name for commits.

## Configure email

```bash
git config --global user.email "your-email@example.com"
```

**Use when:** Setting the author email for commits.

## Check configuration

```bash
git config --list
```

or:

```bash
git config --global --list
```

---

# 3. Repository Commands

## Initialize a repository

```bash
git init
```

**Use when:** Starting Git tracking in an existing local project.

Example:

```bash
mkdir myproject
cd myproject
git init
```

## Clone a remote repository

```bash
git clone <repository-url>
```

**Use when:** You want a complete local copy of an existing remote repository.

Example:

```bash
git clone https://github.com/example/project.git
```

## Check repository status

```bash
git status
```

**Use when:** You want to know:
- Modified files
- Untracked files
- Staged files
- Current branch
- Whether changes are ready to commit

**Best practice:** Run `git status` frequently.

---

# 4. Working Directory, Staging and Commit

## Add one file

```bash
git add file.txt
```

**Use when:** You want to stage one specific file.

## Add multiple files

```bash
git add file1.txt file2.txt
```

## Add all changes

```bash
git add .
```

**Use when:** You intentionally want to stage all changes in the current directory.

## Commit

```bash
git commit -m "Add application configuration"
```

**Use when:** You want to permanently record staged changes in the local Git repository.

## Add and commit tracked files

```bash
git commit -am "Fix application configuration"
```

**Important:** This does NOT stage brand-new untracked files.

---

# 5. View Commit History

## Normal log

```bash
git log
```

**Use when:** You need detailed commit history.

## Compact history

```bash
git log --oneline
```

**Use when:** You need a quick list of commits.

## Very useful history command

```bash
git log --oneline --graph --decorate --all -20
```

Meaning:

- `git log` = commit history
- `--oneline` = one line per commit
- `--graph` = branch/merge graph
- `--decorate` = show HEAD/branch/tag names
- `--all` = include all refs/branches
- `-20` = latest 20 commits

**Use when:** Troubleshooting branch divergence or local/remote commit mismatch.

Example:

```text
* d1b801b (HEAD -> master, origin/master) telecom-hybrid_1st-copy
```

This means both local `master` and `origin/master` currently point to the same commit.

---

# 6. Git Diff

## See unstaged changes

```bash
git diff
```

**Use when:** You changed files but have not staged them.

## See staged changes

```bash
git diff --staged
```

or:

```bash
git diff --cached
```

**Use when:** You want to review exactly what will go into the next commit.

## Compare two commits

```bash
git diff <commit1> <commit2>
```

## Compare local branch with remote

```bash
git fetch origin
git diff main origin/main
```

---

# 7. Branching

## List local branches

```bash
git branch
```

The `*` shows the current branch.

## List all local and remote branches

```bash
git branch -a
```

## Create a branch

```bash
git branch feature/login
```

## Switch branch

```bash
git switch feature/login
```

## Create and switch

```bash
git switch -c feature/login
```

This is the preferred modern command.

Older equivalent:

```bash
git checkout -b feature/login
```

## Delete local branch

```bash
git branch -d feature/login
```

**Use when:** Branch has already been merged.

Force delete:

```bash
git branch -D feature/login
```

**Warning:** `-D` can delete an unmerged branch.

---

# 8. Remote Repository

## List remotes

```bash
git remote -v
```

Typical output:

```text
origin  https://github.com/example/project.git (fetch)
origin  https://github.com/example/project.git (push)
```

## Add a remote

```bash
git remote add origin <repository-url>
```

**Use when:** A local repository needs to be connected to a remote repository.

## Change remote URL

```bash
git remote set-url origin <new-url>
```

## Show remote details

```bash
git remote show origin
```

---

# 9. Git Fetch vs Git Pull

## Fetch

```bash
git fetch origin
```

**Meaning:** Download remote updates and update remote-tracking references without changing your current working branch.

Think:

```text
"Remote mein kya change hua hai? Pehle dekhte hain."
```

## Fetch a specific branch

```bash
git fetch origin main
```

This specifically fetches the remote `main` branch.

### Are these exactly the same?

```bash
git fetch origin
git fetch origin main
```

No.

- `git fetch origin` = fetch updates from the remote generally.
- `git fetch origin main` = specifically fetch `main`.

## Pull

```bash
git pull
```

Conceptually:

```text
git fetch
+
merge/rebase
```

For example:

```bash
git pull origin main
```

fetches the remote `main` and integrates it into the current branch according to the configured/default pull behavior.

### Golden rule

```text
FETCH = Download/update remote information, don't integrate into current branch.

PULL  = Fetch + integrate into current branch.
```

---

# 10. Push

## Push current branch

```bash
git push
```

## Push specific branch

```bash
git push origin main
```

## Push a new branch and set upstream

```bash
git push -u origin feature/login
```

After this, usually:

```bash
git push
```

is enough.

---

# 11. Upstream Tracking

Check tracking information:

```bash
git branch -vv
```

Example:

```text
* main  abc1234 [origin/main] Latest changes
```

This means local `main` tracks `origin/main`.

Set upstream:

```bash
git push -u origin main
```

---

# 12. Merge

## What is merge?

Merge combines histories of two branches.

Example:

```text
A---B---C       main
     \
      D---E     feature
```

Run:

```bash
git switch main
git merge feature
```

Possible result:

```text
A---B---C-------M
     \         /
      D---E----
```

`M` is a merge commit.

## When to use merge

Use merge when:

- You want to preserve branch history.
- You are integrating a feature branch into a shared branch.
- You do not want to rewrite shared history.
- A team uses merge commits/PR merge as its workflow.
- Audit/history of branch integration matters.

---

# 13. Rebase

## What is rebase?

Rebase moves/replays your commits on top of another base.

Before:

```text
A---B---C       main
     \
      D---E     feature
```

Run:

```bash
git switch feature
git rebase main
```

After:

```text
A---B---C---D'---E'
```

The commits get new IDs because history is rewritten.

## When to use rebase

Good use cases:

- Updating your own feature branch with latest `main`.
- Keeping feature history linear.
- Cleaning up local/private commits before creating a PR.
- Avoiding unnecessary merge commits.

Example:

```bash
git fetch origin
git switch feature/login
git rebase origin/main
```

## Golden rule

Do not casually rebase a shared/public branch.

Why?

Because rebase rewrites commit history.

---

# 14. Merge vs Rebase

| Topic | Merge | Rebase |
|---|---|---|
| Preserves branch topology | Yes | No |
| Creates merge commit | Often | No |
| Rewrites commits | No | Yes |
| Good for shared history | Yes | Be careful |
| Good for private feature branch | Yes | Very good |
| History | Can be non-linear | Linear/clean |

### Easy memory

```text
MERGE  = Preserve history
REBASE = Clean/linear history
```

---

# 15. Pull with Rebase

```bash
git pull --rebase origin main
```

Meaning:

1. Fetch latest `main` from `origin`.
2. Temporarily move your local commits aside.
3. Update your branch with remote changes.
4. Replay your local commits on top.

Example:

Before:

```text
A---B---C   local
     \
      D     remote
```

After rebase:

```text
A---B---D---C'
```

Use when your local branch has commits and remote has new commits and you want a clean linear history.

If conflict occurs:

```bash
git status
```

Resolve files, then:

```bash
git add .
git rebase --continue
```

Abort:

```bash
git rebase --abort
```

---

# 16. Merge Conflict

A conflict happens when Git cannot automatically decide which change to keep.

Example:

```text
<<<<<<< HEAD
Local change
=======
Remote change
>>>>>>> origin/main
```

Workflow:

```bash
git status
```

Open conflicted file and manually choose/fix the correct content.

Then:

```bash
git add <file>
```

For merge:

```bash
git commit
```

For rebase:

```bash
git rebase --continue
```

Abort merge:

```bash
git merge --abort
```

Abort rebase:

```bash
git rebase --abort
```

---

# 17. Git Stash

## Stash changes

```bash
git stash
```

**Use when:** You have uncommitted changes but need to temporarily switch branches or perform another Git operation.

## Stash including untracked files

```bash
git stash -u
```

## List stashes

```bash
git stash list
```

## Apply latest stash

```bash
git stash apply
```

## Apply a specific stash

```bash
git stash apply stash@{1}
```

## Apply and remove from stash

```bash
git stash pop
```

## Delete a stash

```bash
git stash drop stash@{0}
```

## Delete all stashes

```bash
git stash clear
```

### Scenario

You are working on:

```text
feature/payment
```

Suddenly production issue requires switching to:

```text
hotfix
```

But you have uncommitted work.

```bash
git stash
git switch hotfix
```

After finishing hotfix:

```bash
git switch feature/payment
git stash pop
```

---

# 18. Undo Changes

## Discard unstaged change in a file

Modern command:

```bash
git restore file.txt
```

**Warning:** Local uncommitted changes in that file are discarded.

## Unstage a file

```bash
git restore --staged file.txt
```

This removes the file from staging but keeps your working-directory changes.

## Reset last commit but keep changes staged

```bash
git reset --soft HEAD~1
```

## Reset last commit and keep changes unstaged

```bash
git reset HEAD~1
```

Equivalent common form:

```bash
git reset --mixed HEAD~1
```

## Reset and discard changes

```bash
git reset --hard HEAD~1
```

**Danger:** Can permanently discard local work.

---

# 19. Revert – Safe Undo for Shared Branches

```bash
git revert <commit-id>
```

**Use when:** A bad commit is already pushed/shared and you want to undo its effect without rewriting history.

Example:

```text
A---B---C
```

Revert C:

```bash
git revert C
```

Result:

```text
A---B---C---R
```

`R` is a new commit that reverses C.

### Reset vs Revert

```text
RESET  = Move history pointer / potentially rewrite local history.

REVERT = Create a new commit that undoes an earlier commit.
```

For shared production branches, `revert` is generally safer.

---

# 20. Amend Commit

## Change the latest commit message

```bash
git commit --amend -m "Correct commit message"
```

## Add forgotten changes to latest commit

```bash
git add forgotten-file.txt
git commit --amend --no-edit
```

**Use:** When the latest commit has not been shared or you intentionally understand the history rewrite.

---

# 21. Cherry-pick

```bash
git cherry-pick <commit-id>
```

**Use when:** You want to bring one specific commit from another branch into your current branch.

Scenario:

```text
feature branch:
A---B---C---D
        ^
        bug fix
```

You want only C in `release`:

```bash
git switch release
git cherry-pick <C-commit-id>
```

Result:

```text
release:
A---B---C'
```

The cherry-picked commit gets a new commit ID.

---

# 22. Tags

## Create tag

```bash
git tag v1.0.0
```

## Annotated tag

```bash
git tag -a v1.0.0 -m "Production release 1.0.0"
```

## List tags

```bash
git tag
```

## Push tag

```bash
git push origin v1.0.0
```

Push all tags:

```bash
git push origin --tags
```

### Use case

Tags are useful for:

```text
v1.0.0
v1.1.0
v2.0.0
```

They can identify production releases.

---

# 23. Git Blame

```bash
git blame file.txt
```

**Use when:** You need to identify which commit/person last changed each line.

Useful for troubleshooting:

```text
"Who introduced this configuration?"
```

---

# 24. Git Show

```bash
git show <commit-id>
```

**Use when:** You want to inspect a specific commit and its changes.

Example:

```bash
git show d1b801b
```

---

# 25. Git Reflog

```bash
git reflog
```

**Very important recovery command.**

It shows where HEAD and branch references have moved locally.

Scenario:

You accidentally run:

```bash
git reset --hard HEAD~2
```

and think commits are lost.

Check:

```bash
git reflog
```

Find the previous commit and recover:

```bash
git reset --hard <commit-id>
```

### Important

Reflog is primarily a local recovery mechanism and is not the same as the shared remote history.

---

# 26. Clean Untracked Files

Preview:

```bash
git clean -n
```

Delete untracked files:

```bash
git clean -f
```

Delete untracked directories too:

```bash
git clean -fd
```

**Warning:** This can delete untracked files permanently.

---

# 27. .gitignore

Example:

```gitignore
*.log
.env
.terraform/
terraform.tfstate
terraform.tfstate.*
.vscode/
node_modules/
```

**Use:** Prevent files from being tracked.

If a file is already tracked, adding it to `.gitignore` does not automatically remove it from Git tracking.

To stop tracking:

```bash
git rm --cached file.txt
```

Then commit:

```bash
git commit -m "Stop tracking file"
```

---

# 28. Remote Branch Cleanup

Delete remote branch:

```bash
git push origin --delete feature/login
```

Clean stale remote-tracking branches:

```bash
git fetch --prune
```

or:

```bash
git remote prune origin
```

---

# 29. Find Branches Containing a Commit

```bash
git branch --contains <commit-id>
```

**Use:** Find which local branches contain a particular commit.

---

# 30. Compare Branches

Commits in feature but not main:

```bash
git log main..feature --oneline
```

Commits in main but not feature:

```bash
git log feature..main --oneline
```

Compare file changes:

```bash
git diff main..feature
```

---

# 31. Find a Commit by Message

```bash
git log --oneline --grep="login"
```

**Use:** Search commit messages.

---

# 32. Git Bisect

```bash
git bisect start
git bisect bad
git bisect good <known-good-commit>
```

Git helps identify which commit introduced a bug.

After testing each checkout:

```bash
git bisect good
```

or:

```bash
git bisect bad
```

Finish:

```bash
git bisect reset
```

**Use:** Large history + difficult regression where you know one old commit was good and current code is bad.

---

# 33. Common Production Workflow

```text
Developer
   |
   v
git clone
   |
   v
git switch -c feature/xyz
   |
   v
Code changes
   |
   v
git status
   |
   v
git diff
   |
   v
git add
   |
   v
git commit
   |
   v
git fetch origin
   |
   v
git rebase origin/main
   |
   v
Resolve conflicts if required
   |
   v
git push -u origin feature/xyz
   |
   v
Pull Request
   |
   v
Code Review
   |
   v
Merge into main
   |
   v
CI/CD
   |
   v
Production
```

---

# 34. Scenario: Local and Remote Are Different

First inspect:

```bash
git status
git fetch origin
git log --oneline --graph --decorate --all -20
```

### Remote is ahead

```text
Local:  A---B
Remote: A---B---C
```

Use:

```bash
git pull
```

or explicitly:

```bash
git pull --rebase origin main
```

if that matches your workflow.

### Local is ahead

```text
Local:  A---B---C
Remote: A---B
```

Use:

```bash
git push
```

### Both are ahead

```text
        C  local
       /
A---B
       \
        D  remote
```

First:

```bash
git fetch origin
```

Inspect:

```bash
git log --oneline --graph --decorate --all
```

Then, for a private/local feature branch:

```bash
git rebase origin/main
```

or use merge:

```bash
git merge origin/main
```

Resolve conflicts, test, then push.

---

# 35. Scenario: Accidentally Committed a Secret

If a secret is only in the latest local commit and has not been pushed:

```bash
git reset --soft HEAD~1
```

Remove secret from the staged content, add it to `.gitignore`, then recommit.

If the secret has already been pushed:

1. Revoke/rotate the secret immediately.
2. Remove it from the repository.
3. Use appropriate history cleanup if required.
4. Never assume deleting the file from the latest commit makes the secret safe.

**Security rule:** A leaked credential should be considered compromised.

---

# 36. Scenario: Wrong Branch Commit

If the commit is local and not shared, one possible approach:

```bash
git log --oneline
git reset --soft HEAD~1
git switch correct-branch
git commit -m "..."
```

If the commit is already shared, avoid rewriting shared history. Consider `cherry-pick` or `revert` depending on the situation.

---

# 37. Scenario: Need Only One Commit from Another Branch

Use:

```bash
git cherry-pick <commit-id>
```

Example:

```bash
git switch release
git cherry-pick abc1234
```

---

# 38. Scenario: Need to Temporarily Save Work

```bash
git stash -u
git switch another-branch
```

Return:

```bash
git switch original-branch
git stash pop
```

---

# 39. Scenario: Merge Conflict

```bash
git fetch origin
git merge origin/main
```

If conflict:

```bash
git status
```

Fix conflict markers:

```text
<<<<<<< HEAD
local
=======
remote
>>>>>>> origin/main
```

Then:

```bash
git add .
git commit
```

For rebase:

```bash
git add .
git rebase --continue
```

---

# 40. Scenario: Need to Undo a Production Commit

If the commit is already shared:

```bash
git revert <commit-id>
git push
```

Avoid using:

```bash
git reset --hard
git push --force
```

on a shared production branch unless there is an explicit, controlled reason and team approval.

---

# 41. Scenario: Recover Deleted/Lost Commit

Use:

```bash
git reflog
```

Find the required commit:

```bash
git reset --hard <commit-id>
```

Only use `--hard` when you understand what local changes will be discarded.

---

# 42. Scenario: Check Whether Local and Remote Are Synchronized

```bash
git fetch origin
git status
```

Useful detailed view:

```bash
git branch -vv
```

Graph:

```bash
git log --oneline --graph --decorate --all -20
```

If both:

```text
HEAD -> main
origin/main
```

point to the same commit, they are aligned at that reference point.

---

# 43. Important Command Cheat Sheet

| Command | Main purpose |
|---|---|
| `git init` | Initialize repository |
| `git clone` | Clone repository |
| `git status` | Check working state |
| `git add` | Stage changes |
| `git commit` | Save staged changes locally |
| `git log` | View history |
| `git diff` | Compare changes |
| `git branch` | Manage branches |
| `git switch` | Switch branches |
| `git merge` | Combine branch histories |
| `git rebase` | Replay commits on another base |
| `git fetch` | Download remote updates without integrating |
| `git pull` | Fetch + integrate |
| `git push` | Upload commits |
| `git stash` | Temporarily save work |
| `git reset` | Move/reset local history/state |
| `git restore` | Restore file/staging state |
| `git revert` | Safely undo a shared commit |
| `git cherry-pick` | Apply one specific commit |
| `git tag` | Mark a release/version |
| `git show` | Inspect a commit |
| `git blame` | Find line-level change history |
| `git reflog` | Recover local reference history |
| `git clean` | Remove untracked files |
| `git bisect` | Find regression-causing commit |

---

# 44. Intermediate Interview Questions & Answers

## Q1. What is Git?

**Answer:**

Git is a distributed version control system that tracks source-code changes and enables developers to work on branches, maintain history, collaborate, and integrate changes safely.

---

## Q2. Git vs GitHub?

**Answer:**

Git is the version control tool.

GitHub is a hosting/collaboration platform that can store Git repositories and provide pull requests, reviews, Actions, security features, and collaboration capabilities.

---

## Q3. What is a Git repository?

**Answer:**

A Git repository contains Git's metadata and history for a project. The `.git` directory stores objects, references, configuration, and other repository information.

---

## Q4. Working directory vs staging area vs repository?

**Answer:**

- Working directory = current files being edited.
- Staging area = changes selected for the next commit.
- Local repository = committed history.

---

## Q5. What is HEAD?

**Answer:**

`HEAD` points to the currently checked-out commit/reference, normally the current branch.

Example:

```text
HEAD -> main
```

means HEAD is currently associated with `main`.

---

## Q6. What is origin?

**Answer:**

`origin` is the conventional default name given to the remote repository when cloning or when a remote is added.

---

## Q7. What is origin/main?

**Answer:**

`origin/main` is a remote-tracking reference representing the last fetched state of the remote repository's `main` branch.

---

## Q8. What is the difference between fetch and pull?

**Answer:**

`git fetch` downloads remote updates without integrating them into the current branch.

`git pull` fetches and then integrates those changes into the current branch.

---

## Q9. What is the difference between merge and rebase?

**Answer:**

Merge combines histories and preserves the branch structure, often using a merge commit.

Rebase replays commits onto a new base and creates a linear history, but rewrites commit IDs.

---

## Q10. Why should we avoid rebase on shared branches?

**Answer:**

Because rebase rewrites commit history. Other developers may already have the old commit IDs, creating synchronization problems.

---

## Q11. Reset vs revert?

**Answer:**

Reset changes/moves local history and can discard or unstage changes.

Revert creates a new commit that reverses an earlier commit, making it safer for shared branches.

---

## Q12. What is cherry-pick?

**Answer:**

Cherry-pick applies the changes from a specific commit onto the current branch.

---

## Q13. What is stash?

**Answer:**

Stash temporarily stores uncommitted changes so you can switch context without committing unfinished work.

---

## Q14. What is a merge conflict?

**Answer:**

A merge conflict occurs when Git cannot automatically reconcile competing changes, usually because the same area of a file was changed differently.

---

## Q15. How do you resolve a merge conflict?

**Answer:**

1. Run `git status`.
2. Open conflicted files.
3. Resolve conflict markers.
4. Run `git add`.
5. Commit for merge or `git rebase --continue` for rebase.
6. Run tests.
7. Push the result.

---

# 45. Advanced Interview Questions & Answers

## Q1. What happens internally during git pull --rebase?

**Answer:**

Git fetches remote updates, identifies the local commits that are not part of the updated remote base, temporarily removes/repositions those local commits, updates the branch to the remote base, and replays the local commits on top.

---

## Q2. Why does a rebased commit get a new hash?

**Answer:**

A commit hash is based on commit content and its parent/history. When rebase changes the parent commit, the commit identity changes, so Git creates a new commit object/hash.

---

## Q3. What is a fast-forward merge?

Example:

```text
Before:
A---B---C   main
         \
          D---E   feature
```

If main has no new commits, Git can move the main pointer forward:

```text
A---B---C---D---E
```

No merge commit is required.

---

## Q4. What is a non-fast-forward push?

**Answer:**

It happens when the remote branch contains commits that your local branch does not contain, so Git rejects a normal push to prevent losing remote history.

Typical response:

```bash
git fetch origin
git rebase origin/main
```

or merge, depending on the workflow.

---

## Q5. When would you use --force-with-lease?

**Answer:**

After intentionally rewriting your own branch history, commonly after rebase, when a normal push is rejected. `--force-with-lease` provides a safety check that the remote branch has not changed unexpectedly.

Example:

```bash
git push --force-with-lease origin feature/login
```

Avoid force-pushing shared branches unless explicitly controlled.

---

## Q6. What is detached HEAD?

**Answer:**

Detached HEAD means HEAD points directly to a commit instead of a branch.

Example:

```bash
git checkout <commit-id>
```

If you make useful commits there, create a branch:

```bash
git switch -c recovery-branch
```

---

## Q7. What is reflog?

**Answer:**

Reflog records local movements of references such as HEAD and branch pointers. It is extremely useful for recovering commits after reset, rebase, or accidental branch movement.

---

## Q8. How do you find which commit introduced a bug?

**Answer:**

Use `git bisect` when you have a known-good and known-bad point.

```bash
git bisect start
git bisect bad
git bisect good <known-good-commit>
```

Git then helps narrow down the problematic commit.

---

## Q9. How do you remove a sensitive file from Git?

**Answer:**

If it is only a local/unshared commit, reset/amend may be enough.

If it was pushed, first rotate/revoke the credential. Then remove it from the repository and, when necessary, clean it from history using an approved history-rewrite process.

The security fix is more important than simply deleting the visible file.

---

## Q10. What is the difference between `git reset --soft`, `--mixed`, and `--hard`?

**Answer:**

```text
--soft
Moves HEAD; changes remain staged.

--mixed
Moves HEAD; changes remain in working directory but are unstaged.

--hard
Moves HEAD and resets staging + working tree.
```

`--hard` can destroy uncommitted local work.

---

# 46. Most Important Scenario-Based Interview Questions

## Scenario 1: Local and remote branches have diverged. What will you do?

**Answer:**

First I will not immediately push or force-push.

```bash
git status
git fetch origin
git log --oneline --graph --decorate --all -20
```

Then I will determine whether the remote is ahead, local is ahead, or both have unique commits.

For my private feature branch, I may use:

```bash
git rebase origin/main
```

For a shared branch where history must be preserved, I may use:

```bash
git merge origin/main
```

After resolving conflicts and testing, I push the branch.

---

## Scenario 2: Developer says `git pull` created a conflict. What do you do?

**Answer:**

I first run:

```bash
git status
```

I identify conflicted files, resolve them manually, test the application, then stage and complete the merge:

```bash
git add .
git commit
```

If it was a rebase:

```bash
git add .
git rebase --continue
```

---

## Scenario 3: A bad commit is already deployed to production. How do you undo it?

**Answer:**

For a shared production branch, I normally use:

```bash
git revert <commit-id>
git push
```

This creates a new commit that reverses the bad change without rewriting shared history.

---

## Scenario 4: You accidentally committed to the wrong branch.

**Answer:**

If it has not been shared, I can use reset/recommit to move the work to the correct branch.

If it has already been shared, I avoid rewriting shared history. Depending on the situation, I use `cherry-pick` to apply the change to the correct branch and `revert` the wrong-branch change if necessary.

---

## Scenario 5: You are halfway through feature development and production asks for an urgent fix.

**Answer:**

If my current changes are not ready to commit:

```bash
git stash -u
git switch hotfix
```

I complete and push the hotfix, then return:

```bash
git switch feature/my-feature
git stash pop
```

---

## Scenario 6: You rebased a feature branch and normal push is rejected.

**Answer:**

Because rebase rewrote the branch history, the remote has the old commit IDs.

If it is my private feature branch and I have verified nobody else's work will be overwritten:

```bash
git push --force-with-lease origin feature/my-feature
```

I would not blindly use `git push --force`.

---

## Scenario 7: Someone accidentally ran `git reset --hard` and lost commits.

**Answer:**

First:

```bash
git reflog
```

I locate the previous HEAD/commit, verify it, and recover using a suitable reset or new branch.

Example:

```bash
git switch -c recovery <commit-id>
```

---

## Scenario 8: Main branch is protected. How would you deliver a feature?

**Answer:**

I create a feature branch:

```bash
git switch -c feature/login
```

Commit and push:

```bash
git add .
git commit -m "Add login"
git push -u origin feature/login
```

Then I create a Pull Request, get review/approval, allow CI checks to run, and merge through the approved repository workflow.

---

## Scenario 9: You need only one bug fix from another branch.

**Answer:**

I use cherry-pick:

```bash
git switch release
git cherry-pick <commit-id>
```

Then test and push.

---

## Scenario 10: How would you check whether local main is behind remote main?

**Answer:**

```bash
git fetch origin
git status
git log --oneline main..origin/main
```

The last command shows commits that are on remote main but not local main.

---

## Scenario 11: How would you see commits present locally but not on remote?

```bash
git fetch origin
git log --oneline origin/main..main
```

---

## Scenario 12: How would you prove local and remote point to the same commit?

```bash
git fetch origin
git log --oneline --decorate --all -10
```

If:

```text
HEAD -> main
origin/main
```

are both attached to the same commit, both references point to the same commit.

You can also use:

```bash
git rev-parse main
git rev-parse origin/main
```

If the hashes are identical, they point to the same commit.

---

# 47. DevOps Production Interview Scenario

### Interviewer:
"Your Terraform repository is used by multiple DevOps engineers. Your local branch and remote branch have diverged. What will you do?"

### Strong answer:

> "I will first check the working tree using `git status`. I will not directly force-push. I will run `git fetch origin` to update my remote-tracking information and inspect the divergence using `git log --oneline --graph --decorate --all`. If this is my private feature branch, I can rebase it onto the latest `origin/main`, resolve conflicts, run Terraform fmt/validate/plan and tests, and then push using `--force-with-lease` if the rebase changed history. If it is a shared branch, I prefer merge or the team's approved PR workflow rather than rewriting history."

---

# 48. Terraform/DevOps Git Best Practices

For Infrastructure as Code repositories:

```text
main
 |
 +-- feature/network-module
 +-- feature/vm-module
 +-- bugfix/pipeline
```

Recommended workflow:

```bash
git fetch origin
git switch -c feature/network-module
```

Make changes:

```bash
terraform fmt
terraform validate
git status
git diff
```

Commit:

```bash
git add .
git commit -m "Add reusable network module"
```

Before PR:

```bash
git fetch origin
git rebase origin/main
```

Run:

```bash
terraform fmt -check
terraform validate
terraform plan
```

Push:

```bash
git push -u origin feature/network-module
```

Then:

```text
Pull Request
    ↓
Code Review
    ↓
Security scans
    ↓
Terraform plan
    ↓
Approval
    ↓
Terraform apply
```

---

# 49. Golden Rules to Remember

1. Always check `git status` before doing Git operations.
2. Use `git fetch` when you want to inspect remote changes safely.
3. Use `git pull` when you intentionally want to fetch and integrate.
4. Use `merge` when preserving branch history is important.
5. Use `rebase` mainly for your own/private feature branches.
6. Do not casually rebase shared branches.
7. Prefer `git revert` for undoing commits already shared on production branches.
8. Be very careful with `git reset --hard`.
9. Prefer `git push --force-with-lease` over `git push --force` when force-push is genuinely required.
10. Use `git reflog` when you need to recover local history.
11. Resolve conflicts carefully and run tests after resolution.
12. Never treat exposed secrets as safe merely because the file was deleted.
13. Use Pull Requests and branch protection for production repositories.
14. Keep commit messages meaningful.
15. Do not commit secrets, Terraform state, `.env`, credentials, or unnecessary generated files.

---

# 50. Final Git Interview Cheat Sheet

```text
START PROJECT
git init
git clone

CHECK
git status
git branch
git remote -v

CHANGE
git add
git commit
git diff

HISTORY
git log
git log --oneline
git log --oneline --graph --decorate --all -20
git show
git blame

BRANCH
git branch
git switch
git switch -c
git branch -d

REMOTE
git fetch
git pull
git push

INTEGRATION
git merge
git rebase
git cherry-pick

TEMPORARY WORK
git stash
git stash pop

UNDO
git restore
git reset
git revert

RECOVERY
git reflog

RELEASE
git tag

DEBUG
git bisect

CLEANUP
git fetch --prune
git clean
```

## One-minute interview revision

```text
FETCH  → Remote changes download, current branch not changed
PULL   → Fetch + integrate
PUSH   → Local commits → remote
MERGE  → Combine histories, preserve history
REBASE → Replay commits, linear history, rewrites IDs
RESET  → Move/reset local history/state
REVERT → New commit that undoes old commit
STASH  → Temporarily save uncommitted work
CHERRY-PICK → Bring one specific commit
REFLOG → Recover local reference/HEAD history
TAG    → Mark release/version
BISECT → Find bug-introducing commit
```

# End

This guide is designed for practical Git/DevOps work and interview preparation, especially branch management, CI/CD repositories, Terraform/IaC repositories, conflict resolution, and production-safe Git operations.
