---
title: "Introducing Hamstercage"
subtitle: "Pets, not Cattle"
date: 2022-06-05T08:55:19+02:00
type: post
bigimg: [{src: "/img/welcome-sign.jpg", desc: ""}]
---

**Hamstercage** is a tool to manage config files on hosts (bare metal, VMs, containers, jails). Unlike other systems, you primarily edit the config files on the target host, then use Hamstercage to save and distribute changes to other machines.
<!--more-->

If you work professionally with many machines and configs, you likely will have heard “cattle, not pets” as the philosophy for managing machines, VMs, etc., and you’re likely using Ansible, Chef, Puppet, SaltStack, or another configuration management system that allows you to manage system configuration comprehensively. This approach works well when you have many targets that share many traits, you have a lab where you can test configuration changes, and you have full time staff to take care of it all.

However, if you’re running a handful of boxes or VPSes for a small organisation, or just for yourself and your friends and family, your workflow might actually look quite different: you make changes to the live configuration of your web server, for example, and after you’re satisfied that everything is working, you might want to save the key bits of config somewhere safe, so you can refer back to it later.

Setting up and using the “big guns” can be cumbersome, especially for making quick provisional changes: in the worst-case scenario, you modify a file in the source repo, commit it, then run the tool to apply it to your machine, before you find out if the change actually was correct. This “push” approach works best if you have a lab setup for your services, and the capability to have automated tests to verify that config changes have the desired effect. This is fantastic for large, important setups, but can be prohibitively complex for smaller cases.

Hamstercage works the other way around: you work on the files on the running machine, adjusting them to the desired state, then use Hamstercage to save them to a Git repository. If necessary, you can compare the currently active files with the version in the repository, and either save the current version to repository, or apply the repository version to the current system. By using a Git (or other) repo, you can track changes over time, and go back to an earlier version of a file, if necessary. All the files managed by Hamstercage are just copies of the files as they are installed on the target system, allowing you to easily understand which file goes where, and what contents it has. Important metadata like ownership an permission bits are part of the manifest, so you can verify and correct that easily.

Hamstercage is designed to do one thing and do it well: manage (config) files. It does not help you install packages, update databases, or produce files based on some template. You can use other tools to do these things and hook them into Hamstercage, though.

The first version of Hamstercage implements the basic operations to create a repo, add files to it, and sync the contents and metadata of the files between the repository and the target system.

Apart from the [documentation](../../documentation/), there is a [demo repository](https://github.com/hamstercage/hamstercage-demo/) that allows you to bring up an nginx web server Docker container and try out Hamstercage operations on files inside that container.