---
title: "Theory of Operation"
type: page
weight: 1
---

Hamstercage keeps a list of files under management in a **manifest** file, by default `hamstercage.yaml`. The various Hamstercage commands can be used to add, update, and remove files from the manifest, but you can always make changes manually, for example to correct the owner or mode of a file.

Managed files are kept not as a single list, but as multiple lists, one for each **tag**. A tag is simply a label for a set of files. You are free to choose tags to organize sets of files as you see fit, for example, by purpose, application, operating system, or any other metric.

In addition to the managed files, the manifest also contains a list of **hostnames**, with the tags that should be applied to each hostname. This allows you to run the same Hamstercage command across multiple hosts and have files applied according to the hosts' respective tags.

The contents of files is kept alongside the manifest, using the path of the target. For example, a target file `/etc/profile` would be stored in `repo/all/etc/profile`.

While Hamstercage manages files very efficiently, there are some configuration tasks that require manipulation of existing files, refreshing a set of files, or restarting a daemon. Hamstercage does not attempt to implement any such actions. **Hooks** allow you to supply your own custom logic for these kinds of actions. You can define hooks to run before or after a command is executed (for example before a `save` or after an `apply`). Hooks are defined on tags, so you can cater the commands to the set of files that are managed. Hooks can be individual shell commands, any executable including scripts, or Python programs that are executed inside Hamstercages Python process.

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

## Managing the same file in multiple tags

When using multiple tags, it is possible that a file in the target has an entry in more than one tag. Consider this manifest:
```yaml
hosts:
  testing.example.com:
    description: ''
    tags:
    - jumphost
    - all
  web.example.com:
    description: ''
    tags:
    - web
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
  jumphost:
    description: files that apply only to jumphosts
    entries:
      /etc/profile:
        group: root
        mode: 0o444
        owner: root
        type: file
  web:
    description: files that apply only to jumphosts
```

The file `/etc/profile` is kept in the repository both in the `all` and in the `jumphosts` tags.

When processing files, Hamstercage will always consider the entry in the first tag that defines it. When applying or saving the files on `testing.example.com`, the entry in `jumphost` will be selected, because the tag `jumphost` comes first in the list of tags for that host. On `web.example.com`, no entry exists in the tag `web`, so the entry in `all`is selected.