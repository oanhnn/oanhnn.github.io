## Problem
I have a GitHub account, it is *oanhnn*, but at office, I couldn't use my personal GitHub account. I have to use an other GitHub account, eg *supermen*.
I want use both two accounts on same computer (use without typing password each times, when git push or pull).

<!--more-->

## Solution
Using ssh keys and ssh config file to create an alias host and use multiple GitHub accounts

## How to?
1. [Make key pair for each accounts](https://help.github.com/articles/generating-a-new-ssh-key/) and [add it to GitHub accounts](https://help.github.com/articles/adding-a-new-ssh-key-to-your-github-account/).
2. Make ssh configure file (`~/.ssh/config`) like:

   ```
   # Default github account: oanhnn
   Host github.com
      HostName github.com
      IdentityFile ~/.ssh/oanhnn_private_key
      IdentitiesOnly yes
      
   # Other github account: supermen
   Host github-supermen
      HostName github.com
      IdentityFile ~/.ssh/supermen_private_key
      IdentitiesOnly yes
   ```
   
3. [Added ssh key to your agent](https://help.github.com/articles/adding-a-new-ssh-key-to-the-ssh-agent/) by command:

   ```
   $ ssh-add ~/.ssh/oanhnn_private_key
   $ ssh-add ~/.ssh/supermen_private_key
   ```

4. Check that repo recognizes keys.

   ```
   $ ssh -T git@github.com
   $ ssh -T git@github-supermen
   ```

5. Clone projects

   ```
   $ git clone git@github-supermen:org2/project2.git /path/to/project2
   $ cd /path/to/project2
   $ git config user.email "supermen@org2.com"
   $ git config user.name  "Super Men"
   ```
   

Done! Have goodluck!
