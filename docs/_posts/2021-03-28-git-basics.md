---
layout: post
title:  "Git basics: Get started with Git and GitHub"
author: David Smatlak
permalink: git-basics
comments: false
---

Many organizations treat documentation as code, so technical writers need to know how to use [Git](https://git-scm.com/docs)
and [GitHub](https://docs.github.com/github) for version control. This article is a user guide for
the basic workflow to work with a repository. You'll learn how to create, clone, and commit changes
to a repository.

<div class="note">
<b>Note</b> <br>
This article is written as a beginners guide for Git and GitHub. The repository is set up for one
person to learn the command syntax. Some steps verify that a command worked as intended so that you
can see the results and understand the concepts.
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

To begin, you'll create a GitHub repository.

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

    ```powershell
    New-Item -Path "C:\github\clonedemo" -ItemType Directory
    Set-Location -Path "C:\github\clonedemo"
    ```

    `New-Item` creates the directory _C:\github\clonedemo_ and `Set-Location` switches to the
    directory.

1. From GitHub, go to your repository's **Code** tab and select the **Code** button.
1. Select **HTTPS** and use the clipboard icon to copy the URL.
1. From the VS Code terminal, clone the repository. Replace `<GitHub account>` including the angle
   brackets with your GitHub account.

    ```plaintext
    git clone https://github.com/<GitHub account>/testrepo.git
    ```

    The output shows the progress.

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
shortcut name for the repository's GitHub URL. The `fetch` downloads repository changes from GitHub
to your computer and `push` uploads changes from your cloned repository to GitHub. The commands are
used to keep your repository in sync.

To view the cloned repository's remote name and URL, use the `git remote` command.

```plaintext
git remote -v
```

The output displays your repository's remote name and URL.

```plaintext
origin  https://github.com/<GitHub account>/testrepo.git (fetch)
origin  https://github.com/<GitHub account>/testrepo.git (push)
```

<div class="note">
<b>Note</b> <br>
For a single-user repository only <code>origin</code> is necessary. When you work with a shared
repository, you'll use additional remote names. For more information about how to manage remotes,
see <a
href="https://docs.github.com/github/getting-started-with-github/about-remote-repositories">About
remote repositories</a>.
</div>

## Change a file

Git is version control software that tracks changes to files. To see changes that aren't committed,
use the `git status` command. In this example, you'll add some text to the _README.md_ file and save
the change.

1. In the VS Code terminal, display the cloned repository's status.

    ```plaintext
    git status
    ```

    The output shows **nothing to commit, working tree clean**.

    ```plaintext
    On branch main
    Your branch is up to date with 'origin/main'.

    nothing to commit, working tree clean
    ```

1. To open _README.md_ in VS Code, use the `code` command. Then update and save the file.

    ```plaintext
    code README.md
    ```

1. Run `git status` and verify the output shows there are **Changes not staged for commit**.

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

After a file is changed in your cloned repository, you commit the change and then push the commit to
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

1. To upload the commit to GitHub run the `push` command.

    ```plaintext
    git push origin main
    ```

    The output shows the status as the commit is uploaded.

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
GitHub. To learn more about how to manage your repository, continue to [Git basics: How to branch and merge](git-basics-branch-merge).
