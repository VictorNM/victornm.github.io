---
title: "Edit old commits"
date: 2025-09-20T08:18:44+07:00
draft: false
author: VictorNM
---

When coding, I usually commit in small steps to keep the history clear. But sometimes, after finishing a whole branch and preparing to open a pull request, I realize there is a small error or need to add code to a previous commit. If I keep adding that change to the latest commit, the history will be fragmented: a feature/bugfix that should be encapsulated in one commit is now scattered across many commits.

My favorite solution: **`git rebase -i` to edit old commits and consolidate changes back to their rightful place**.
<!--more-->

## Step-by-Step Guide

### 1. Identify the commit you want to edit
Suppose you want to edit a commit that is among the `n` most recent commits. Run the command:

```bash
git rebase -i HEAD~n
```

Replace `n` with the number of the commit you want to review (e.g., `HEAD~5` to open the 5 most recent commits).

### 2. Select the commit to edit

The editor opens and lists the commits. By default, each commit is marked with `pick`.

- Change `pick` to `edit` on the commit you want to edit (on `vim`, press `i` to enter insert mode, then make the change).
- Save and exit the editor (on `vim`, press `Esc`, then type `:wq` and press `Enter`).

### 3. Add changes to the old commit

Git will stop at that commit so you can edit the code.

- Edit the file as desired
- Stage changes (for example: `git add .`)
- Then amend the commit: `git commit --amend --no-edit`

The `--no-edit` flag helps keep the old commit message intact, or you can use `-m "new message"` to change it.

### 4. Continue rebase

After editing the commit, run: `git rebase --continue`

Git will reapply the commits after the one you just edited. If there are no conflicts, you're done, but if there are, you resolve and continue with git rebase --continue.

## Conclusion

With this workflow, you can:

- Keep your commit history neat and clear.

- Group related changes into the right commit.

- Make it easier for reviewers (and yourself in the future) to read the code.

Next time you need to edit an old commit, try `git rebase -i`. It takes a little effort at first, but it will make your Git history much “cleaner”.