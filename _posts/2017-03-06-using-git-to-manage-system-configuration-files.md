---
layout: post
title: Using Git to Manage System Configuration Files on Linux/MacOS
description: This post shows how to use git to manage system configuration files (also known as dot files) on a Linux or MacOS machine.
---

In this post, I will show you how to use git to manage your system configuration files (also known as dot files) on a Linux or MacOS machine. Most of you may have the experience of recovering a previous configurations of your heavily customized system. You might be tired of doing that manually (copy and paste them in the right locations) from time to time. It's gonna be much easier to manage it by using git. Another advantage of using git is that you can roll back easily and have multiple versions (e.g., one for MacOS and one for Ubuntu) on different branches. There are several ways for implementation in terms of whether the git working directory is separated or not and the way we pull configurations from remote repositories. In this post, we only talk about how to set up a separated git repository from the working tree (home directory) for better management.

## Creating a Separate Git Repository

First of all, let's create a folder under home directory.

```shell
cd ~
mkdir configs
```

Then, we initialize the git repository within the folder we just created.

```shell
cd ~/configs
git init
```

If we do `git status` right now without any further configurations, we will get something similar as follows.

```shell
On branch master

Initial commit

nothing to commit (create/copy files and use "git add" to track)
```

It shows that nothing to commit. That's because the git regards the current folder (~/configs) as working directory by default. The working tree can be set specifically by `core.worktree` git configuration option. Execute the following command under `~/config`.

```shell
git config core.worktree "../../"
```

`../../` is the relative directory with respect to the `.git` directory within `~/config`. By setting that option, we make the git to keep the repository within '~/config', but track the file under `~/` (two levels above).

Let's check the status again by `git status`.

```shell
On branch master

Initial commit

Untracked files:
  (use "git add <file>..." to include in what will be committed)

    # following untracked files will be similar

    ../.bash_history
    ../.bash_logout
    ../.bash_profile
    ../.bashrc
    ../.emacs
    ../.vimrc

nothing added to commit but untracked files present (use "git add" to track)
```

Things are getting familiar right? Now, you can add your configuration files. For example:

```shell
    git add ../.bashrc
    git add ../.bash_profile
    git add ../.emacs
    git add ../.vimrc
    # etc.
```

We can then commit the changes we made above.

```shell
    git commit -m "Initial commit"
```

In order to make the git working properly, we have to create a file name `.git` in `~/` with the following content.

```
gitdir: /home/USERNAME/configs/.git
```

Don't forget to replace the USERNAME with yours.

## Setting up the Remote Git Server
It is always good to store your configurations on the cloud (e.g., GitHub (be careful with your private data!) and GitLab). It's very easy to set up a remote git server.

- Open GitHub and login
- Create a new repository
- Follow the instruction of `…or push an existing repository from the command line`

```shell
git remote add origin https://github.com/YOURACCOUNT/YOURREPOSITORY.git
git push -u origin master
```

## Resetting your configurations on a new machine

When you are setting up a new system, you can pull your configurations from the remote git repository. Since we are separating the working directory from the git repository, we have to specify where is the working directory before checkout the content in the repository. Instead of doing a normal `git clone`, do it with an option as follows:

```
git clone --no-checkout https://github.com/YOURACCOUNT/YOURREPOSITORY.git
```

After cloning the repository locally, we have to let the git know where is the working directory.

```
cd ~/configs
git config core.worktree "../../"
```

Now, we are ready to checkout the content. Basically, what we want here is to put the configuration files into right positions and overwrite if they are already existing.

```shell
git reset --hard origin/master
```

## Pulling back changes on the remote repository

Once you set up your repository on multiple machines, you may want to update all of them by pulling the latest commit on the remote repository.

### method 1

Create a file name `.git` in `~/` with the content: `gitdir: /home/USERNAME/configs/.git`.

```shell
cd ~
echo "gitdir: /home/USERNAME/configs/.git" > .git
```

### method 2

Set the GIT_DIR to .git temporally.

```shell
cd ~/configs
export GIT_DIR=".git"
```

Now, we are ready to pull.

```shell
cd ~/configs
git pull
```

## Ignoring files by `.gitignore` or `.git/info/exclude`

If you are interested in what's the difference between `.gitignore` and `.git/info/exclude`, please look into this [stack overflow link](http://stackoverflow.com/questions/23097368/git-ignore-vs-exclude-vs-assume-unchanged).

It's usually annoying to see a lot of untracked files when you check the status. So just use either way mentioned above to ignore files that you don't want git to track.

Thanks for reading!
