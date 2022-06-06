---
title: "Home"
date: 2022-06-05T08:55:19+02:00
---

If you work professionally with many machines and configs, you likely will have heard “cattle, not pets” as the philosophy for managing machines, VMs, etc., and you're likely using Ansible, Puppet, SaltStack, or another configuration management system that allows you to express configuration as code. This approach works well when you have many targets that share many traits, you have a lab where you can test configuration changes, and you have full time staff to take care of it all.

Hamstercage aims to solve a different problem: you run a handful of machines (VMs, VPSes, etc.) for yourself and your family, or a small company, and you work on the configs right on the production system, making adjustments as you go along. Hamstercage helps you to sync the relevant files with a Git repo, so you can easily track changes over time, go back to an earlier version if necessary, or distribute common config files among those machines.

## Documentation and Links

* [Documentation Overview](documentation)
* [github.com/hamstercage/hamstercage](https://github.com/hamstercage/hamstercage) source code repository
* [Hamstercage at PyPI](https://pypi.org/project/hamstercage/)