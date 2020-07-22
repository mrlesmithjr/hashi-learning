# 01. Getting Started

## Cloning Repo

First let's go ahead and clone this repo. Because we use Git submodules, ensure
that you clone exactly like below:

```bash
git clone https://github.com/mrlesmithjr/hashi-learning.git --recursive
```

## Pre-Reqs

> NOTE: Windows support may be limited, but we are open to PR's, etc.

- [Python3](#python3)
- [VirtualBox](#virtualbox)

### Python3

> NOTE: Ensure that you have a working Python3 setup as we will be using a Python
> virtualenv as part of this repo.

Now let's setup our Python virtualenv. We will be using a virtualenv to ensure
not to clutter up your existing Python environment.

```bash
pip3 install virtualenv
python3 -m venv venv
source venv/bin/activate
pip3 install --upgrade pip pip-tools
pip-sync requirements.txt requirements-dev.txt
```

### VirtualBox

We will be using VirtualBox for our Virtualization. Vagrant will also be
using Virtualbox for it's provider throughout this learning.

Follow the setup for Virtualbox [here](https://www.virtualbox.org/wiki/Downloads).
