# 03. Vagrant

> Vagrant is a tool for building and managing virtual machine environments in a single workflow. With an easy-to-use workflow and focus on automation, Vagrant lowers development environment setup time, increases production parity, and makes the "works on my machine" excuse a relic of the past.

## Adding Images

For our learning, we will be adding the image we build previously with Packer.

```bash
vagrant box add local/ubuntu1804 ubuntu1804-virtualbox-1595373302.box
```

```bash
==> box: Box file was not detected as metadata. Adding it directly...
==> box: Adding box 'local/ubuntu1804' (v0) for provider:
    box: Unpacking necessary files from: file:///Users/larrysmithjr/Git_Projects/Personal/GitHub/mrlesmithjr/hashi-learning/learning/Packer/ubuntu1804-virtualbox-1595373302.box
==> box: Successfully added box 'local/ubuntu1804' (v0) for 'virtualbox'!
```
