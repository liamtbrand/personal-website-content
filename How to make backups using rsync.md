---
tags:
  - linux
  - macos
date: 2025-02-13
---

# Overview
This is a short guide on how to use `rsync` to make backups of a directory.

The source and target directories may be on different machines. Backups over the network will use ssh as the transport mechanism. This guide will show how to make the backup over the network. This is ideal for backing up remote machines or service data on those machines.

Only differences will be transferred. Each backup uses the contents of the previous backup as a reference for what has changed. Backup directories are linked such that they can be copied stand-alone. Storage is efficient using hardlinks.

By the end of this guide you will have a functional backup script you can use to make a backup of a source directory to a target location.
# Prerequisites
- You know [[How to use the terminal]]
- You know [[How to use the ssh client]]
- You have `rsync` installed.
- The destination file system supports hard links.
	- ext4
	- btrfs
	- apfs
# Creating the backup script
To begin, go to the location where you want to store the backup and create a new directory. 

Name it something like `~/backups/machine` where `machine` is the hostname of the machine you're backing up.

Alternatively, you may want to name it `~/backups/project` where `project` is the name of the project folder you are backing up. However, I would advise you to make backups of entire machines as backing up individual projects is tedious.

If you're making the backup onto an external drive, change the working directory to the location on the external drive. For example: `/Volumes/rsync-backups/rpi5b`.

Next, create a new script that will control the backups. Below is a template that you can use.

```shell
#!/bin/sh
# This script will make a backup of the source directory.

# Switch the working directory to the directory containing this script.
cd "$(dirname "$0")"

# Capture a timestamp for this backup.
date=`date "+%Y-%m-%dT%H:%M:%S"`

USER=liam              # The user on the remote machine
HOST=rpi5b             # The hostname of the remote machine
SOURCE_PATH=/home/liam # The path to the source directory on the remote machine

# Use rsync to transfer the data.
rsync -azP \
	--link-dest=$(dirname "$0")/latest \
	--log-file=backup-$date.log \
	--exclude-from=rsync_ignore \
	--rsync-path="sudo rsync" \
	--dry-run \
	$USER@$HOST:$SOURCE_PATH backup-$date

# Symlink the latest backup as latest
rm -f latest
ln -s backup-$date latest
```

- Replace `USER=liam` with the user on the remote (source) machine.
- Replace `HOST=rpi5b` with the hostname of the remote (source) machine.
- Replace `SOURCE_PATH=/home/liam` with the path to make a backup of.

For information about the commands used, see `man rsync`.

The `--dry-run` flag has been set to avoid making unwanted changes. Once you're satisfied the script works as expected, you can remove the `--dry-run` flag.

The `rsync_ignore` file will work in the same way a `.gitignore` file works. List the paths you want `rsync` to ignore in this file. See `man rsync` and search for `--exclude-from` for more information on how the ignore file works.

Save the commands above as a backup script `make-backup.sh` so that you can reproduce the exact same setup next time you go to backup the directory. I recommend including a `README.md` in the same directory where the script is to describe the purpose of the backup and maybe some other relevant notes you may have.

> [!WARNING] ACLs and Extended Attributes need a special flag.
> ACLs, Extended Attributes and Resource Forks on MacOS need a special flag to be copied correctly. A similar situation applies under Linux.
> TODO:
> - [ ] Figure out which flag needs to be set to copy these correctly.
> 
> Note: Probably don't need all the attributes if the files you're backing up are data files.

# Run the backup script
First make sure you can execute the script:
```shell
chmod +x make-backup.sh
```
Then run it:
```shell
./make-backup.sh
```

You should now have an initial backup in your target location. Running the script again should transfer only the difference and create a new backup directory containing the full backup.
# Resources
- https://rsync.samba.org