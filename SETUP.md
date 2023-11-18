# Overview

This file contains setup instructions on how to get started porting to z/OS.

# Setting up SSH

To connect to the z/OS system, we require your public SSH key. If you are unsure how to generate and retrieve your public SSH key, please follow the steps outlined below:

## Windows keygen instructions

Open your terminal or command prompt.

Type the command `ssh-keygen -t rsa` and press Enter. This will generate a new SSH key pair at `/c/Users/<username/.ssh` by default.

If you chose the default location, your public key will be available at `/c/Users/<username>/.ssh/id_rsa.pub`. Provide the contents of this file to the admin.

## Linux/macOS keygen instructions

Open your terminal or command prompt.

Type the command `ssh-keygen -t rsa` and press Enter. This will generate a new SSH key pair at `~/.ssh` by default.

If you chose the default location, your public key will be available at `~/.ssh/id_rsa.pub`. Provide the contents of this file to the admin.

## Setting up a config file

To connect to z/OS without typing out your username and the IP, you can set up a config file. To do this you can create a config file in your .ssh directory (`~/.ssh/config` on linux/macOS). Once you have created the file, add the following to the file.

````
Host <Alias of your choosing>
   HostName 128.xxx.xxx.xxx
   User <Admin provided username here>
   IdentityFile ~/.ssh/id_rsa
````

## Connecting to z/OS

Once the admin has added your public key, you should be able to connect to z/OS using `ssh username@128.xxx.xxx.xxx`. 

If you set up a config file, you can connect using `ssh <alias you assigned>`.

If you get a permission error, it is possible you need to alter the permissions of your private key so only you can view/edit it. You can do this by running `chmod 600 ~/.ssh/id_rsa`.

If ssh cannot find your private key, you can specify the file path to the private key in the command using: `ssh username@128.xxx.xxx.xxx -i /path/to/private/key/id_rsa`.

# Getting Started on z/OS 