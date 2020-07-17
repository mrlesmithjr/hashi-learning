# Hashi Learning

A fun repository for learning all things HashiCorp.

## Getting Started

### Clone Repo

First let's go ahead and clone this repo. Because we use Git submodules, ensure
that you clone exactly like below:

```bash
git clone https://github.com/mrlesmithjr/hashi-learning.git --recursive
```

### Pre-Reqs

> NOTE: Windows support may be limited, but we are open to PR's, etc.

- [Python3](#python3)
- [Terraform](https://www.terraform.io/downloads.html)
- [Vagrant](https://www.vagrantup.com/downloads)
- [VirtualBox](https://www.virtualbox.org/wiki/Downloads)

#### Python3

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

## Background

Some of us like to get things up and running extremely quick and not have to
worry about the mundane tasks involved. This repo will do exactly that. By
leveraging Vagrant and Ansible to do all of the heavy lifting.

The functionality of this repo will grow over time iteratively. So, keep an eye
out for new functionality over time.

### Current Functionality

The following list is to outline the current functionality available.

- [Three node Consul cluster](#consul-cluster)
- [Three node Vault HA setup leveraging Consul](#vault-ha)

#### Consul Cluster

There are currently three nodes setup as a fully functional Consul cluster.

- consul01: `192.168.250.11`
- consul02: `192.168.250.12`
- consul03: `192.168.250.13`

#### Vault HA

There are currently three nodes setup as a fully functional Vault HA using Consul
as the storage.

- vault01: `192.168.250.21`
- vault02: `192.168.250.22`
- vault03: `192.168.250.23`

## Spinning Up

Now that all of the [getting started](#getting-started) tasks have been
followed, let's go ahead and spin up!

```bash
vagrant up
...
Bringing machine 'consul01' up with 'virtualbox' provider...
Bringing machine 'consul02' up with 'virtualbox' provider...
Bringing machine 'consul03' up with 'virtualbox' provider...
Bringing machine 'vault01' up with 'virtualbox' provider...
Bringing machine 'vault02' up with 'virtualbox' provider...
Bringing machine 'vault03' up with 'virtualbox' provider...
```

And once everything spins up you are ready to get your learning on.

```bash
PLAY RECAP *********************************************************************
consul01                   : ok=29   changed=12   unreachable=0    failed=0    skipped=30   rescued=0    ignored=0
consul02                   : ok=29   changed=12   unreachable=0    failed=0    skipped=29   rescued=0    ignored=0
consul03                   : ok=29   changed=12   unreachable=0    failed=0    skipped=29   rescued=0    ignored=0
vault01                    : ok=60   changed=22   unreachable=0    failed=0    skipped=20   rescued=0    ignored=0
vault02                    : ok=57   changed=21   unreachable=0    failed=0    skipped=23   rescued=0    ignored=0
vault03                    : ok=57   changed=21   unreachable=0    failed=0    skipped=23   rescued=0    ignored=0
```

### Consul Validation

Now that everything is spun up, open your browser of choice and let's do some quick Consul validations.

- [Consul Services](http://192.168.250.11:8500/ui/dc1/services)

  ![Consul Services](.images/2020-07-17-11-35-32.png)

- [Consul Node Status](http://192.168.250.11:8500/ui/dc1/nodes)

  ![Consul Node Status](.images/2020-07-17-11-37-31.png)

- [Consul Node Health Check](http://192.168.250.11:8500/ui/dc1/nodes/vault01)

  ![Consul Node Health Check](.images/2020-07-17-11-38-33.png)

### Vault Validation

Now that we've validated our Consul cluster, let's now take a look
at Vault.

> NOTE: Vault is in an unsealed state at the time of spinning up.

As part of the provisioning, our Vault keys are stored on each of the
Vault servers (Not the safest, but...). So, let's see how we can get access
to these.

Let's SSH into anyone of our Vault servers using `vagrant ssh vault[01-03]`.

```bash
vagrant ssh vault01
...
Welcome to Ubuntu 18.04.4 LTS (GNU/Linux 4.15.0-76-generic x86_64)

 * Documentation:  https://help.ubuntu.com
 * Management:     https://landscape.canonical.com
 * Support:        https://ubuntu.com/advantage

Last login: Fri Jul 17 15:42:58 2020 from 192.168.250.1
vagrant@vault01:~$
```

Now we will change into the `/etc/vault.d` directory and take a look.

```bash
cd /etc/vault.d
ls -la
...
vagrant@vault01:/etc/vault.d$ ls -la
total 16
drwxr-xr-x  2 root root  4096 Jul 17 14:44 .
drwxr-xr-x 80 root root  4096 Jul 17 14:43 ..
-r--------  1 root root   768 Jul 17 14:44 .hashi_vault_init.json
-rw-r--r--  1 root vault  493 Jul 17 14:44 vault-config.json
vagrant@vault01:/etc/vault.d$
```

And if you notice there is a hidden file `.hashi_vault_init.json` with only
read permissions for the `root` user. So, in order to view the keys we will need
to use `sudo`.

```bash
sudo cat .hashi_vault_init.json
...
vagrant@vault01:/etc/vault.d$ sudo cat .hashi_vault_init.json
{
    "keys": [
        "b1533be3b65c1e05858c550fcd2e722f828503ccdd4541ced7c14ee2aa404f7811",
        "78c46bf18d4ddc5d8d5c00954efcf327e42e2fe710962db8cb33fd2c2ad4b52dfb",
        "1d0acc10e611f6f6cd28fc4acc71c9f69495e29000749530bebd01bdcac426d82c",
        "30825bcbfbf1781229adfff74ec94d07bb1c39e7eea052dd62bd66edb70670fb0d",
        "4b620d270f19cf491b62841527f083debecfdbf2e7979a203d60c2aefeda191580"
    ],
    "keys_base64": [
        "sVM747ZcHgWFjFUPzS5yL4KFA8zdRUHO18FO4qpAT3gR",
        "eMRr8Y1N3F2NXACVTvzzJ+QuL+cQli24yzP9LCrUtS37",
        "HQrMEOYR9vbNKPxKzHHJ9pSV4pAAdJUwvr0BvcrEJtgs",
        "MIJby/vxeBIprf/3TslNB7scOefuoFLdYr1m7bcGcPsN",
        "S2INJw8Zz0kbYoQVJ/CD3r7P2/Lnl5ogPWDCrv7aGRWA"
    ],
    "root_token": "s.DQ84tbYvsaiG8sipOiK6OmLv"
}
vagrant@vault01:/etc/vault.d$
```

Now let's check our Vault status from the CLI while we're here.

First we will need to set an environment variable to define our `VAULT_ADDR`. This is required because we are not using TLS, which
is the default for the `vault` command.

```bash
export VAULT_ADDR="http://127.0.0.1:8200"
```

Now we can check our Vault status properly from the CLI.

```bash
vault status
...
vagrant@vault01:/etc/vault.d$ vault status
Key             Value
---             -----
Seal Type       shamir
Initialized     true
Sealed          false
Total Shares    5
Threshold       3
Version         1.4.1
Cluster Name    vault-cluster-57e7a21f
Cluster ID      2691685a-390d-50d9-48b4-e9efc459016a
HA Enabled      true
HA Cluster      https://192.168.250.21:8201
HA Mode         active
vagrant@vault01:/etc/vault.d$
```

While we are here, let's also take a quick look at our Vault configuration.

```bash
cat vault-config.json
...
vagrant@vault01:/etc/vault.d$ cat vault-config.json
{
    "api_addr": "http://192.168.250.21:8200",
    "cluster_addr": "http://192.168.250.21:8201",
    "listener": [
        {
            "tcp": {
                "address": "192.168.250.21:8200",
                "cluster_address": "192.168.250.21:8201",
                "tls_disable": true
            }
        },
        {
            "tcp": {
                "address": "127.0.0.1:8200",
                "tls_disable": true
            }
        }
    ],
    "storage": {
        "consul": {
            "address": "127.0.0.1:8500",
            "path": "vault/",
            "scheme": "http"
        }
    },
    "ui": true
}
vagrant@vault01:/etc/vault.d$
```

And if you notice in the `storage` portion, we are using the adress of `127.0.0.1`.

```bash
    "storage": {
        "consul": {
            "address": "127.0.0.1:8500",
            "path": "vault/",
            "scheme": "http"
        }
    },
```

The reason for this is that we are using a Consul client installation on the
Vault servers to properly join our Consul cluster.

If you take a look at the Consul configuration you'll see how that is done. So,
let's change into the `/etc/consul.d` directory.

```bash
cd /etc/consul.d
ls -la
...
vagrant@vault01:/etc/consul.d$ ls -la
total 16
drwxr-xr-x  4 root root 4096 Jul 17 14:43 .
drwxr-xr-x 80 root root 4096 Jul 17 14:43 ..
drwxr-xr-x  2 root root 4096 Jul 17 14:43 client
drwxr-xr-x  2 root root 4096 Jul 17 14:43 scripts
vagrant@vault01:/etc/consul.d$
```

Because we are using Consul as a client rather than a server, let's change into
the `client` directory.

```bash
cd client
ls -la
...
vagrant@vault01:/etc/consul.d/client$ ls -la
total 12
drwxr-xr-x 2 root root 4096 Jul 17 14:43 .
drwxr-xr-x 4 root root 4096 Jul 17 14:43 ..
-rw-r--r-- 1 root root  414 Jul 17 14:43 config.json
vagrant@vault01:/etc/consul.d/client$
```

In this directory you will find the `config.json` file. So, let's take a look
at that.

```bash
cat config.json
...
vagrant@vault01:/etc/consul.d/client$ cat config.json
{
    "bind_addr": "192.168.250.21",
    "client_addr": "0.0.0.0",
    "data_dir": "/var/consul/data",
    "datacenter": "dc1",
    "enable_syslog": true,
    "encrypt": "WWw4l0h1LbB4+pC5+VUWiV8kMBNQc+nEwt8OODMx2xg=",
    "log_level": "DEBUG",
    "node_name": "vault01",
    "retry_join": [
        "192.168.250.11",
        "192.168.250.12",
        "192.168.250.13"
    ],
    "server": false,
    "ui": true
}
vagrant@vault01:/etc/consul.d/client$
```

And if you take a look at the `retry_join` portion, you'll notice that we have
our three Consul servers defined.

## Tearing Down

When you are all done learning and are ready to tear everything down,
simply:

```bash
./scripts/cleanup.sh
```

## License

MIT

## Author Information

Larry Smith Jr.

- [@mrlesmithjr](https://twitter.com/mrlesmithjr)
- [mrlesmithjr@gmail.com](mailto:mrlesmithjr@gmail.com)
- [http://everythingshouldbevirtual.com](http://everythingshouldbevirtual.com)
