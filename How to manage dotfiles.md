---
tags:
  - linux
  - macos
date: 2025-02-13
---

# Overview
This is a short guide on how to set up an alias for managing your dot files using `git`.

By the end of this guide, you'll have a functional alias `config` which you can invoke from anywhere on your system to inspect the state of your dotfiles.
# Prerequisites
- You're on a MacOS or Linux system.
- You know [[How to use the terminal]].
- You have `git` installed.
# Philosophy
Text based configuration is the standard in MacOS / Linux. Many applications create hidden "dot" files like `.zshrc` which contain configuration settings.

Changing configuration settings can bork your setup. We want to avoid this borking.

For this reason, we should place configuration files under version control. We can outsource all the heavy lifting to `git` to avoid installing additional programs.
# Setup
## Create a git repo for dotfiles
The first step is to create a bare git repository for storing your dotfiles. You can do this anywhere on your system and it can be named whatever you like. For the purposes of this guide, we'll name it `dotfiles` and place it in the user's home directory.

```shell
git init --bare ~/dotfiles.git
```

## Configure the `config` alias
To install the config alias, go to your `.zshrc` file and add the following line:
```shell
alias config='/usr/bin/git --git-dir=$HOME/dotfiles.git --work-tree=$HOME'
```
On Linux you may need to edit `.bashrc` instead.

To apply changes to the current shell, run: `source ~/.zshrc`

Finally, ensure untracked files are not shown when running `config`.
```shell
config config --local status.showUntrackedFiles no
```

> [!INFO] You can use any alias you like.
> You may want to call your alias `dotfiles` if you think `config` is confusing. I like the `config` alias but it's up to you how you want to do it.
# Adding configuration
Now that you have a dotfiles repo and a config alias, you can interact with it using `config` as you would `git`. `config status` for example would show the current status. Note: No files are present. You'll need to manually add each file you want to be tracked in your dotfiles.

For example, to add the new zsh configuration to our tracked dotfiles:
```shell
config add ~/.zshrc
```

To inspect we've added the changes we expect:
```shell
config diff --staged
```

To commit the changes:
```shell
config commit -m "Install the config alias for using git to track dotfiles."
```

# List tracked configuration
You can view a list of configuration files you are tracking using:
```shell
config ls-files
```
# Common configuration locations

```shell
# Add SSH client configuration
config add ~/.ssh/config
config add ~/.ssh/known_hosts
# Add zsh configuration
config add ~/.zshrc
# Add tmux configuration
config add ~/.tmux.conf
# Add git configuration
config add ~/.gitconfig
```

# Conclusion
You now have an alias `config` in your shell that you can use to leverage `git` version control to keep track of changes to your dotfiles and roll back from breaking changes.

Always remember to add configuration to your dotfiles manually by running `config add`. You should check configuration is added before making changes because if you forget to track the files you change you will not be able to undo the changes.