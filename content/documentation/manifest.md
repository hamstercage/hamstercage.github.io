---
title: "Manifest"
type: page
weight: 3
---
All information about file entries is stored in the manifest file `hamstercage.yaml`. This [YAML](https://yaml.org) file consists of these top-level entries:
* `hosts`: each entry describes one host, in particular the set of tags that apply to it
* `tags`: each entry contains a list of files managed for this tag

This is a very minimal example with one host, one tag and one file:
```yaml
hosts:
  testing.example.com:
    description: ''
    tags:
    - all
tags:
  all:
    description: files that apply to all hosts
    entries:
      /etc/profile:
        group: root
        mode: 0o444
        owner: root
        type: file
```


## Hosts

Each entry in this dict defines one host. Hosts are distinguished by their fully-qualified hostname. When running Hamstercage, you can override the hostname by specifying the `-h` option. Each host definition can have these fields:
* `description`: allows you to add a description or note to this entry. Optional.
* `tags`: a list of one or more tags to apply to this host.

## Tags

Each entry in this dict defines one tag. Tag names can be freely chosen; it is recommended to stick to alpha-numeric identifiers though, to keep the names compatible with shell scripts, etc. Each entry has these fields:
* `description`: allows you to add a description or note to this entry. Optional.
* `entries`: a dict of files managed through this tag. See below for details.
* `hooks`: a dict of hook scripts that will be run. See below for details.

## Tags: Entries

The entries field in a tag entry describes files, directories, and symbolic links that are managed through that tag. Each entry can have these fields:
* `group`: the group name owning this file.
* `mode`: the access mode for this file. You need to specify the mode as an octal number (`0o644`) or as a string in quotes (`"644"`). If you simply give a number, it will be interpreted as a base-10 integer, leading to unexpected mode bits
* `owner`: the user name owning this file.
* `target`: the path the link points to, only applicable to `type`=`link`
* `type`: must be one of `dir`, `file`, or `link`.

Note that Hamstercage only manages symbolic links (soft links). Hard links are not supported.

## Tags: Hooks

The `hooks` dict in a tag allows you to hook scripts into Hamstercages execution. The name of each entry defines when to run the script; the contents of the dict define what command to execute.

The name consists of two parts separated by a dash: the prefix `pre` or `post`, and the name of the Hamstercage command. For example, the hook defined by the name `pre-save` will be executed when you run `hamstercage save`, just before the contents of the files will be copied from the target system to the repository. A hook `post-apply` will be run right after all files have been updated by the command `hamstercage apply`.

Hamstercage searches for a matching hook definition in this order:
1. for the exact match *step*`-`*command* (for example, `pre-save`)
2. for the wildcard match `*-`*command* (for example, `*-save`)
3. for the wildcard match *step*`-*` (for example, `pre-*`)
4. for the fallback wildcard `*`.

Only the first match found will be executed.

Each entry has these fields:
* `command`: the command or script to execute.
* `description`: allows you to add a description or note to this entry. Optional.
* `type`: the type of command or script to run.
  * `exec`: run the program specified by `command`. If `command` is not an absolute path, the system will search for it on the `PATH`. See [subprocess.Popen](https://docs.python.org/3/library/subprocess.html#subprocess.Popen) for details. The command will be invoked with the Hamstercage command, the step, and the tag the hook is defined in as parameters, for example `.../myhook.sh apply post all`.
  * `python`: `command` should specify a Python script. A relative path is interpreted relative to the repository directory. See below for global variables available to the script.
  * `shell`: run `command` through the shell. Typically, this will be the shell of the user running Hamstercage.

The `exec` and `shell` hooks receive the following environment variables:
* `HAMSTERCAGE_CMD`: the command being executed
* `HAMSTERCAGE_MANIFEST`: the manifest file path name
* `HAMSTERCAGE_HOOK`: the name of the hook entry
* `HAMSTERCAGE_REPO`: the repo directory, which is the directory the manifest file lives in
* `HAMSTERCAGE_STEP`: the step (`pre` or `post`)
* `HAMSTERCAGE_TAG`: the tag name this hook is defined in

The `python` script receives these global variables:
* `cmd`: the command being executed
* `manifest`: the manifest object
* `hook`: the name of the hook entry
* `repo`: the repo directory, which is the directory the manifest file lives in
* `step`: the step (`pre` or `post`)
* `tag`: the tag object this hook is defined in
* `__file__`: the Python hook file
* `__name__`: the constant `__hamstercage__`.

