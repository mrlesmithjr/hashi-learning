# 02. Packer

> HashiCorp Packer automates the creation of any type of machine image. It embraces modern configuration management by encouraging you to use automated scripts to install and configure the software within your Packer-made images. Packer brings machine images into the modern age, unlocking untapped potential and opening new opportunities.

## Installing

Before we can get started using Packer, we need to first install it. So, please
follow the instructions found [here](https://www.packer.io/downloads) based on
your OS.

## Images

Now that you've installed Packer, let's go ahead and build our first image. Our
first image will be a `Ubuntu 18.04` server image. We will be using this image
as part of our learning from here on out once we've completed the build.

### Template

The very first thing you'll need in order to build a Packer image is a template.
A Packer template is the configuration that describes the image that you intend
to build. This template is written in JSON.

We have included a `Ubuntu 18.04` template as part of this repository that will
be used. You can find this template in `learning/Packer`, and the template name
is `ubuntu1804.json`.

#### Ubuntu 18.04 Template

The following template is the one we will be using here in a few moments.

```json
{
  "variables": {
    "boot_command_prefix": "<enter><wait><f6><esc><bs><bs><bs><bs><bs><bs><bs><bs><bs><bs><bs><bs><bs><bs><bs><bs><bs><bs><bs><bs><bs><bs><bs><bs><bs><bs><bs><bs><bs><bs><bs><bs><bs><bs><bs><bs><bs><bs><bs><bs><bs><bs><bs><bs><bs><bs><bs><bs><bs><bs><bs><bs><bs><bs><bs><bs><bs><bs><bs><bs><bs><bs><bs><bs><bs><bs><bs><bs><bs><bs><bs><bs><bs><bs><bs><bs><bs><bs><bs><bs><bs><bs><bs>",
    "compression_level": "6",
    "iso_checksum": "e2ecdace33c939527cbc9e8d23576381c493b071107207d2040af72595f8990b",
    "iso_url": "http://cdimage.ubuntu.com/releases/18.04/release/ubuntu-18.04.4-server-amd64.iso",
    "vm_disk_adapter_type": "scsi",
    "vm_disk_size": "36864",
    "vm_memory": "1024",
    "vm_name": "ubuntu1804",
    "vm_ssh_password": "vagrant",
    "vm_ssh_username": "vagrant",
    "vm_vcpu": "1"
  },
  "builders": [
    {
      "boot_command": [
        "{{ user `boot_command_prefix` }}",
        "<wait>",
        "/install/vmlinuz",
        "<wait>",
        " initrd=/install/initrd.gz",
        "<wait>",
        " auto=true",
        "<wait>",
        " priority=critical",
        "<wait>",
        " url=http://{{ .HTTPIP }}:{{ .HTTPPort }}/ubuntu/preseed.cfg <wait>",
        "<wait>",
        "<enter>"
      ],
      "cpus": "{{ user `vm_vcpu` }}",
      "disk_size": "{{ user `vm_disk_size` }}",
      "guest_os_type": "Ubuntu_64",
      "hard_drive_interface": "{{ user `vm_disk_adapter_type` }}",
      "headless": true,
      "http_directory": "http",
      "iso_checksum": "{{ user `iso_checksum` }}",
      "iso_url": "{{ user `iso_url` }}",
      "memory": "{{ user `vm_memory` }}",
      "output_directory": "output-{{ user `vm_name` }}-{{ build_type }}-{{ timestamp }}",
      "shutdown_command": "echo '/sbin/halt -h -p' > shutdown.sh; echo 'packer'|sudo -S bash 'shutdown.sh'",
      "ssh_password": "{{ user `vm_ssh_password` }}",
      "ssh_username": "{{ user `vm_ssh_username` }}",
      "type": "virtualbox-iso",
      "vm_name": "{{ user `vm_name` }}-{{ timestamp }}",
      "ssh_timeout": "60m"
    }
  ],
  "provisioners": [
    {
      "type": "shell",
      "scripts": [
        "scripts/base.sh",
        "scripts/vagrant.sh",
        "scripts/virtualbox.sh",
        "scripts/vmware.sh",
        "scripts/cleanup.sh",
        "scripts/zerodisk.sh"
      ]
    }
  ],
  "post-processors": [
    [
      {
        "compression_level": "{{ user `compression_level` }}",
        "output": "{{ user `vm_name` }}-{{.Provider}}-{{ timestamp }}.box",
        "type": "vagrant"
      },
      {
        "type": "manifest",
        "output": "manifest.json",
        "strip_path": true
      }
    ]
  ]
}
```

#### Builders

#### Provisioners

#### Post-Processors

### Validating

Now that we have the template that we will be using, we should first validate
it. So, let's go ahead and do that now.

```bash
cd learning/Packer
packer validate ubuntu1804.json
```

### Building

Now that our template has been validated, we are ready to build our first image.
So, if you are not currently in the `learning/Packer` directory, let's go ahead
and change into that directory.

Now that we're in the `learning/Packer` directory, let's go ahead and build our
image.

```bash
packer build ubuntu1804.json
```

```bash
virtualbox-iso: output will be in this color.

==> virtualbox-iso: Retrieving Guest additions
==> virtualbox-iso: Trying /Applications/VirtualBox.app/Contents/MacOS/VBoxGuestAdditions.iso
==> virtualbox-iso: Trying /Applications/VirtualBox.app/Contents/MacOS/VBoxGuestAdditions.iso
==> virtualbox-iso: /Applications/VirtualBox.app/Contents/MacOS/VBoxGuestAdditions.iso => /Applications/VirtualBox.app/Contents/MacOS/VBoxGuestAdditions.iso
==> virtualbox-iso: Retrieving ISO
==> virtualbox-iso: Trying http://cdimage.ubuntu.com/releases/18.04/release/ubuntu-18.04.4-server-amd64.iso
==> virtualbox-iso: Trying http://cdimage.ubuntu.com/releases/18.04/release/ubuntu-18.04.4-server-amd64.iso?checksum=sha256%3Ae2ecdace33c939527cbc9e8d23576381c493b071107207d2040af72595f8990b
ubuntu-18.04.4-server-amd64.iso 532.94 MiB / 921.00 MiB [=================================================>-----------------------------------]  57.86% 00m28s
...
==> Builds finished. The artifacts of successful builds are:
--> virtualbox-iso: 'virtualbox' provider box: ubuntu1804-virtualbox-1595373302.box
--> virtualbox-iso:
```

And once the build completes you will have a `ubuntu-virtualbox-**********.box`
file which is our Vagrant box image that we will be using. In the example
above, my Vagrant box file was `ubuntu1804-virtualbox-1595373302.box`.
