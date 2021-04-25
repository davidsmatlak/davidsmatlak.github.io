---
layout: post
title:  "Git basics: How to branch and merge"
author: David Smatlak
permalink: git-basics-branch-merge
comments: false
---

This article describes how to use a working branch and then merge the changes to your default branch. A working branch is used to make changes and commits without any affect on your default branch. When you're happy with the changes in the working branch, merge the commits to your default branch.

Visual Studio Code (VS Code) is used as the Markdown editor and the terminal for Git commands. If you need to set up your environment or create a repository, see [Git basics: Get started with Git and GitHub](git-basics).

<div class="note">
<b>Note</b> <br>
This article is written as a beginners guide for Git and GitHub. The repository is set up for one person to learn the command syntax. Some steps verify that a command worked as intended so that you can see the results and understand the concepts.
</div>

## Branch overview

Repositories have several types of branches: default, remote, and working. The default branch is a repository's single-source of truth. A working branch might also be referred to as a _feature branch_.

| Branch | Description |
| ---- | ---- |
| **Default** | The repository's branch named `main`. You can commit changes directly to `main` but a best practice is to merge commits from a working branch. <br><br> Use `git branch` to display the local clone's list of branches. The branch tagged with an asterisk (`*`) is the active branch. |
| **Remote** | Each branch has a remote branch in GitHub. For example, in your local clone, `origin/main` is the remote branch for `main`. <br><br> Use `git branch -r` to display your local clone's remote branches. |
| **Working** | Create a working branch from the `main` branch. The commit history is identical and the most recent commit is the base of the two branches. As you commit changes in the working branch, it diverges and its commit history is ahead of the `main` branch. Use `git log` to display the commit history. <br><br> Changes in the working branch don't affect the default branch. To publish changes in your working branch, merge the working branch commits into the `main` branch. |

## Sync the repository

Make sure that your GitHub repository and your local clone are in sync. Changes can be committed on
GitHub, so it's a best practice to make sure you're working with the most current files. This step
becomes more important when you work with shared repositories where multiple people update a repository.

1. Launch VS Code.
1. Open your local clone's directory: **File** > **Open Folder**.
1. Open a VS Code terminal: **Terminal** > **New Terminal**.
1. Checkout the `main` branch.

    ```plaintext
    git checkout main
    ```

    The output shows that you're on the `main` branch.

    ```plaintext
    Already on 'main'
    Your branch is up to date with 'origin/main'.
    ```

1. Sync the `main` branch of your GitHub repository and local clone.

    ```plaintext
    git fetch origin
    git merge origin/main
    ```

    You use `git fetch` to pull any changes from GitHub to your local clone. The `git merge` command merges changes from the GitHub remote branch `origin/main` into your `main` branch. If the GitHub repository and local clone are in sync, **Already up to date** is displayed.

## Create a working branch

Create a working branch in your local clone and push it to GitHub. You'll use this branch to update files and commit changes.

1. Create a working branch from the `main` branch.

    ```plaintext
    git checkout -b my-new-branch
    ```

    The `git checkout` command combines two tasks:

    - `git branch <working branch>`
    - `git checkout <branch name>`.

    The output shows that `git checkout -b` created a new branch named `my-new-branch` and then switched to that branch.

    ```plaintext
    Switched to a new branch 'my-new-branch'
    ```

1. Verify the new branch is active.

    ```plaintext
    git branch
    ```

    The output lists the branches and the asterisk (`*`) confirms `my-new-branch` is active. Any changes to the repository's files are now tracked in `my-new-branch`.

    ```plaintext
      main
    * my-new-branch
    ```

1. Push your working branch to GitHub.

    ```plaintext
    git push origin my-new-branch
    ```

    The output shows that your working branch was pushed to your GitHub account's repository.

    ```plaintext
    Total 0 (delta 0), reused 0 (delta 0), pack-reused 0
    remote:
    remote: Create a pull request for 'my-new-branch' on GitHub by visiting:
    remote:      https://github.com/<GitHub account>/testrepo/pull/new/my-new-branch
    remote:
    To https://github.com/<GitHub account>/testrepo.git
     * [new branch]      my-new-branch -> my-new-branch
    ```

## Edit a file

Use the working branch to add, change, or delete files. In this example, you'll update the _README.md_ file and push the commit to GitHub.

