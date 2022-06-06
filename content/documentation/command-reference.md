---
title: "Command Reference"
type: page
weight: 5
---

Hamstercage commands take a number of options and parameters.

```
usage: hamstercage [-h] [-d DIRECTORY] [-f FILE] [-n HOSTNAME] [-r REPO] [-t TAG] [-v]
                   {add,apply,diff,init,list,ls,remove,del,rm,save,tag} ...

Manage the hamster cage.

positional arguments:
  {add,apply,diff,init,list,ls,remove,del,rm,save,tag}
                        sub-command help
    add                 add one or more files to the manifest
    apply               apply files from repo to target
    diff                print differences between target and repo
    init                create a new manifest
    list (ls)           list manifest entries
    remove (del, rm)    remove one or more files from the manifest
    save                save target files to repo
    tag                 manage tags in the manifest

optional arguments:
  -h, --help            show this help message and exit
  -d DIRECTORY, --directory DIRECTORY
                        base directory of target files
  -f FILE, --file FILE  manifest file to use
  -n HOSTNAME, --hostname HOSTNAME
                        name of this host
  -r REPO, --repo REPO  directory of file repo
  -t TAG, --tag TAG     tags to apply/save
  -v, --verbose         verbose output
```

## General Options

### Target Directory `-d` / `--directory`

The directory where the managed files live, default `/`. You can set this to a different path when restoring config from a backup, or when managing a jail or container from the outside.

### Hostname `-n` / `--hostname`

The hostname of the local host. By default the value of `hostname(1)`. When applying files to the target directory, the hostname is used to obtain the default list of tags to use. Specifying this can be useful when restoring a host from backup, or when working on a container or jail.

### Manifest File `-f` / `--file`

The name of the manifest file that tracks all managed files, as well as settings for the repository. Default is `./hamstercage.yaml`.

### Repository Base Directory `-r` / `--rep`

All managed files are stored under this directory. The default is the same directory the manifest file lives in.

### Tags `-t` / `--tag`

When adding files or applying them to the target directory, one or more tags can be specified. See the `add` and `apply` commands.

### Verbose output `-v` / `--verbose`

By default, Hamstercage will only print warnings or error. With this flag, progress will be reported on all operations.

## Commands

### Adding Files `add`

```shell
hamstercage -t mytag add file...
```

Add one or more files to the repository. You need to specify exactly one tag, which determines the tag the file will be added to. The file will be added to the manifest, and the contents of the file copied to the repository.

### Applying Files `apply`

```shell
hamstercage apply [file...]
```

Apply files from the repository to the target. Without any further options and parameters, will apply all files from the default tags for this hostname. Specify one or more tags to limit files to those in those tags, or specify filenames that should be applied.

Adding a file that is already in the manifest for the given tag is an error.

### Determining Differences `diff`

```shell
hamstercage diff [file...]
```
Prints out a unified diff between all files in the repository and in the target. This allows you to preview what changed `apply` or `save` will make.

### Initialize a New Repository `init`

```shell
hamstercage init
```

Creates a new manifest file, by default `./hamstercage.yaml`. Combine this with `git init` to create a new Hamstercage repository.

### List Entries `list`

```shell
hamstercage list [-l]
```
Lists all entries in the manifest. When `-l` is given, will print more details on each entry.

### Remove Files From Repository `remove`

```shell
hamstercage -t tag remove file...
```

Removes one or more files from the specified tag. Does not change files in the target.

### Save Target to Repository `save`

```shell
hamstercage save [file...]
```

Updates the manifest entries and repository files from the target.

### Adding Tags `tag add`

Adds a new tag to the manifest.

```shell
hamstercage tag add [-d description] [file...]
```
