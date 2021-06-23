---
layout: post
title: "Git Cheat Sheet"
author: alfred
categories: [git, cheat sheet]
featured: false
hidden: false
---

[GitHub Git Cheat Sheet](https://education.github.com/git-cheat-sheet-education.pdf)

### Submodules

```sh
# How to work with Git submodules
# https://git-scm.com/book/en/v2/Git-Tools-Submodules

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
