Git feature-branch workflow — commands, realtime explanation, and examples

Goal: create a feature in a repo, push it, open a PR, update the branch with main, resolve conflicts if any, and merge cleanly.

Prerequisites

git installed (git --version).

Access to the remote repo (SSH key or HTTPS credentials).

A local clone or permission to git clone it.

Quick checks
git --version
git config --global user.name "Your Name"
git config --global user.email "you@example.com"


Explanation: confirms git is installed and sets your identity for commits.

1) Clone the repo (if you don’t already have it)
git clone git@github.com:org/repo.git
# or using HTTPS:
git clone https://github.com/org/repo.git
cd repo


Realtime explanation / expected output:
Cloning into 'repo'... then files appear. If you get SSH auth errors, you’ll see Permission denied (publickey). For HTTPS you may be prompted for username/password or a token.

2) Check the current state and create a feature branch
git fetch origin                # get the latest refs from remote
git checkout -b feature/awesome origin/main
# alternative modern:
# git switch -c feature/awesome --track origin/main


Explanation:

git fetch updates remote refs without modifying working tree.

git checkout -b feature/awesome origin/main creates a new branch starting from origin/main and switches to it. You’ll see Switched to a new branch 'feature/awesome'.

Realtime check:

git status
# On branch feature/awesome
# nothing to commit, working tree clean

3) Make changes and inspect them

Example: add a file

echo "Some feature code" > src/feature.txt
git status


Expected git status:

On branch feature/awesome
Untracked files:
  (use "git add <file>..." to include in what will be committed)
        src/feature.txt


Explanation: git status tells you what's changed in the working tree vs. index vs. HEAD.

4) Stage and commit
git add src/feature.txt
git status --short
git commit -m "feat(feature): add initial feature file"


git status --short example:

A  src/feature.txt


Explanation:

git add stages the file (puts it in the index).

git commit -m records a snapshot; choose clear commit messages (e.g., feat:, fix:, chore:).

Commit output example:

[feature/awesome 1234abc] feat(feature): add initial feature file
 1 file changed, 1 insertion(+)
 create mode 100644 src/feature.txt

5) Push branch to remote and set upstream
git push -u origin feature/awesome


Explanation:

-u sets origin/feature/awesome as the upstream so future git push / git pull work without specifying the remote/branch.
Push output example: To github.com:org/repo.git ... Branch 'feature/awesome' set up to track 'origin/feature/awesome'.

6) Open a Pull Request

Two ways:

Web UI: go to the repo on GitHub/GitLab and create PR from feature/awesome -> main.

CLI (GitHub CLI):

gh pr create --fill


Explanation: gh pr create opens an interactive PR creation or can auto-fill title/body with --fill. If you don’t have gh, use the web UI.

7) Keep your branch up to date with the base branch (main)

Two common approaches: merge or rebase. I’ll show both.

A) Merge approach (safer, no history rewrite)
git fetch origin
git checkout feature/awesome
git merge origin/main


If no conflicts: git will merge and create a merge commit.
If conflicts: git will pause and mark conflicted files.

B) Rebase approach (clean linear history)
git fetch origin
git checkout feature/awesome
git rebase origin/main


Explanation: rebase rewrites your branch commits on top of the latest origin/main. If conflicts happen, git stops at the conflicting commit.

8) Resolving conflicts (step-by-step)

When conflict occurs, git shows something like:

CONFLICT (content): Merge conflict in src/feature.txt


Open the file; you'll see conflict markers:

<<<<<<< HEAD
code from main
=======
your code in branch
>>>>>>> commit-hash


Resolve manually (edit the file to the desired content), then:

git add src/feature.txt
# if you were merging:
git commit   # completes the merge (if merge stopped)
# if you were rebasing:
git rebase --continue
# if you want to abort:
git rebase --abort
git merge --abort


Explanation: git add marks the conflict as resolved. git rebase --continue continues the rebase. Use --abort to roll back to pre-rebase state.

9) Update remote after resolving

If you rebased locally, you must force-push (careful):

# if you rebased:
git push --force-with-lease origin feature/awesome

# if you merged:
git push origin feature/awesome


Explanation: --force-with-lease is safer than --force — it refuses to overwrite remote if it changed since you last fetched.

10) Squash commits (optional: before merging)

Interactive rebase to squash the last N commits:

git rebase -i HEAD~3   # choose pick/squash for the commits
# follow the editor prompts, combine messages as desired


Explanation: Use interactive rebase to clean up history (e.g., compress many WIP commits into 1).

11) Merge the PR (finalize)

If you use GitHub/GitLab web UI, click Merge. If you prefer local merge:

git checkout main
git pull origin main            # ensure main is up-to-date
git merge --no-ff feature/awesome -m "Merge: feature/awesome"
git push origin main


Explanation: --no-ff forces a merge commit so history keeps the branch context. If you prefer a fast-forward merge (no merge commit), omit --no-ff.

12) Clean up branches (local + remote)
# delete remote branch
git push origin --delete feature/awesome

# delete local branch
git branch -d feature/awesome   # use -D to force-delete if not merged

Handy "undo" commands (use carefully)

Unstage a file:

git restore --staged file.txt
# or older:
git reset HEAD file.txt


Discard changes in working tree (danger — this loses work):

git restore file.txt
# or reset entire tree to last commit:
git reset --hard HEAD


Undo last commit but keep changes staged:

git reset --soft HEAD~1


Revert a public commit (safe when commit is pushed):

git revert <commit-hash>


Explanation: revert creates a new commit that undoes the changes; reset --hard rewrites history and discards data — use with caution.

Useful inspection commands
git status
git diff              # unstaged changes
git diff --staged     # changes staged for commit
git log --oneline --graph --decorate --all
git show <commit-hash>
git blame src/file.js


Explanation: these help you understand current state and history.

Full minimal example (copy-paste)
# clone
git clone git@github.com:org/repo.git && cd repo

# make feature branch from main
git fetch origin
git checkout -b feature/awesome origin/main

# add file, commit, push
echo "Hello feature" > hello.txt
git add hello.txt
git commit -m "feat: add hello.txt"
git push -u origin feature/awesome

# keep in sync if main updates
git fetch origin
git rebase origin/main
# resolve if conflicts, then:
git push --force-with-lease origin feature/awesome

# when ready: merge via PR or locally:
git checkout main
git pull origin main
git merge --no-ff feature/awesome
git push origin main

# cleanup
git push origin --delete feature/awesome
git branch -d feature/awesome

Quick cheat-sheet (most-used commands)

git status — current state

git add <file> — stage changes

git commit -m "msg" — commit staged changes

git push / git pull — sync with remote

git checkout -b <branch> / git switch -c <branch> — create+switch

git merge <branch> — merge another branch

git rebase <branch> — replay your commits on top of branch

git log --oneline --graph --decorate — view history

git restore, git reset, git revert — undo tools
