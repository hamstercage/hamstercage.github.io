---
title: "Why Hamstercage"
subtitle: "Alternatives, and why they might not be a good fit"
date: 2022-06-06T10:33:00+02:00
type: post
---
Hamstercage is not unique in its goal to manage config files. Here is an overview of some other tools, and how it differs from them.

While this discussion focuses on the use of Git as a version control system, others can be used interchangeably.

# The Big Guns

When managing large amounts of systems, a number of requirements come to the fore:
* Manage all aspects of the entire landscape from a single source
* Roll out changes using an automated, centralized process
* Make it possible to configure a large number of hosts that are very similar
* Have clear reporting on changes to the systems
* Ability to verify correct configuration with automated tests
* Ability to deploy to a test bed to verify changes before they are applied to production

There are probably many more such aspects, but the main takeaway for configuration management systems supporting many or all of these requirements is that they are large and complex. Mastering them brings with it a steep learning curve, and depending on the size of your operation, you might need dedicated hardware just for the configuration management solution, and maybe dedicated staff.

These tools use a “push” approach, where the target systems are actively deployed from a (more or less) centralized infrastructure; the individual hosts and their configuration is overwritten from the central store.

Examples of these products include:
* [Ansible](https://www.ansible.com)
* [Chef](https://www.chef.io)
* [Puppet](https://puppet.com)
* [SaltStack](https://saltproject.io)

## Configuring Packages Through an Abstraction

One aspect that might make using an approach like this more complicated is the actual configuration: typically, you do not create complete, custom config files yourself, but you rely on modules for these systems to create the config files automatically from variables you provide. This can work extremly well, reducing large config files to just a handful of variables.

However, it requires these modules to provide a good abstraction of the configuration options for a packages. The quality of these abstractions, and how well they fit your use case, can vary significantly, and the complexity of these modules can make chasing configuration problems quite hard, as it is sometimes not easy to determine where a certain line in a deployed config files actually comes from, or why setting a variable in the manifest does not lead to the desired results.

Hamstercage foregoes any such abstractions, managing the contents of the config files directly.

# Manage Config Files Directly Through Git

This approach is using a “pull” approach, where changes to files shared between hosts are pulled individually on each host.

It is possible to manage config files directly in a Git repo, however, there are some limitations:
* Git does not keep track of file ownership and permissions (except for the execute bit)
* If you want to manage not only files in `/etc`, but also in other places (like `/usr/local/etc`), the Git repo might span the entire filesystem. This can be inefficient, or lead to accidents when files are changed by Git that were not intended to be changed.
* Tricks can be played with symbolic links, but this can be confusing.

There are a number of recipes to be found on how to set up such a Git repo, and how to work around some of the issues. However, most of them seem to be homegrown, single approaches without good documentation.

# Manage Config Files Through Git With a Helper: etckeeper

One example of augmenting Git so it can manage the necessary data is [etckeeper](https://etckeeper.branchable.com): it hooks into the version control system to fix up files before checkin, and after checkout.

etckeeper would seem like it already does pretty much what Hamstercage does, but there are some crucial differences:
* etckeeper can only manage a single directory, so if you want to manage both `/etc` and `/usr/local/etc`, you will need two Git repos, or you will need your Git repo to span the entire filesystem.
* etckeeper manages the files directly in the Git repo; a `git pull` wil immediately change the system files.
* the repo can only apply to a single host.
* etckeeper is aimed at Debian-based Linux distributions; it's possible to use on other distributions or BSD, but that makes it more complicated.
* etckeeper hooks into the package manager, automatically creating commits when packages are installed. This might add a lot of files to the repo that remain unchanged from the package defaults.
* etckeeper can automatically commit all changes once a day, keeping a record of incidential changes.

In contrast, Hamstercage:
* can manage files anywhere in the filesystem
* sync between the installed files and the Git repo, with control over which changes to apply or save
* manage multiple hosts from a single repo
* works on any host that can run Python and Git
* does not require interaction with a package manager, or anything else
* does not automatically save changes to the managed files

In summary, etckeeper is focused on managing `/etc/` with as little interaction as possible, whereas Hamstercage takes a more hands-off approach, forcing you to create automatic actions yourself, for example, with a custom cron job.

# And now for something completely different

A relatively new approach for managing packages and configuration (and the way packages are built and installed) comes from [NixOS](https://nixos.org), which uses a purely functional model. This approach treats the config files (and everything else) not as something to be edited directly, but as the result of a function. This is similar to configuration management systems, but differs in the way the configuration is created and applied to the target.

In a sense, think of NixOS and the nix package manager as following the same principles as building Docker images, and being able to install those images as native packages on your system.

This approach is a very different way of managing your system, and it might work really well, but it also is quite different from “classical” system administration, much the same way that using Docker Compose or Kubernetes differs from managing VMs to run services.

NixOS and the nix package manager also rely on package configuration being controlled throught the package and variables that you set when creating the packages, like the large configuration management systems. The upsides and downsides apply here as well.