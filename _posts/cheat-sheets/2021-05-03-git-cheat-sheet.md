---
layout: post
title: "Git Cheat Sheet"
author: alfred
categories: [git, cheat sheet]
featured: false
hidden: false
toc: true
---

Git is a free and open source distributed version control system designed to handle everything from small to very large projects.

Git is easy to learn and has a tiny footprint with fast performance. It outclasses other SCM tools with features like cheap local branching, convenient staging areas, and multiple workflows.

<!--more-->

### References

[GitHub Git Cheat Sheet](https://education.github.com/git-cheat-sheet-education.pdf)

[How to work with Git submodules](https://git-scm.com/book/en/v2/Git-Tools-Submodules)

### Submodules

```sh
# Add new submodule
git submodule add <repo> <path>

# Update submodules in new pulled repo
git submodule update --init --recursive

# Update submodule to new master version of submodule
git submodule update --remote [<path>]

# Replace submodule with branch version of submodule
git rm <path>
git submodule add --force --branch <branch> <repo> <path>
git submodule update --init --recursive <path>
```

### Clone on Existing Folder

```sh
# Create an empty repo on the folder
git init .

# Add an origin remote for the repository
git remote add origin <repo url>
```

### Branching and Tagging

```sh
# Create a new branch keeping local changes (no commit yet)
git checkout -b <new-branch>

# Create a local branch and push to remote
git checkout -b <new-branch>
git push -u origin <new-branch>

# Create a local tag and push to remote
git tag <new-tag>
git push origin <new-tag>
```

### Rebase

```sh
# Get most recent version of master branch
git checkout master
git pull

# Switch to feature branch
git checkout <feature-branch>

# Rebase
git rebase master

# Continue rebase after resolving merge conflicts
git rebase --Continue

# If feature branch was already pushed before, force push to update remote feature branch
git push -f origin <feature-branch>
```

### Set Execute Permission on Bash Files

```sh
# Show current mode bits (-> 100644)
git ls-files --stage

# Set execute permission
git update-index --chmod=+x *.sh

# Show changed mode bits (-> 100755)
git ls-files --stage
```

### Git Large File Storage (LFS)

```sh
# https://git-lfs.github.com/

# Set up Git LFS and its respective hooks
git lfs install

# Select the file types you'd like Git LFS to manage (or directly edit your .gitattributes). 
# You can configure additional file extensions at anytime.
git lfs track "*.psd"

# Make sure .gitattributes is tracked
git add .gitattributes

# There is no step three. Just commit and push to GitHub as you normally would.
git add file.psd
git commit -m "Add design file"
git push origin master
```
