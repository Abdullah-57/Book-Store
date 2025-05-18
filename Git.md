# Comprehensive Git Commands Cheat Sheet for Software Construction and Development

## 1. Introduction to Git
- **What is Git?**: A distributed version control system for tracking code changes, enabling collaboration, and managing project history.
- **Analogy**: Git is like a time capsule for your code, letting you save snapshots (commits), rewind to past versions, and work on parallel paths (branches) with teammates.

## 2. Git Basics
- **Initialize a Git Repository**:
  - `git init`
  - **Use**: Creates a new Git repository in the current directory.
  - **Example**: Start a project at 07:34 PM PKT on May 17, 2025:
    ```
    mkdir my_project
    cd my_project
    git init
    ```
    - Output: `Initialized empty Git repository in /home/user/my_project/.git/`

- **Clone an Existing Repository**:
  - `git clone <repository-url>`
  - **Use**: Downloads a remote repository to your local machine.
  - **Example**: Clone a GitHub repo:
    ```
    git clone https://github.com/user/project.git
    cd project
    ```
    - Creates a local copy.

- **Check Git Status (View Changes)**:
  - `git status`
  - **Use**: Displays the state of your working directory (staged, unstaged, untracked files).
  - **Example**: After adding a file:
    ```
    echo "Hello" > readme.md
    git status
    ```
    - Output: `Untracked files: readme.md`

## 3. Branching and Working with Branches
- **Create a New Branch and Switch to It**:
  - `git checkout -b <branch-name>`
  - **Use**: Creates and switches to a new branch for feature development.
  - **Example**: Create a feature branch:
    ```
    git checkout -b feature-login
    ```
    - Output: `Switched to a new branch 'feature-login'`

- **Switch to an Existing Branch**:
  - `git checkout <branch-name>`
  - **Use**: Switches to an existing branch.
  - **Example**: Switch back to main:
    ```
    git checkout main
    ```
    - Output: `Switched to branch 'main'`

- **List All Branches (Local)**:
  - `git branch`
  - **Use**: Shows all local branches, with an asterisk (*) on the current one.
  - **Example**:
    ```
    git branch
    ```
    - Output: `* main  feature-login`

- **List All Branches (Local + Remote)**:
  - `git branch -a`
  - **Use**: Lists all local and remote-tracking branches.
  - **Example**:
    ```
    git branch -a
    ```
    - Output: `* main  feature-login  remotes/origin/main`

- **Rename Current Branch**:
  - `git branch -m <new-branch-name>`
  - **Use**: Renames the current branch.
  - **Example**: Rename `feature-login` to `login-feature`:
    ```
    git branch -m login-feature
    ```
    - Output: Branch renamed to `login-feature`.

- **Delete a Local Branch**:
  - `git branch -d <branch-name>`
  - **Use**: Deletes a local branch (must be merged first).
  - **Example**: Delete `login-feature`:
    ```
    git branch -d login-feature
    ```
    - Output: `Deleted branch login-feature`

- **Create a Branch on the Remote**:
  - `git push -u origin <branch-name>`
  - **Use**: Pushes a new local branch to the remote and sets upstream.
  - **Example**: Push `feature-login`:
    ```
    git checkout -b feature-login
    git push -u origin feature-login
    ```
    - Output: Branch `feature-login` created on remote.

- **Delete a Remote Branch**:
  - `git push origin --delete <branch-name>`
  - **Use**: Removes a branch from the remote repository.
  - **Example**: Delete `feature-login` remotely:
    ```
    git push origin --delete feature-login
    ```
    - Output: Branch `feature-login` deleted from remote.

## 4. Committing Changes
- **Stage Changes for Commit**:
  - `git add <file-name>`
  - **Use**: Stages a specific file for the next commit.
  - **Example**: Stage `readme.md`:
    ```
    git add readme.md
    git status
    ```
    - Output: `Changes to be committed: readme.md`

- **Stage All Changes for Commit**:
  - `git add .`
  - **Use**: Stages all modified and new files.
  - **Example**: Stage all changes:
    ```
    echo "Update" > update.md
    git add .
    ```
    - Output: All files staged.

- **Commit Staged Changes**:
  - `git commit -m "Commit message"`
  - **Use**: Commits staged changes with a short message.
  - **Example**: Commit changes:
    ```
    git commit -m "Add readme and update files"
    ```
    - Output: `[main abc1234] Add readme and update files`

- **Commit with a Detailed Message**:
  - `git commit -m "Title" -m "Detailed description"`
  - **Use**: Adds a title and detailed description to the commit.
  - **Example**:
    ```
    git commit -m "Add Login Feature" -m "Implemented login page with basic authentication at 07:34 PM PKT on May 17, 2025"
    ```
    - Output: Commit with title and body.

