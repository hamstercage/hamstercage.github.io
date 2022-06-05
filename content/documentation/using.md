---
title: "Using Hamstercage"
type: page
weight: 4
---
After installation, Hamstercage is available on the path as `hamstercage`.

In most cases, you will want to run Hamstercage as root, for example by using [`sudo`](https://www.sudo.ws). This is necessary to access files your user doesn't have access to, but only `root`.

## Creating a new Hamstercage repo

Hamstercage needs a manifest file in order to track files managed through it. You can create this file manually, or you can have Hamstercage create a template file for you:

```shell
hamstercage init
```

This creates `hamstercage.yaml` in the current directory.

## Adding one or more files to the repository

To add one more files, directories, or symbolic links to the repo, use the `add` command. You will need to specify which tag these files should be added to.

For example, to add `/etc/profile` to the `all` tag, use:
```shell
sudo hamstercage -t all /etc/profile
```

## Applying contents from the repository to the target

When files in the repository are updated, for example, because you have made changes to a file in a tag used by multiple hosts, use the [`apply` command](../command-reference#applying-files-apply).

```shell
sudo hamstercage apply
```
