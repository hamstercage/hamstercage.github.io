---
title: "Theory of Operation"
type: page
weight: 1
bigimg: [{src: "/img/math-work.jpg", desc: ""}]
---

Hamstercage keeps a list of files under management in a **manifest** file, by default `hamstercage.yaml`. The various Hamstercage commands can be used to add, update, and remove files from the manifest, but you can always make changes manually, for example to correct the owner or mode of a file.

Managed files are kept not as a single list, but as multiple lists, one for each **tag**. A tag is simply a label for a set of files. You are free to choose tags to organize sets of files as you see fit, for example, by purpose, application, operating system, or any other metric.

In addition to the managed files, the manifest also contains a list of **hostnames**, with the tags that should be applied to each hostname. This allows you to run the same Hamstercage command across multiple hosts and have files applied according to the hosts' respective tags.

The contents of files is kept alongside the manifest, using the path of the target. For example, a target file `/etc/profile` would be stored in `tags/all/etc/profile`.

While Hamstercage manages files very efficiently, there are some configuration tasks that require manipulation of existing files, refreshing a set of files, or restarting a daemon. Hamstercage does not attempt to implement any such actions. **Hooks** allow you to supply your own custom logic for these kinds of actions. You can define hooks to run before or after a command is executed (for example before a `save` or after an `apply`). Hooks are defined on tags, so you can cater the commands to the set of files that are managed. Hooks can be individual shell commands, any executable including scripts, or Python programs that are executed inside Hamstercage's Python process.

Hooks are called for these commands:
* [`apply`](../command-reference/#adding-files-add)
* [`diff`](../command-reference/#determining-differences-diff)
* [`save`](../command-reference/#save-target-to-repository-save)

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

## Interaction with version control systems

Hamstercage is designed to produce a repository that can be checked in and managed through a version control system like Git. Hamstercage does not make any assumptions about how that system works, so you can use whichever system you prefer.

## Permissions in the repo

When you add a file to a tag in the repo, Hamstercage copies the contents of the file from the target. The owner, group, and permissions of the file are added to the metadata in the manifest, but the file in the repo will receive the same owner, group, and permissions as the manifest file. For example, if you add `/etc/profile` to the repo (which is not executable), the file in the repo will be owned by the same account as the manifest, and the file mode will be the same as the manifest. When you add a file that has the execute bit set, that will be set on the repo file as well.

```
$ ls -ld hamstercage.yaml /etc/profile tags/all/etc/profile
-rw-r--r--  1 root  wheel   488 Nov  1  2020 /etc/profile
-rw-r--r--  1 root  wheel  5152 Jun  4 22:31 hamstercage.yaml
-rw-rw----  1 root  wheel  1130 Jan 21  2017 tags/all/etc/profile
```

In the above example, the repository directory has group wheel, and only the user and group have access to it; all the files in the tags have ownership and permissions accordingly.

This allows you to control who has access to the Hamstercage repo and its contents. If you share admin duties for the machines with other people, you can put the repo into a shared directory, but adjust the ownership and permissions so that only the admins have access.

For best results interacting with the version control system, you should make sure that your umask is set to `002`, so that files created by your version control system are group-writable. This ensures that your fellow admins can edit files without having to change their permissions first.

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

## Defining Hooks

See [Manifest, Tags: Hooks](..manifest/tags-hooks) for details on defining hooks.