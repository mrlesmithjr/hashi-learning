# 09. Vault - Single Node

In this scenario, we will simply spin up a single node Vault server which will
provide us a learning environment to get familiar with basic Vault concepts.

## Spinning Up

First we need to export our scenario configuration for Vagrant:

```bash
export SCENARIO=scenarios/vault.yml
```

Now that we've exported our scenario configuration, we are ready to spin up
our environment:

```bash
vagrant up
```

And off we go! You will see a lot going on here as Vagrant and Ansible do their
job.

```bash
▶ vagrant up
Bringing machine 'vault01' up with 'virtualbox' provider...
...
PLAY RECAP *********************************************************************
vault01                    : ok=44   changed=14   unreachable=0    failed=0    skipped=23   rescued=0    ignored=0
```

### Nodes

This scenario has the following nodes when completed.

|  Node   |     IP #1      | IP #2 |
| :-----: | :------------: | :---: |
| vault01 | 192.168.250.21 |       |

## CLI

Now we are ready to begin exploring our Vault server using the CLI. So, to do
this. Let's SSH into our `vault01` node.

```bash
vagrant ssh vault01
```

```bash
▶ vagrant ssh vault01
Welcome to Ubuntu 18.04.4 LTS (GNU/Linux 4.15.0-76-generic x86_64)

 * Documentation:  https://help.ubuntu.com
 * Management:     https://landscape.canonical.com
 * Support:        https://ubuntu.com/advantage

Last login: Wed Jul 22 02:01:12 2020 from 10.0.2.2
vagrant@vault01:~$
```

Now that we've logged in, let's change into the `/etc/vault.d` directory and
see what files/directories are present.

```bash
cd /etc/vault.d
ls -la
```

```bash
vagrant@vault01:~$ cd /etc/vault.d
vagrant@vault01:/etc/vault.d$ ls -la
total 16
drwxr-xr-x  2 root root  4096 Jul 22 02:00 .
drwxr-xr-x 79 root root  4096 Jul 22 02:00 ..
-r--------  1 root root   768 Jul 22 02:00 .hashi_vault_init.json
-rw-r--r--  1 root vault  368 Jul 22 02:00 vault-config.json
vagrant@vault01:/etc/vault.d$
```

### CLI - Configuration

> NOTE: Vault is in an unsealed state at the time of spinning up.

As part of the provisioning, our Vault keys are stored on each of the
Vault servers (Not the safest, but...). So, let's see how we can get access
to these.

In our `/etc/vault.d` directory there is a hidden file `.hashi_vault_init.json` with only read permissions for the `root` user. So, in order to view the keys we will need to use `sudo`.

```bash
sudo cat .hashi_vault_init.json
```

```bash
vagrant@vault01:/etc/vault.d$ sudo cat .hashi_vault_init.json
{
    "keys": [
        "1d110968b43499898b919912af59f1debf107304c6273b343dcff41c510cdd4929",
        "51a7f642c73c49354c004d77736e9dd7b889b532f339c939d7f2490e3768afbbee",
        "fbaf4298ad4a93f7045a98829f2623c6b9b5a4cd580ccbaf50b07344a3374ebb9c",
        "a95dd1c8b96d9cd11b49fc3ebebc92161706c959d4d36c8e5180390eadf46fa261",
        "074af393c1bfe94052140561591b14f948705e7a129bbc9510e1e01a042487853e"
    ],
    "keys_base64": [
        "HREJaLQ0mYmLkZkSr1nx3r8QcwTGJzs0Pc/0HFEM3Ukp",
        "Uaf2Qsc8STVMAE13c26d17iJtTLzOck51/JJDjdor7vu",
        "+69CmK1Kk/cEWpiCnyYjxrm1pM1YDMuvULBzRKM3Truc",
        "qV3RyLltnNEbSfw+vrySFhcGyVnU02yOUYA5Dq30b6Jh",
        "B0rzk8G/6UBSFAVhWRsU+UhwXnoSm7yVEOHgGgQkh4U+"
    ],
    "root_token": "s.Epa2crdqGBbe9LpOLkJtuwJG"
}
vagrant@vault01:/etc/vault.d$
```

Let's first take a quick look at our Vault configuration while we're here.

```bash
cat vault-config.json
```

```bash
vagrant@vault01:/etc/vault.d$ cat vault-config.json
{
    "api_addr": "http://192.168.250.21:8200",
    "listener": [
        {
            "tcp": {
                "address": "127.0.0.1:8200",
                "cluster_address": "127.0.0.1:8201",
                "tls_disable": true
            }
        }
    ],
    "storage": {
        "file": {
            "path": "/var/lib/vault"
        }
    },
    "ui": true
}
vagrant@vault01:/etc/vault.d$
```

### CLI - Status

Now that we've checked out our Vault configuration, let's check the status.

First we will need to set an environment variable to define our `VAULT_ADDR`.
This is required because we are not using TLS, which is the default for the
`vault` command.

```bash
export VAULT_ADDR="http://127.0.0.1:8200"
```

Now we can check our Vault status properly from the CLI.

```bash
vault status
```

```bash
vagrant@vault01:/etc/vault.d$ vault status
Key             Value
---             -----
Seal Type       shamir
Initialized     true
Sealed          false
Total Shares    5
Threshold       3
Version         1.4.1
Cluster Name    vault-cluster-1bd459aa
Cluster ID      8041b47b-edc0-6f9e-4360-9999e3f4640e
HA Enabled      false
vagrant@vault01:/etc/vault.d$
```
