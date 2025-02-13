---
tags:
  - linux
  - macos
date: 2025-02-13
---

# Overview
This guide will help you configure your ssh client.
# Prerequisites
- You know [[How to use the terminal]]
- You are on MacOS (or Linux).
- You have `openssh` installed.
- You have read the wikipedia page on [public key cryptography](https://en.wikipedia.org/wiki/Public-key_cryptography).
- You understand the difference between a public and private key.
# Configuring the ssh client

## Generating a keypair
If you don't have a public/private keypair, you will need to generate one.

You can do this using the ssh-keygen tool on the local machine.
```shell
ssh-keygen -t ed25519
```
Follow the instructions and fill out your details. You should password protect the private key.

You should now have a private key at `~/.ssh/id_ed25519` and a corresponding public key at `~/.ssh/id_ed25519.pub`.

> [!WARNING] Do not move the private key.
> The private key is PRIVATE. It should not leave the device and should not be shared.

## Distributing the public key
The public key generated in the step above should be distributed to the hosts to which you want to provide access using the private key. This can safely be done copying the public key data via a public channel.

Once on the hosts you should add this key to the list of `~/.ssh/authorized_keys`. For more information on how configure ssh access to a machine, see: [[How to configure the ssh daemon]].
# Connecting to servers
To connect to a remote machine via ssh you can do:
```shell
ssh user@host
```
Substitute in the user and host you want to connect to. You will be prompted for your ssh key password. You will then be connected over ssh and able to issue commands to the remote shell.

## Saving connection configuration
You can save your connection configuration using the ssh client config file. This is located at `~/.ssh/config`.

This file contains hosts for which you want to save the connection information. This makes it simple to connect with a simple `ssh rpi5b`.

An example for the `~/.ssh/config` file looks like:
```ssh-config
# Raspberry Pi 5 (remote)
Host rpi5b
User liam
HostName rpi5b
IdentityFile ~/.ssh/id_ed25519

# More entries...
```

# Remembering known hosts
On the first connection to a remote machine, you will be shown a fingerprint and asked to confirm the host is who you think it is.

> [!WARNING] Always verify the fingerprint.
> You should always verify the fingerprint before trusting the remote. While the remote knows you have your private key, this is the step where you must manually validate the remote's private key. You must be sure the computer you're connecting to is in-fact the computer you think it is.
> 
> This is an important security step. DO NOT SKIP.
