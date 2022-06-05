---
title: "Introducing Hamstercage"
subtitle: "Pets, not Cattle"
date: 2022-06-05T08:55:19+02:00
---

**Hamstercage** is a tool to manage config files on hosts (bare metal, VMs, containers, jails). Unlike other systems, you primarily edit the config files on the target host, then use Hamstercage to save and distribute changes to other machines.

If you work professionally with many machines and configs, you like will have heard “cattle, not pets” as the philosophy for managing machines, VMs, etc., and you’re likely using Ansible, Puppet, SaltStack, or another configuration management system that allows you to express configuration as code. This approach works well when you have many targets that share many traits, you have a lab where you can test configuration changes, and you have full time staff to take care of it all.
However, if you’re running a handful of boxes or VPSes for a small organisation, or just for yourself and your friends and family, your workflow might actually look quite different: you make changes to the live configuration of your web server, for example, and after you’re satisfied that everything is working, you might want to save the key bits of config somewhere safe, so you can refer back to it later. Setting up any of the heavy tools can be cumbersome, especially for making quick provisional changes: in the worstcase scenario, you modify a file in the source repo, commit it, then run the tool to apply it to your machine.

The first version of Hamstercage implements the basic operations to create a repo, add files to it, and sync the contents and metadata of the files between the repository and the target system.