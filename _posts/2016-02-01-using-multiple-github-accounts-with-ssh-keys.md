---
title:    Using multiple GitHub accounts with SSH keys
layout:   post
category: tutorial-tips
tags:     [skill]
---

## Problem
I have a GitHub account, it is *oanhnn*, but at office, I could not use my personal GitHub account. I have to use an other GitHub account, eg *superman*.
I want to use both two accounts on same computer (without typing password every time, when doing git push or pull).

<!--more-->

## Solution
Using ssh keys and ssh config file to create an alias host and use multiple GitHub accounts

## How to?
1. [Make key pair for each accounts](https://help.github.com/articles/generating-a-new-ssh-key/) and [add it to GitHub accounts](https://help.github.com/articles/adding-a-new-ssh-key-to-your-github-account/).
2. Make ssh configure file (`~/.ssh/config`) like:

   ```conf
   # Default github account: oanhnn
   Host github.com
      HostName github.com
      IdentityFile ~/.ssh/oanhnn_private_key
      IdentitiesOnly yes
      
   # Other github account: superman
   Host github-superman
      HostName github.com
      IdentityFile ~/.ssh/superman_private_key
      IdentitiesOnly yes
   ```
   
3. [Added ssh key to your agent](https://help.github.com/articles/adding-a-new-ssh-key-to-the-ssh-agent/) by command:

   ```shell
   $ ssh-add ~/.ssh/oanhnn_private_key
   $ ssh-add ~/.ssh/superman_private_key
   ```

4. Check that repo recognizes keys.

   ```shell
   $ ssh -T git@github.com
   $ ssh -T git@github-superman
   ```

5. Clone projects

   ```shell
   $ git clone git@github-superman:org2/project2.git /path/to/project2
   $ cd /path/to/project2
   $ git config user.email "superman@org2.com"
   $ git config user.name  "Super Man"
   ```
   

Done! Have goodluck!