- **Amend the Last Commit**:
  - `git commit --amend`
  - **Use**: Updates the last commit (e.g., add files or change message).
  - **Example**: Add a forgotten file and amend:
    ```
    git add forgot.md
    git commit --amend --no-edit
    ```
    - Output: Updates commit without changing the message.

## 5. Viewing and Navigating History
- **View Commit History**:
  - `git log`
  - **Use**: Displays the full commit history.
  - **Example**:
    ```
    git log
    ```
    - Output: Detailed log with commit hashes, authors, and dates.

- **View Commit History in a More Compact Format**:
  - `git log --oneline`
  - **Use**: Shows a concise one-line history.
  - **Example**:
    ```
    git log --oneline
    ```
    - Output: `abc1234 Add login feature`

- **View Specific Commit Details**:
  - `git show <commit-hash>`
  - **Use**: Displays details of a specific commit.
  - **Example**: View commit `abc1234`:
    ```
    git show abc1234
    ```
    - Output: Commit details and changes.

- **View Differences Between Commits**:
  - `git diff`
  - **Use**: Shows changes between working directory and last commit.
  - **Example**: After editing `readme.md`:
    ```
    echo "New line" >> readme.md
    git diff
    ```
    - Output: Shows `+New line`.

- **Git Log Customization**:
  - `git log --oneline --graph --decorate`
  - **Use**: Displays a graphical view of branch history with decorations.
  - **Example**:
    ```
    git log --oneline --graph --decorate
    ```
    - Output: Graph showing branch merges and tags.
  - **Show Log Between Two Commits**:
    - `git log <commit-hash1>..<commit-hash2>`
    - **Example**: `git log abc1234..def5678`
      - Output: Commits between `abc1234` and `def5678`.

## 6. Working with Remote Repositories
- **Add a Remote Repository**:
  - `git remote add origin <repository-url>`
  - **Use**: Links a local repository to a remote one.
  - **Example**: Add a GitHub remote:
    ```
    git remote add origin https://github.com/user/my_project.git
    ```

- **Check Remote Repositories**:
  - `git remote -v`
  - **Use**: Lists all remote repositories with URLs.
  - **Example**:
    ```
    git remote -v
    ```
    - Output: `origin https://github.com/user/my_project.git (fetch)`

- **Fetch Latest Changes from the Remote**:
  - `git fetch origin`
  - **Use**: Downloads remote changes without merging.
  - **Example**:
    ```
    git fetch origin
    ```
    - Updates remote-tracking branches.

- **Pull Changes from the Remote**:
  - `git pull origin <branch-name>`
  - **Use**: Fetches and merges remote changes into the current branch.
  - **Example**: Pull from `main`:
    ```
    git pull origin main
    ```
    - Merges remote `main` changes.

- **Push Local Commits to the Remote Repository**:
  - `git push origin <branch-name>`
  - **Use**: Uploads local commits to the remote branch.
  - **Example**: Push to `main`:
    ```
    git push origin main
    ```

- **Push Changes with Force (Use Cautiously, Especially After Rebase)**:
  - `git push --force`
  - **Use**: Forces push to overwrite remote history (e.g., after rebasing).
  - **Example**: Force push after rebase:
    ```
    git push --force
    ```
    - Warning: Use only if you understand the impact.

- **Set Upstream Branch and Push**:
  - `git push -u origin <branch-name>`
  - **Use**: Sets upstream and pushes the branch.
  - **Example**: Set upstream for `feature-login`:
    ```
    git push -u origin feature-login
    ```

- **Push to the Same Branch (After Setting Upstream)**:
  - `git push`
  - **Use**: Pushes to the upstream branch.
  - **Example**: After `git push -u`, just `git push`.

- **Pull the Latest Changes from the Upstream Branch**:
  - `git pull`
  - **Use**: Pulls from the upstream branch (after setting with `-u`).
  - **Example**: `git pull` (Fetches and merges from `origin`).

## 7. Git Rebase and Merge
- **Rebase Your Branch onto Another Branch**:
  - `git rebase <branch-name>`
  - **Use**: Reapplies commits onto another branch’s history for a linear timeline.
  - **Example**: Rebase `feature-login` onto `main`:
    ```
    git checkout feature-login
    git rebase main
    ```
    - Resolve conflicts if needed, then `git rebase --continue`.

