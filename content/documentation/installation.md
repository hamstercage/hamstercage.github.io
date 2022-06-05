---
title: "Installation"
weight: 2
---

Hamstercage is a Python module. You can install it from [the source repository](https://github.com/hamstercage/hamstercage/), or from [PyPi(https://pypi.org/project/hamstercage/)].


```shell
pip install hamstercage
```

## Using Hamstercage

After installation, Hamstercage is available on the path as `hamstercage`.

### Creating a new Hamstercage repo

Hamstercage needs a manifest file in order to track files managed through it. You can create this file manually, or you can have Hamstercage create a template file for you:

```shell
hamstercage init
```

This creates `hamstercage.yaml` in the current directory.

### The Hamstercage Manifest

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
      foo.txt:
        group: staff
        mode: 0o644
        owner: stb
        type: file
```
