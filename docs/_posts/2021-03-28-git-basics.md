---
layout: post
title:  Get started with Git and GitHub
author: David Smatlak
permalink: git-basics
comments: false
---

Many organizations treat documentation as code, so technical writers need to know how to use [Git](https://git-scm.com)
and [GitHub](https://github.com) for version control. This article is a user guide for the basic
workflow to work with a repository. You'll learn how to create, clone, and commit changes to a
repository.

<div class="note">
<b>Note</b> <br>
This article creates a repository for a single-user to commit changes in the main branch. More
advanced topics such as branches, merges, upstream remotes, and pull requests will be covered in
future articles.
</div>

## Prerequisites

Use the default settings for these installations unless your computing environment requires specific
configurations.

- Install [Microsoft Visual Studio Code](https://code.visualstudio.com/docs/setup/windows) (VS
  Code).
- Install [Git](https://git-scm.com/downloads). After the install, use `git config` to set up your
  editor, name, and email address. For more information, see [First time Git setup](https://git-scm.com/book/en/v2/Getting-Started-First-Time-Git-Setup).
- Install [PowerShell](https://docs.microsoft.com/powershell/scripting/install/installing-powershell-core-on-windows).
  PowerShell integrates with VS code and you can run PowerShell commands from a VS Code terminal.
- Create a GitHub account. Go to [GitHub](https://github.com) and select **Sign Up**.

## Create a repository

To begin, you'll create a GitHub repository. This repository is referred to as `origin` later in the
article.

1. Sign in to [GitHub](https://github.com).
1. On the top, right of the screen, select the plus sign (`+`).
1. Select **New Repository**.
1. Enter the repository information:
    - Enter a **Repository name** such as _testrepo_.
    - Add a **Description** such as _My test repository_.
    - Select **Public**.
    - Under **Initialize the repository with** select **Add a README file**.
1. Select **Create repository**.

The repository is created and your browser opens to the repository's **Code** tab. The _README.md_
file is displayed and includes the repository's name and description.

## Clone a repository

A clone is a copy of a repository that you download to your computer. You use a cloned repository to
add, change, or delete files.

1. Launch VS Code and open a terminal session with one of the following methods:
    - From the menu select **Terminal** > **New Terminal**.
    - Use the keyboard shortcut **Ctrl** + **Shift** + **`** (backtick).

1. Use PowerShell commands to create a directory for your repository and switch to that directory.
   For example, _C:\github\clonedemo_. `New-Item` creates the directory and `Set-Location` switches
   to the specified directory.

    ```powershell
    New-Item -Path "C:\github\clonedemo" -ItemType Directory
    Set-Location -Path "C:\github\clonedemo"
    ```

1. From GitHub, go to your repository's **Code** tab and select the **Code** button.
1. Select **HTTPS** and use the clipboard icon to copy the URL.
1. From the VS Code terminal, clone the repository. Replace `<GitHub account>` including the angle
   brackets with your GitHub account.

    ```plaintext
    git clone https://github.com/<GitHub account>/testrepo.git
    ```

    The output displays the progress.

    ```plaintext
    Cloning into 'testrepo'...
    remote: Enumerating objects: 3, done.
    remote: Counting objects: 100% (3/3), done.
    remote: Total 3 (delta 0), reused 0 (delta 0), pack-reused 0
    Receiving objects: 100% (3/3), done.
    ```

1. After the clone is finished, switch to the repository's directory.

    ```powershell
    Set-Location -Path "C:\github\clonedemo\testrepo"
    ```

### Verify the remote

When you clone a repository, the `origin` remote is created with `fetch` and `push`. A remote is a
shortcut name for the repository's GitHub URL. The `fetch` command pulls changes from GitHub to your
computer. The `push` uploads your changes to GitHub. Both of these commands are used to keep your
repository in sync.

<div class="note">
<b>Note</b> <br>
For a single-user repository only <code>origin</code> is necessary. When you work with a shared
repository, you'll use additional remote names.
</div>

```plaintext
git remote -v
```

```plaintext
origin  https://github.com/<GitHub account>/testrepo.git (fetch)
origin  https://github.com/<GitHub account>/testrepo.git (push)
```

For more information about how to manage remotes, see [About remote repositories](https://docs.github.com/github/getting-started-with-github/about-remote-repositories).

## Change a file

Git is version control software that tracks changes to files. To see changes that aren't committed,
use the `git status` command. In this example, you'll add some text to the _README.md_ file and save
the change.

1. In the VS Code terminal, view the cloned repository's status and verify the output shows
   **nothing to commit, working tree clean**.

    ```plaintext
    git status
    ```

    ```plaintext
    On branch main
    Your branch is up to date with 'origin/main'.

    nothing to commit, working tree clean
    ```

1. Run the `code` command to open _README.md_ in VS Code and update the file.

    ```plaintext
    code README.md
    ```

1. Run `git status` and the output shows there are **Changes not staged for commit**.

    ```plaintext
    On branch main
    Your branch is up to date with 'origin/main'.

    Changes not staged for commit:
      (use "git add <file>..." to update what will be committed)
      (use "git restore <file>..." to discard changes in working directory)
            modified:   README.md

    no changes added to commit (use "git add" and/or "git commit -a")
    ```

1. Update Git tracking so that the changes can be committed.

    ```plaintext
    git add README.md
    ```

1. Run `git status` and the output shows **Changes to be committed**.

    ```plaintext
    On branch main
    Your branch is up to date with 'origin/main'.

    Changes to be committed:
      (use "git restore --staged <file>..." to unstage)
            modified:   README.md
    ```

## Commit a change

When a file is changed in the cloned repository, you commit the change and then push the commit to
your GitHub repository. When you commit changes, include a brief message that describes the commit
and is included in the commit history. The `push` syncs your clone with GitHub and the commit
history is the same in both places.

1. Commit changes to an updated file in the cloned repository.

    ```plaintext
    git commit -m "updates README.md"
    ```

    Each commit has a 40-character unique hash and `git` commands reference the initial seven
    characters. The hash in this message is `b897559`.

    ```plaintext
    [main b897559] updates README.md
      1 file changed, 2 insertions(+)
    ```

1. Run `git status`. The cloned repository is one commit ahead of the GitHub repository.

    ```plaintext
    On branch main
    Your branch is ahead of 'origin/main' by 1 commit.
      (use "git push" to publish your local commits)

    nothing to commit, working tree clean
    ```

1. Run the `push` command. The `origin` shortcut references the cloned repository's remote URL.

    ```plaintext
    git push origin main
    ```

    The output shows the status as the changes are uploaded to GitHub.

    ```plaintext
    Enumerating objects: 5, done.
    Counting objects: 100% (5/5), done.
    Delta compression using up to 8 threads
    Compressing objects: 100% (2/2), done.
    Writing objects: 100% (3/3), 328 bytes | 328.00 KiB/s, done.
    Total 3 (delta 0), reused 0 (delta 0), pack-reused 0
    To https://github.com/<GitHub account>/testrepo.git
       d3e4f6a..b897559  main -> main
    ```

After a successful `push` your cloned repository and GitHub repository are in sync. Your GitHub
repository contains the updated _README.md_ file.

<div class="tip">
<b>Tip</b> <br>
An optional step is to confirm the commit history is identical between your local clone and GitHub.
In a VS Code terminal, run <code>git log</code> to display the commits. On GitHub, go to your
repository's <b>Code</b> tab and select <b>Commits</b>.
</div>

## Conclusion

In this article you set up a GitHub repository, cloned the repository, and committed a change to
GitHub. This is the basic workflow to work with a repository using Git and GitHub.