- **Rebase Interactively (To Modify Commit History)**:
  - `git rebase -i <commit-hash>`
  - **Use**: Allows editing, squashing, or reordering commits.
  - **Example**: Rebase the last 2 commits:
    ```
    git rebase -i HEAD~2
    ```
    - Edit file to `squash` or `edit`, save, and exit.

- **Merge a Branch into the Current Branch**:
  - `git merge <branch-name>`
  - **Use**: Combines branch changes into the current branch.
  - **Example**: Merge `feature-login` into `main`:
    ```
    git checkout main
    git merge feature-login
    ```
    - Output: `Fast-forward` or conflict resolution if needed.

- **Abort a Rebase**:
  - `git rebase --abort`
  - **Use**: Cancels a rebase and restores the original state.
  - **Example**: Abort a failed rebase:
    ```
    git rebase --abort
    ```

- **Continue a Rebase After Resolving Conflicts**:
  - `git rebase --continue`
  - **Use**: Proceeds after fixing conflicts.
  - **Example**: After resolving:
    ```
    git add conflicting_file
    git rebase --continue
    ```

## 8. Git Reset
- **Undo the Last Commit (Keep Changes Staged)**:
  - `git reset --soft HEAD~1`
  - **Use**: Undoes the commit but keeps changes staged.
  - **Example**:
    ```
    git reset --soft HEAD~1
    ```
    - Changes are ready to recommit.

- **Undo the Last Commit (Keep Changes Unstaged)**:
  - `git reset --mixed HEAD~1`
  - **Use**: Undoes the commit and unstages changes.
  - **Example**:
    ```
    git reset --mixed HEAD~1
    ```
    - Changes remain in the working directory.

- **Completely Undo the Last Commit (Including Changes)**:
  - `git reset --hard HEAD~1`
  - **Use**: Discards the commit and all changes.
  - **Example**:
    ```
    git reset --hard HEAD~1
    ```
    - Reverts to the state before the commit.

## 9. Git Checkout and Checkout -b
- **Switch to a Branch**:
  - `git checkout <branch-name>`
  - **Use**: Switches to an existing branch.
  - **Example**: Switch to `main`:
    ```
    git checkout main
    ```

- **Create a New Branch and Switch to It**:
  - `git checkout -b <branch-name>`
  - **Use**: Creates and switches to a new branch.
  - **Example**: Create `bug-fix`:
    ```
    git checkout -b bug-fix
    ```

## 10. Git Stash (Temporarily Save Changes)
- **Save Changes to the Stash**:
  - `git stash`
  - **Use**: Temporarily saves uncommitted changes.
  - **Example**: Stash work-in-progress:
    ```
    echo "WIP" >> readme.md
    git stash
    ```
    - Output: `Saved working directory and index state WIP on main`

- **Apply the Most Recent Stash**:
  - `git stash apply`
  - **Use**: Reapplies the latest stash.
  - **Example**:
    ```
    git stash apply
    ```
    - Restores stashed changes.

- **Apply a Specific Stash**:
  - `git stash apply stash@{<index>}`
  - **Use**: Applies a specific stash by index.
  - **Example**: Apply `stash@{1}`:
    ```
    git stash apply stash@{1}
    ```

- **Pop the Most Recent Stash**:
  - `git stash pop`
  - **Use**: Applies and removes the latest stash.
  - **Example**:
    ```
    git stash pop
    ```
    - Applies and deletes `stash@{0}`.

- **List All Stashes**:
  - `git stash list`
  - **Use**: Displays all stashed changes.
  - **Example**:
    ```
    git stash list
    ```
    - Output: `stash@{0}: WIP on main: abc1234 Add readme`

## 11. Git Revert
- **Revert a Commit**:
  - `git revert <commit-hash>`
  - **Use**: Creates a new commit that undoes a previous commit.
  - **Example**: Revert commit `abc1234`:
    ```
    git revert abc1234
    ```
    - Output: New commit undoing `abc1234`.

## 12. Git Clean (Remove Untracked Files)
- **Remove Untracked Files**:
  - `git clean -f`
  - **Use**: Deletes untracked files from the working directory.
  - **Example**: Clean untracked files:
    ```
    git clean -f
    ```
    - Removes files not in Git.

- **Remove Untracked Directories**:
  - `git clean -fd`
  - **Use**: Deletes untracked files and directories.
  - **Example**:
    ```
    git clean -fd
    ```
    - Removes all untracked content.

## 13. Git Tagging
- **Create a Tag**:
  - `git tag <tag-name>`
  - **Use**: Marks a specific commit (e.g., for releases).
  - **Example**: Tag a release:
    ```
    git tag v1.0.0
    ```
  - **Annotated Tag**: `git tag -a v1.0.0 -m "Release 1.0.0"`

