# 03. Vagrant

> Vagrant is a tool for building and managing virtual machine environments in a single workflow. With an easy-to-use workflow and focus on automation, Vagrant lowers development environment setup time, increases production parity, and makes the "works on my machine" excuse a relic of the past.

## Boxes

### Adding

For our learning, we will be adding the box we built using Packer back in
[02. Packer](02_Packer.md#building). So, let's go ahead and do this now.

> NOTE: Replace the `1595459323` with whatever matches your build.

```bash
vagrant box add local/ubuntu1804 ubuntu1804-virtualbox-1595459323.box
```

```bash
==> box: Box file was not detected as metadata. Adding it directly...
==> box: Adding box 'local/ubuntu1804' (v0) for provider:
    box: Unpacking necessary files from: file:///Users/larrysmithjr/Git_Projects/Personal/GitHub/mrlesmithjr/hashi-learning/learning/Packer/ubuntu1804-virtualbox-1595459323.box
==> box: Successfully added box 'local/ubuntu1804' (v0) for 'virtualbox'!
```

We've successfully added our first Vagrant box! And as we mentioned previously,
we will be using this image for the remainder of this learning.

However, before we move on, let's add another box.

```bash
vagrant box add mrlesmithjr/centos7
```

```bash
▶ vagrant box add mrlesmithjr/centos7
==> box: Loading metadata for box 'mrlesmithjr/centos7'
    box: URL: https://vagrantcloud.com/mrlesmithjr/centos7
==> box: Adding box 'mrlesmithjr/centos7' (v1588339341) for provider: libvirt
    box: Downloading: https://vagrantcloud.com/mrlesmithjr/boxes/centos7/versions/1588339341/providers/libvirt.box
Download redirected to host: vagrantcloud-files-production.s3.amazonaws.com
==> box: Successfully added box 'mrlesmithjr/centos7' (v1588339341) for 'libvirt'!
```

We've now just successfully added a new box from [Vagrant Cloud](https://app.vagrantup.com/boxes/search) for `CentOS 7` using the `libvirt` provider.

### Listing

Now that we've added our first box, we can get a quick list of boxes currently
available by executing:

```bash
vagrant box list
```

```bash
▶ vagrant box list
local/ubuntu1804    (virtualbox, 0)
mrlesmithjr/centos7 (libvirt, 1588339341)
```