1. In VS Code, open _README.md_.
1. Add a sentence to the file and save the change.
1. From the VS Code terminal, check the branch's status.

    ```plaintext
    git status
    ```

    The output shows that in `my-new-branch` the _README.md_ file was changed with the status **Changes not staged for commit**.

    ```plaintext
    On branch my-new-branch
    Changes not staged for commit:
      (use "git add <file>..." to update what will be committed)
      (use "git restore <file>..." to discard changes in working directory)
            modified:   README.md

    no changes added to commit (use "git add" and/or "git commit -a")
    ```

1. Stage the updated file for commit and show the status.

    ```plaintext
    git add .
    git status
    ```

    The `git add` command stages the modified file so it can be committed. The period (`.`) indicates that any modified file will be staged. An alternative is to specify the file with `git add README.md`.

    The `git status` output shows **Changes to be committed**.

    ```plaintext
    On branch my-new-branch
    Changes to be committed:
      (use "git restore --staged <file>..." to unstage)
            modified:   README.md
    ```

1. Commit the change to the file and include a commit message.

    ```plaintext
    git commit -m "updates README.md"
    ```

    The `git commit -m` command commits the change with the commit message within double-quotes. This message is displayed on GitHub and in the `git log` history.

    The output shows the successful commit and the commit's unique hash: `9d377a0`.

    ```plaintext
    [my-new-branch 9d377a0] updates README.md
     1 file changed, 1 insertion(+), 1 deletion(-)
    ```

1. Verify the commit was successful.

    ```plaintext
    git status
    ````

    The output confirms there are no other changes that need to be added or committed.

    ```plaintext
    On branch my-new-branch
    nothing to commit, working tree clean
    ```

1. Push the changes to `my-new-branch` on GitHub.

    ```plaintext
    git push origin my-new-branch
    ```

    The output shows the status as the commit is uploaded to GitHub.

    ```plaintext
    Enumerating objects: 5, done.
    Counting objects: 100% (5/5), done.
    Delta compression using up to 8 threads
    Compressing objects: 100% (2/2), done.
    Writing objects: 100% (3/3), 346 bytes | 346.00 KiB/s, done.
    Total 3 (delta 0), reused 0 (delta 0), pack-reused 0
    To https://github.com/<GitHub account>/testrepo.git
       b897559..9d377a0  my-new-branch -> my-new-branch
    ```

## Compare files

Compare files between your branches on GitHub. This comparison shows the differences between your `main` branch and `my-new-branch`.

1. Sign in to [GitHub](https://github.com/login).
1. Go to your GitHub repository named `testrepo` and select the **Code** tab.
1. Select the link that shows **2 branches**. The number will vary if you've created other branches.
1. Open `main` and `my-new-branch` in separate browser tabs.
1. View the _README.md_ file in each branch.

The _README.md_ files are different because you changed the file in `my-new-branch` and that change doesn't affect the `main` branch.

## Merge changes

When the committed changes in your working branch are ready for publication, merge the commits to your `main` branch. When you push the commit to GitHub, your changes are live in the `main` branch.

1. In a VS Code terminal, checkout the `main` branch.

    ```plaintext
    git checkout main
    ```

    ```plaintext
    Switched to branch 'main'
    Your branch is up to date with 'origin/main'.
    ```

1. Merge the commits from `my-new-branch` into the `main` branch.

    ```plaintext
    git merge my-new-branch
    ```

    The output shows the changes.

    ```plaintext
    Updating b897559..9d377a0
    Fast-forward
     README.md | 2 +-
     1 file changed, 1 insertion(+), 1 deletion(-)
    ```

1. Verify the status of the `main` branch.

    ```plaintext
    git status
    ````

    The output shows your local repository is one commit ahead of the remote.

    ```plaintext
    On branch main
    Your branch is ahead of 'origin/main' by 1 commit.
      (use "git push" to publish your local commits)

    nothing to commit, working tree clean
    ```

1. Push the changes to the `main` branch on GitHub.

    ```plaintext
    git push origin main
    ```

    The output shows the status.

    ```plaintext
    Total 0 (delta 0), reused 0 (delta 0), pack-reused 0
    To https://github.com/<GitHub account>/testrepo.git
       b897559..9d377a0  main -> main
    ```

1. [Compare](#compare-files) the _README.md_ files again and verify the files in `main` and `my-new-branch` contain the same content.

## Conclusion

In this article you created a working branch, updated a file, and merged the commit into your default branch. When you combine these skills with the skills learned in [Git basics: Get started with Git and GitHub](git-basics), you can maintain your repository. To learn more, see the documentation for [Git](https://git-scm.com/docs) and [GitHub](https://docs.github.com/github).