- **List Tags**:
  - `git tag`
  - **Use**: Shows all tags.
  - **Example**:
    ```
    git tag
    ```
    - Output: `v1.0.0`

- **Push Tags to the Remote**:
  - `git push origin <tag-name>`
  - **Use**: Uploads tags to the remote repository.
  - **Example**: Push `v1.0.0`:
    ```
    git push origin v1.0.0
    ```

- **Delete a Tag Locally**:
  - `git tag -d <tag-name>`
  - **Use**: Removes a local tag.
  - **Example**: Delete `v1.0.0`:
    ```
    git tag -d v1.0.0
    ```
    - Output: `Deleted tag 'v1.0.0'`

## 14. Common Git Aliases
- **Show Commit Log in a Single Line**:
  - `git config --global alias.lg "log --oneline --graph --decorate"`
  - **Use**: Sets a custom alias for a concise log view.
  - **Example**: Use the alias:
    ```
    git lg
    ```
    - Output: Graph with one-line commits.

## 15. Useful Git Configurations
- **Set Your Username and Email**:
  - `git config --global user.name "Your Name"`
  - `git config --global user.email "your-email@example.com"`
  - **Use**: Configures identity for commits.
  - **Example**:
    ```
    git config --global user.name "Alice Smith"
    git config --global user.email "alice.smith@example.com"
    ```

- **Set Default Text Editor**:
  - `git config --global core.editor "nano"`
  - **Use**: Sets the editor for commit messages.
  - **Example**:
    ```
    git config --global core.editor "nano"
    ```
    - Uses `nano` for editing messages.

## 16. Example Scenarios
1. **Starting a Project**:
   - Initialize: `git init`
   - Add file: `echo "Initial content" > app.py && git add app.py`
   - Commit: `git commit -m "Initial commit at 07:34 PM PKT on May 17, 2025"`
   - Add remote: `git remote add origin https://github.com/user/app.git`
   - Push: `git push origin main`

2. **Collaborating with a Team**:
   - Clone: `git clone https://github.com/user/team_project.git`
   - Create branch: `git checkout -b feature-signup`
   - Make changes: `echo "Signup page" > signup.html && git add signup.html`
   - Commit: `git commit -m "Add signup page"`
   - Push branch: `git push -u origin feature-signup`
   - Pull updates: `git checkout main && git pull origin main`

3. **Fixing Mistakes**:
   - Discard changes: `echo "Mistake" >> app.py && git restore app.py`
   - Stash WIP: `echo "WIP" >> app.py && git stash`
   - Undo commit: `git reset --soft HEAD~1`

4. **Releasing a Version**:
   - Tag release: `git tag -a v1.0.0 -m "Release 1.0.0"`
   - Push tag: `git push origin v1.0.0`

5. **Rebasing a Feature**:
   - Create branch: `git checkout -b feature-login`
   - Add commit: `echo "Login page" > login.html && git add login.html && git commit -m "Add login page"`
   - Update `main`: Switch to `main`, add a commit (e.g., `echo "Update" > update.txt && git commit -m "Update main"`).
   - Rebase: `git checkout feature-login && git rebase main`
   - Resolve if needed, then push: `git push --force`

6. **Cleaning Up**:
   - Remove untracked files: `touch temp.txt && git clean -f`
   - Output: Removes `temp.txt` if untracked.

## 17. Tips for Exam Preparation
- **Objective Questions**: Memorize commands (e.g., `git add`, `git commit`, `git rebase`).
- **Subjective Questions**: Explain workflows (e.g., “How to rebase a branch?” → Use Scenario 5).
- **Practice**: Set up a repo, create branches, commit, rebase, and clean untracked files to master Git.

## 18. Summary
- **Git Basics**: `git init`, `git clone`, `git status`
- **Branching**: `git checkout -b <branch-name>`, `git branch`, `git push -u origin <branch-name>`
- **Committing**: `git add .`, `git commit -m "message"`
- **Working with Remote**: `git push`, `git pull`, `git fetch`
- **Rebase and Merge**: `git rebase <branch-name>`, `git merge <branch-name>`
- **Reset**: `git reset --soft HEAD~1`, `git reset --hard HEAD~1`
- **Stashing**: `git stash`, `git stash pop`
- **Reverting**: `git revert <commit-hash>`
- **Log Customization**: `git log --oneline --graph --decorate`
- **Cleaning**: `git clean -f`
- **Tagging**: `git tag <tag-name>`
- **Aliases**: `git config --global alias.lg "log --oneline --graph --decorate"`