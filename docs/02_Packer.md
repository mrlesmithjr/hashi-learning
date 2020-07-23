# 02. Packer

> HashiCorp Packer automates the creation of any type of machine image. It embraces modern configuration management by encouraging you to use automated scripts to install and configure the software within your Packer-made images. Packer brings machine images into the modern age, unlocking untapped potential and opening new opportunities.

## Installing

Before we can get started using Packer, we need to first install it. So, please
follow the instructions found [here](https://www.packer.io/downloads) based on
your OS.

## Template

Now that Packer is installed, let's begin walking through what is required to
build our first image.

The very first thing you'll need, is a template. A Packer template is the configuration that describes the image that you intend to build. This template is written in JSON.

We have included a `Ubuntu 18.04` template as part of this repository that will
be used. You can find this template in `learning/Packer`, and the template name
is `ubuntu1804.json`.

> NOTE: Our `Ubuntu 18.04` image will be what we use from here on our throughout
> this learning.

### Ubuntu 18.04 Template

The following template is the one we will be using here in a few moments.

```json
{
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
      "ssh_timeout": "60m",
      "ssh_username": "{{ user `vm_ssh_username` }}",
      "type": "virtualbox-iso",
      "vm_name": "{{ user `vm_name` }}-{{ timestamp }}"
    },
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
      "disk_adapter_type": "{{ user `vm_disk_adapter_type` }}",
      "disk_size": "{{ user `vm_disk_size` }}",
      "guest_os_type": "ubuntu-64",
      "headless": true,
      "http_directory": "http",
      "iso_checksum": "{{ user `iso_checksum` }}",
      "iso_url": "{{ user `iso_url` }}",
      "memory": "{{ user `vm_memory` }}",
      "output_directory": "output-{{ user `vm_name` }}-{{ build_type }}-{{ timestamp }}",
      "shutdown_command": "echo '/sbin/halt -h -p' > shutdown.sh; echo 'packer'|sudo -S bash 'shutdown.sh'",
      "ssh_password": "{{ user `vm_ssh_password` }}",
      "ssh_timeout": "60m",
      "ssh_username": "{{ user `vm_ssh_username` }}",
      "type": "vmware-iso",
      "vm_name": "{{ user `vm_name` }}-{{ timestamp }}"
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
        "output": "manifest.json",
        "strip_path": true,
        "type": "manifest"
      }
    ]
  ],
  "provisioners": [
    {
      "scripts": [
        "scripts/base.sh",
        "scripts/vagrant.sh",
        "scripts/virtualbox.sh",
        "scripts/vmware.sh",
        "scripts/cleanup.sh",
        "scripts/zerodisk.sh"
      ],
      "type": "shell"
    }
  ],
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
  }
}
```

### User Variables

User variables are defined within a template as `variables`. These variables
allow us to parameterize portions of our templates and make them portable. We
can also pass variables as arguments (`-var`, `-var-file`) when executing Packer
to allow us to pass defaults along.

By using the `-var` argument, we can define a specific variable such as:

```bash
packer build -var username=superuser -var password=supersecretpassword template.json
```

We can also use the `-var-file` argument to pass additional variables from a
JSON file. An example of this could be:

`variables.json`:

```json
{ "username": "superuser", "password": "supersecretpassword" }
```

And then we can pass this onto Packer:

```bash
packer build -var-file variables.json template.json
```

### Builders

Builders are what Packer uses to create and generate OS images. Builders are
available for numerous different platforms and each one has their own specific
configuration options.

Some examples of Packer builders include:

- [Azure](https://www.packer.io/docs/builders/azure)
- [DigitalOcean](https://www.packer.io/docs/builders/digitalocean)
- [Vagrant](https://www.packer.io/docs/builders/vagrant)
- [VirtualBox](https://www.packer.io/docs/builders/virtualbox)
- [VMware](https://www.packer.io/docs/builders/vmware)

> NOTE: Our `ubuntu1804.json` template has the following builders:
> `virtualbox-iso` and `vmware-iso`.

### Provisioners

Provisioners are an array of provisioners that Packer will use to install, configure, etc. within our image. These may include Ansible, Chef, Puppet, Shell scripts, Powershell, etc. Provisioners are also optional within a template.

### Post-Processors

Post-processors are tasks that execute after any provisioners that may be defined.
Otherwise, after the builders complete. Post-processors are used for artifacts,
conversions, etc.

#### Vagrant

The Vagrant post-processor comsumes an existing build and then converts it to
a consumable Vagrant box. One thing to note is that all Vagrant boxes are
provider specific. So, whichever Packer builder is used when creating an image.
The Vagrant post-processor will generate the Vagrant box for that provider.

## Validating

Now that we have the template that we will be using, we should first validate
it. So, let's go ahead and do that now.

```bash
cd learning/Packer
packer validate ubuntu1804.json
```

## Building

Now that our template has been validated, we are ready to build our first image.
So, if you are not currently in the `learning/Packer` directory, let's go ahead
and change into that directory.

Now that we're in the `learning/Packer` directory, let's go ahead and build our
image. Because we are using VirtualBox as our default virtualization, we will
only build for our `virtualbox-iso` builder.

```bash
packer build -only virtualbox-iso ubuntu1804.json
```

```bash
▶ packer build -only virtualbox-iso ubuntu1804.json
virtualbox-iso: output will be in this color.

==> virtualbox-iso: Retrieving Guest additions
==> virtualbox-iso: Trying /Applications/VirtualBox.app/Contents/MacOS/VBoxGuestAdditions.iso
==> virtualbox-iso: Trying /Applications/VirtualBox.app/Contents/MacOS/VBoxGuestAdditions.iso
==> virtualbox-iso: /Applications/VirtualBox.app/Contents/MacOS/VBoxGuestAdditions.iso => /Applications/VirtualBox.app/Contents/MacOS/VBoxGuestAdditions.iso
==> virtualbox-iso: Retrieving ISO
==> virtualbox-iso: Trying http://cdimage.ubuntu.com/releases/18.04/release/ubuntu-18.04.4-server-amd64.iso
==> virtualbox-iso: Trying http://cdimage.ubuntu.com/releases/18.04/release/ubuntu-18.04.4-server-amd64.iso?checksum=sha256%3Ae2ecdace33c939527cbc9e8d23576381c493b071107207d2040af72595f8990b
==> virtualbox-iso: http://cdimage.ubuntu.com/releases/18.04/release/ubuntu-18.04.4-server-amd64.iso?checksum=sha256%3Ae2ecdace33c939527cbc9e8d23576381c493b071107207d2040af72595f8990b => /Users/larrysmithjr/Git_Projects/Personal/GitHub/mrlesmithjr/hashi-learning/learning/Packer/packer_cache/23ac40ee61da5cc4ecaa017cd6d33a162365c3ac.iso
==> virtualbox-iso: Starting HTTP server on port 8046
==> virtualbox-iso: Creating virtual machine...
==> virtualbox-iso: Creating hard drive...
==> virtualbox-iso: Creating forwarded port mapping for communicator (SSH, WinRM, etc) (host port 3952)
==> virtualbox-iso: Starting the virtual machine...
    virtualbox-iso: The VM will be run headless, without a GUI. If you want to
    virtualbox-iso: view the screen of the VM, connect via VRDP without a password to
    virtualbox-iso: rdp://127.0.0.1:5933
==> virtualbox-iso: Waiting 10s for boot...
==> virtualbox-iso: Typing the boot command...
==> virtualbox-iso: Using ssh communicator to connect: 127.0.0.1
==> virtualbox-iso: Waiting for SSH to become available...
...
==> Builds finished. The artifacts of successful builds are:
--> virtualbox-iso: 'virtualbox' provider box: ubuntu1804-virtualbox-1595459323.box
--> virtualbox-iso:
```

And once the build completes you will have a `ubuntu-virtualbox-**********.box`
file which is our Vagrant box image that we will be using. In the example
above, my Vagrant box file was `ubuntu1804-virtualbox-1595459323.box`.

## Consuming

We will explore importing this new generated image in [03_Vagrant](03_Vagrant.md#adding-images) and see how we can consume this image with Vagrant.
