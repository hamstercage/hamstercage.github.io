---
title: "Security Considerations"
type: page
weight: 6
bigimg: [{src: "/img/keys.jpg", desc: ""}]
---

Keeping your config files, some with sensitive contents, in a repo has security consequences.

# On the Host

For Hamstercage to be able to sync files between the repo and the target, a copy of the repo needs to be available locally. You should make sure that files in this repo are only accessible to authorized users, especially if you're managing files with secrets in them, for example, ssh host keys, a password database, or similar.

## umask

You should set a sensible value for umask. On many systems, the default is `022`, meaning that files will be created with `644` permission. This is likely not what you want when working with a repo that contains secrets. Depending on whether multiple users need to work with the repo, you should set the umask to `007` or `077`, meaning that files will be created with `660` or `600` mode.

**Git** does not manage file permission bits explicitly (apart from the execute bit), but relies on the operating system defaults, in particular the umask.

## Hamstercage

The files in the repo get created with the owner, group, and permissions copied over from the owner, group, and permissions that the manifest file has. By restricting access to the repo directory as well as the manifest file, the permissions on files added to the repo will also be set correctly.

# On the Git Repo Server

In general, it is considered bad security practice to check secrets into repositories, because any secret is only as safe as the security of the repo server. If you choose to check it secrets, make sure the Git repo is only accessible by authorized users.
