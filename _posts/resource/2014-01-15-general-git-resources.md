---
layout: post
title: Git 常用资源
category: resource
tags: Git
keywords: Git
---

## Settings

### Version & Help

```bash
git --version # View git version

git status -h # Show help infomation for git status command
git status --help | git help status # Show help infomation on web page

git status # View current status
```

### config

```bash
git config --list # View git config infomation

git config --global user.name "Your Name" # Save the values in the global configuration file, ~/.gitconfig for all repositories
git config --global user.email "youremail@yourdomain.com" # global commit name and email address with the --global option

git config user.name "Your Name" # Save the values in the .git/config file under the root directory of the repository for specific repository
git config user.email "youremail@yourdomain.com" # Use a different username or email address for a specific repository without the --global option

git config --global alias.st status # Create alias
```

### Miscellaneous

```bash
git archive # Create a compressed package to release

ssh-keygen -t rsa -C "address@qq.com" # Create key file

```

### Ignore

Update .gitignore file

```bash
test.txt # Ignore test.txt file in the same level with .gitignore file
upload/ | upload/* # Ignore all files in the upload folder in the same level with .gitignore file
upload/*.txt # Ignore all txt files in the upload folder in the same level with .gitignore file
*.py[cod] # Ignore all .pyc .pyo .pyd files in the same level with .gitignore file
*.txt !a.txt # Ignore all .txt files but a.txt

git add -f test.pyo # Add the ignored file
git check-ignore -v test.pyo # Check whether the file exist in  .gitignore file
```

## Repository

### Create

```bash
mkdir repo # Create a repo directory
cd repo # Enter into the repo directory
git init # Initialize a git repository in the root of the folder
ls -ah # Check the .git directory
```

### Remote

```bash
git remote | git remote -v # Show remote repository
git remote show <remote> | git remote show origin # Show details about remote repository

git remote add <remote> <url> | git remote add origin https://github.com/OWNER/REPOSITORY.git # Set a new remote repository

git pull <remote> <branch> # Fetch the remote and merge to local

git push <remote> --force # Force push
git push <remote> --all # Push all branches to remote

git remote set-url origin https://github.com/OWNER/REPOSITORY.git # Change remote's URL from SSH to HTTPS
git remote set-url origin git@github.com:OWNER/REPOSITORY.git # Change remote's URL from HTTPS to SSH

git remote rename origin destination # Change remote name from 'origin' to 'destination'
git remote rm destination # Remove remote
```

### Clone

```bash
git clone https://github.com/php/php-src.git
git clone --depth=1 https://github.com/php/php-src.git # Only clone the latest commit

git clone --branch <branchname> <remote-repo-url> | git clone -b <branchname> <remote-repo-url> 
# Clone the repository, fetch all branches, and checkout to a specific branch immediately
git clone --branch <branchname> --single-branch <remote-repo-url> | git clone -b <branchname> --single-branch <remote-repo-url>
# Clone the repository and fetch only a single branch
```

## Common & Working

### Add & Commit

```bash
git add *.java | git add -A | git add . | git add path | git add path1 path2 | git add dir # Add the changed file
git add -p [file_name] # Stage the changes separately
git add -f test.pyo # Add the ignored file

git commit file1 file2
git commit -am "commit message" # Commit with add & message option
git commit --amend | git commit --amend file1 file2 # Update the last commit
git commit --amend -m "xxxx" # Update the last commit message
git commit --amend --author='Author Name <email@address.com>' # Update the author

git cherry-pick <commit-id> # Integrate selected, individual commits from any branch into your current HEAD branch
git cherry-pick af02e0b --no-commit # Only add the commit's changes to your Working Copy - without directly committing
git cherry-pick <commit-id> <commit-id> | git cherry-pick <commit-id>..<commit-id> | git cherry-pick <commit-id>^..<commit-id>
```

### View Changes

```bash
git diff --color-words # highlighting changes
git diff file | git diff HEAD file | git diff --cached file # Compare the changes in the working directory, index and local repository
git diff branch1..other-branch | git diff branch1 other-branch # Comparing two branches
git diff main new_branch ./diff_test.txt # Comparing files from two branches
git diff <commitID> <commitID> --stat

git diff --shortstat "@{0 day ago}" # Show how many lines you code today
```

### Rollback

