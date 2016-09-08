---
title:    How to chmod all directories except files recursively
layout:   post
category: tutorial-tips
tags:     [linux, dev-ops]
feature:  /assets/img/fail.jpg
---

Did you chmod all directories and files recursively by command

```
chmod -R 777 /path/to/base/dir
```

<!--more-->

To recursively give directories read & execute privileges:

```
find /path/to/base/dir -type d -exec chmod 755 {} +
```

To recursively give files read privileges:

```
find /path/to/base/dir -type f -exec chmod 644 {} +
```

Or, if there are many objects to process:

```
chmod 755 $(find /path/to/base/dir -type d)
chmod 644 $(find /path/to/base/dir -type f)
```

Or, to reduce chmod spawning:

```
find /path/to/base/dir -type d -print0 | xargs -0 chmod 755
find /path/to/base/dir -type f -print0 | xargs -0 chmod 644
```

*Source from [superuser.com](http://superuser.com/questions/91935/how-to-chmod-all-directories-except-files-recursively)*

### Recursive chmod script for dirs and/or files

```
#!/bin/sh
#
# chmodr.sh
#
# author: Francis Byrne
# date: 2011/02/12
#
# Generic Script for recursively setting permissions for directories and files
# to defined or default permissions using chmod.
#
# Takes a path to recurse through and options for specifying directory and/or
# file permissions.
# Outputs a list of affected directories and files.
#
# If no options are specified, it recursively resets all directory and file
# permissions to the default for most OSs (dirs: 755, files: 644).

# Usage message
usage()
{
  echo "Usage: $0 PATH -d DIRPERMS -f FILEPERMS"
  echo "Arguments:"
  echo "PATH: path to the root directory you wish to modify permissions for"
  echo "Options:"
  echo " -d DIRPERMS, directory permissions"
  echo " -f FILEPERMS, file permissions"
  exit 1
}

# Check if user entered arguments
if [ $# -lt 1 ] ; then
 usage
fi

# Get options
while getopts d:f: opt
do
  case "$opt" in
    d) DIRPERMS="$OPTARG";;
    f) FILEPERMS="$OPTARG";;
    \?) usage;;
  esac
done

# Shift option index so that $1 now refers to the first argument
shift $(($OPTIND - 1))

# Default directory and file permissions, if not set on command line
if [ -z "$DIRPERMS" ] && [ -z "$FILEPERMS" ] ; then
  DIRPERMS=755
  FILEPERMS=644
fi

# Set the root path to be the argument entered by the user
ROOT=$1

# Check if the root path is a valid directory
if [ ! -d $ROOT ] ; then
 echo "$ROOT does not exist or isn't a directory!" ; exit 1
fi

# Recursively set directory/file permissions based on the permission variables
if [ -n "$DIRPERMS" ] ; then
  find $ROOT -type d -print0 | xargs -0 chmod -v $DIRPERMS
fi

if [ -n "$FILEPERMS" ] ; then
  find $ROOT -type f -print0 | xargs -0 chmod -v $FILEPERMS
fi
```

*Source from [Gist](https://gist.github.com/francisbyrne/3731497)*