```bash
git checkout -f some_branch # force switch to a different branch or commit and discard any local changes or uncommitted modifications
git checkout -- '*.c' | git checkout . | git checkout SHA -- file # Rollback file in working Directory

git restore '*.c' | git restore . # Restore files to match the version in the index
git restore --source master~2 file | git checkout master~2 file # Revert the file to two revisions back
git restore --staged hello.c | git reset # Restore a file in the index to match the version in HEAD
git restore --source=HEAD --staged --worktree file # Restore both the index and the working tree

git reset -- filename | git reset HEAD <file> | git reset HEAD | git reset # Reset a file in the index to match the version in HEAD
git reset --hard "HEAD^" | git reset --hard HEAD~1 | git reset --hard SHA # Reset current branch to a previous commit, discard any local changes
git reset --soft "HEAD^" | git reset --keep <commit> # Reset current branch to a previous commit, but not change index and workspace
git branch feature & git reset --hard origin/master & git checkout feature # Rollback the commit on the wrong branch  

git revert <commit> (should not use: git push --force) # Swap additions and deletions changes

git update-ref HEAD HEAD^ # updates the current branch head to the new object
```

### Stash

```bash
git stash | git stash save "message..." # Temporary save all modified files to a stack
git stash -u # Save current modified files including untracked files
git stash pop # Restore the most recently stashed changes (top of stack) and remove this stash
git stash pop stash@{2} 
git stash apply | git stash apply stash@{2} # Apply the changes to working copy and keep them in stash
git stash list # Display the temporary stack
git stash drop | git stash drop stash@{0} # Drop the most recently stashed changes
git stash show # View  a summary of a stash
```

## Branch

### Create & Switch
    
```bash
git branch MSF230339 # Create a new branch
git branch [barnch] [commit] # Create a new branch and point to a commit
git branch --track [barnch] [remote-barnch] # Create a new branch and track to the remote branch
git branch --set-upstream [barnch] [remote-barnch] # Set the Upstream between branch and remote branch

git checkout - # Switch to last branch
git checkout MSF230339 | git switch MSF230339 # Switch to another branch
git checkout --track origin/CSIB-78960 # Check out branch first time with track
git checkout -b MSF230339 | git switch -c CSIB-78960 # Create and switch to the new branch

```

### Upstream
    
```bash
git push --set-upstream origin <branch name> # Set the Upstream branch
git branch -u <remote/branch name> # Track a new upstream branch
git branch -vv # List all your branches that are tracking upstream branch
```

### Push & Pull
    
```bash
git push -u origin develop # Push local branch to remote and track it

git pull origin other-branch # Pull another branch
```

### View & Check
    
```bash
git branch # View local branch
git ls-remote --heads origin | git branch -r # View remote branch
git branch -a # View all branches on local and remote

git branch --merged origin # View the branch that merged into remote
git remote show origin # View info about remote branch origin

git show-branch HEAD # View last commit of current branch
git rev-parse HEAD | git rev-parse --short HEAD # View last commit SHA1 of current branch

git branch --format='%(HEAD) %(refname:short) -> %(upstream:short)'
git for-each-ref --format="%(if)%(HEAD)%(then)* %(else) %(end)%(refname:short)"
# List branch with format
```

### Delete

```bash
git branch -d MSF3000000 | git branch -D MSF230339 # Delete local branch
git push origin --delete <branch> | git push origin :<remote_branch> | git branch -dr [remote branch] # Delete remote branch
git remote prune origin # Delete remote branch # Delete local branch references to remote branches that do not exist
```

### Rename

```bash
git branch -m new-name # Rename local branch
git branch -m <old_name> <new_name> # Rename local branch on a different branch
git push origin -u <new_name> & git push origin --delete <old_name> # Rename remote branch
```

### Merge

```bash
git checkout master & git pull # Prepare
git merge --squash bugfix | git merge --no-ff develop | git merge develop | git mergetool # Merge from another barnch
git commit | git commit -m 'Your custom commit message' # Commit the result

git merge --abort
```

### Rebase

```bash
git log --graph --decorate --pretty=oneline --abbrev-commit # Count your commits or view  the SHA from the last commit
git rebase -i HEAD~[NUMBER OF COMMITS] | git rebase -i [SHA]
git push origin branchName --force | git push -f # If previously pushed your code to a remote branch, you will need to force push.

git checkout master # Checkout master branch
git pull | git pull origin master # Pull master branch
git checkout branchName # Checkout bug/feature branch
git rebase -i master # Handle any conflicts. Combine two commands: git rebase -i master branchName
git push origin branchName --force | git push -f # Force push branch to remote.

git checkout master
git merge branchName
git push origin master

git rebase --abort
```

## File

### Create & View

```bash
notepad "ApplicationDB/Stored Procedures/Rpt_KhoaNewStoredProcedure.sp" # Create (View) file
type "ApplicationDB\Stored Procedures\Rpt_CfgAttrSp.sp" # View file
dir "ApplicationDB\Stored Procedures\Rpt_KhoaNewStoredProcedure.sp" # Check whether file exist
```

### Search & Show

```bash
dir /s /b | find "PermissionsAuditor.sql"
git ls-files --recurse-submodule **/<tableName>*.trg
git grep "hello-wold" # Search in repository

git rev-list -n 1 HEAD -- **/CopySiteSp* | git log --diff-filter=D --summary **/CopySiteSp* # View deleted file
git ls-files **/EventMessageDel.trg # View tracked file
git grep -e <regExpPattern> <filePath> | git grep -e AccessAs '*.sql' # View lines matching a pattern
git show <branch_name>:<file_name> # Show a file content in a branch
```

### Remove & Clean

```bash
git rm --f "ApplicationDB\Stored Procedures\Rpt_KhoaNewStoredProcedure.sp" # Remove added file
git rm file1 file2 | git rm Documentation/\*.txt # Remove file
git rm --cached file # Remove file from stage and keep in working area
git clean -xdf # f: untracked file d: untracked dir n: Prompt content x: include content in .gitignore
```

### Rename & Move

```bash
git mv source destination
```

### Ignore & Track

```bash
git status --ignored # Show ignored file
git update-index --assume-unchanged /path/to/file # Ignore the file
git update-index --no-assume-unchanged /path/to/file # Track the ignored file
```

### History & Recover

```bash
gitk -- "C:\Development\csi\SingleVersion\AllInOneDb\DatabaseLevels\00002\5_DataPatch\AppMessages_BaseSyteLine.sql"
git log -p /path/to/file

git show HEAD~4:src/main.c | git show eab4f9e4 "src/main.c" # Check previous content of file

git rm --cached one.txt # Resore added file
git checkout <commitID> <filePath> # Recover file from a certain commit
```

## Log & History

### View

```bash
git status # View current status

1. git reflog # Show all commits that the HEAD pointed to
2. git checkout -b branch-name 70b3696 # Recover a git branch you accidentally deleted according to the last command result

git log | git log -n | git log path | git log --pretty=oneline path # Show the current HEAD and its ancestry
git log --graph --decorate --pretty=oneline --abbrev-commit

# Find the missing branch that is accidentally deleted
1. git fsck --full --no-reflogs --unreachable --lost-found # Create a list of all dangling or unreachable commits. 
2. ls -1 .git/lost-found/commit/ | xargs -n 1 git log -n 1 --pretty=oneline # List of commit messages for all commits in the lost and found
3. git log -p --stat --color 9ae38fc # Find your missing commit through the process of manual inspection
4. git checkout -b branch-name 9ae38fc # Create a new branch with the missing commit as the branch head

git show [commit_id] | git show HEAD | git show master # Show history
git show --name-only [commit] # Show changed files on the commit
git show [commit]:[file] # how file content on the commit
```

### Search

```bash
git log --all --grep='<given-text>' # Search the commit log (across all branches) for the given text
git log --all -i --grep='Build 0051' # Ignoring case in the grep search
git grep 'Build 0051' $(git rev-list --all) # Search the actual content of commits through a repo's history
git log --stat # Show commit file History
git log -S [keyword] # Search log history contains keyword
git log -5 --pretty --oneline # Show last 5 COMMITS

git blame <file> & git show commitID <file> # Check when and who
git whatchanged --since='2 weeks ago' # Show the changes since 2 weeks ago

git log --follow file # Show the file history
git whatchanged file
```

## Tag

### View & Search
    
```bash
git describe --contains <commit> # View the tag that commit is belonged to   
git describe --tags --abbrev=0 # Show the latest tag of current branch
git tag -l 'v1.4.2.*' # Show tags begin with 1.4.2
git show v1.4 # Show tag information

git tag | git tag -l # View all local tags
git ls-remote --tags # Show all remote tags
```

### Create

```bash
git tag v1.3 # Create a tag
git tag v1.0.0 039bf8b | git tag v1.0.0 -m "add tags information" 039bf8b # Create a tag based on specific commit
git tag -a <version-number> -m "v1.0 release(description)" <commit-id> # Create a annotation tag based on specific commit
```

### Pull & Push

```bash
git checkout -b branch_name tag_name # Create branch based on tag

git push origin v1.5 # Push tag
git push origin --tags # Push all tags
```

### Delete

```bash
git tag --delete v1.0.0 | git tag -d v1.0.0 # Delete local tag
git push -d origin v1.0.0 | git push --delete origin v1.0.0 | git push origin :refs/tags/<tag-name> # Delete remote tag
```

## Submodule

### Clone

```bash
git clone --recursive https://github.com/chaconinc/MainProject # Clone repository with submodule
git submodule update --init submoduleName #  Clone submodule
git clone https://github.com/chaconinc/MainProject # Clone repository
git submodule init
git submodule update
(
	UserName: builduser@git.infor.com 
	Password:GitP@ssw0rd
) # Clone submodules
```

### Update & Rollback

```bash
git pull --recurse-submodules | git submodule update # Update submodules
git config --global submodule.recurse true # Auto update sumodules on git pull command

git submodule update --remote -f # Rollback to HEAD version
```

### Remove

```bash
git submodule deinit --all # Remove submodules
```
