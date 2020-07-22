# Hashi Learning

A fun repository for learning all things HashiCorp.

You can find the curated learning material at [https://mrlesmithjr.github.io/hashi-learning/](https://mrlesmithjr.github.io/hashi-learning/).

#### Vault HA

There are currently three nodes setup as a fully functional Vault HA using Consul
as the storage.

- vault01: `192.168.250.21`
- vault02: `192.168.250.22`
- vault03: `192.168.250.23`

## Spinning Up

### Spinning Up - Scenarios

#### Spinning Up - Vault HA

In this scenario, we will spin up a three node Consul cluster along with three
Vault servers configured in HA. This scenario is perfect to learn a bit about
how Vault and Consul function together in HA.

To spin up this scenario, you will need to do the following on the CLI.

```bash
export SCENARIO=scenarios/vault_ha.yml
```

Next follow the steps in [Spin Up - Scenario](#spin-up-scenario)

### Spin Up - Scenario

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

### Vault Validation

Now that we've validated our Consul cluster, let's now take a look
at Vault.

> NOTE: Vault is in an unsealed state at the time of spinning up.

As part of the provisioning, our Vault keys are stored on each of the
Vault servers (Not the safest, but...). So, let's see how we can get access
to these.

Let's SSH into any one of our Vault servers using `vagrant ssh vault[01-03]`.

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

```bash
    "retry_join": [
        "192.168.250.11",
        "192.168.250.12",
        "192.168.250.13"
    ],
```

## Halting Environment

There will likely come a time where you want to halt the whole environment to
take a break or free up some resources. To do this and not have to rebuild the
whole stack again, simply:

```bash
vagrant halt
...
▶ vagrant halt
==> lb01: Attempting graceful shutdown of VM...
==> app03: Attempting graceful shutdown of VM...
==> app02: Attempting graceful shutdown of VM...
==> app01: Attempting graceful shutdown of VM...
==> vault03: Attempting graceful shutdown of VM...
==> vault02: Attempting graceful shutdown of VM...
==> vault01: Attempting graceful shutdown of VM...
==> consul03: Attempting graceful shutdown of VM...
==> consul02: Attempting graceful shutdown of VM...
==> consul01: Attempting graceful shutdown of VM...
(venv)
```

And when you are ready to resume where you left off, simply:

```bash
vagrant up
...
▶ vagrant up
Bringing machine 'consul01' up with 'virtualbox' provider...
Bringing machine 'consul02' up with 'virtualbox' provider...
Bringing machine 'consul03' up with 'virtualbox' provider...
Bringing machine 'vault01' up with 'virtualbox' provider...
Bringing machine 'vault02' up with 'virtualbox' provider...
Bringing machine 'vault03' up with 'virtualbox' provider...
Bringing machine 'app01' up with 'virtualbox' provider...
Bringing machine 'app02' up with 'virtualbox' provider...
Bringing machine 'app03' up with 'virtualbox' provider...
Bringing machine 'lb01' up with 'virtualbox' provider...
```

Once everything is back up and running, you can jump over to one of the Consul
servers UI to check quickly on the status of things.
[http://192.168.250.11:8500/ui/dc1/nodes](http://192.168.250.11:8500/ui/dc1/nodes)

![Consul Node Status](.images/2020-07-18-00-12-30.png)

From the screenshot above, you'll notice that our Vault servers are in a
sealed state. This is because when Vault starts on boot, it is automatically
sealed. So, to unseal the Vault, we can run our [playbooks/vault.yml](playbooks/vault.yml)
Ansible playbook. This playbook will execute our Vault Ansible role again, and
at the very end it will unseal it.

So, let's do that real quick.

```bash
ansible-playbook -i hosts playbooks/vault.yml
...


PLAY [consul_servers] ****************************************************************************************************************************************

TASK [Gathering Facts] ***************************************************************************************************************************************
[DEPRECATION WARNING]: Distribution Ubuntu 18.04 on host consul01 should use /usr/bin/python3, but is using /usr/bin/python for backward compatibility with
prior Ansible releases. A future Ansible release will default to using the discovered platform python for this host. See
https://docs.ansible.com/ansible/2.9/reference_appendices/interpreter_discovery.html for more information. This feature will be removed in version 2.12.
Deprecation warnings can be disabled by setting deprecation_warnings=False in ansible.cfg.
ok: [consul01]
[DEPRECATION WARNING]: Distribution Ubuntu 18.04 on host consul02 should use /usr/bin/python3, but is using /usr/bin/python for backward compatibility with
prior Ansible releases. A future Ansible release will default to using the discovered platform python for this host. See
https://docs.ansible.com/ansible/2.9/reference_appendices/interpreter_discovery.html for more information. This feature will be removed in version 2.12.
Deprecation warnings can be disabled by setting deprecation_warnings=False in ansible.cfg.
ok: [consul02]
[DEPRECATION WARNING]: Distribution Ubuntu 18.04 on host consul03 should use /usr/bin/python3, but is using /usr/bin/python for backward compatibility with
prior Ansible releases. A future Ansible release will default to using the discovered platform python for this host. See
https://docs.ansible.com/ansible/2.9/reference_appendices/interpreter_discovery.html for more information. This feature will be removed in version 2.12.
Deprecation warnings can be disabled by setting deprecation_warnings=False in ansible.cfg.
ok: [consul03]

PLAY [vault_servers] *****************************************************************************************************************************************

TASK [Gathering Facts] ***************************************************************************************************************************************
[DEPRECATION WARNING]: Distribution Ubuntu 18.04 on host vault01 should use /usr/bin/python3, but is using /usr/bin/python for backward compatibility with
prior Ansible releases. A future Ansible release will default to using the discovered platform python for this host. See
https://docs.ansible.com/ansible/2.9/reference_appendices/interpreter_discovery.html for more information. This feature will be removed in version 2.12.
Deprecation warnings can be disabled by setting deprecation_warnings=False in ansible.cfg.
ok: [vault01]
[DEPRECATION WARNING]: Distribution Ubuntu 18.04 on host vault02 should use /usr/bin/python3, but is using /usr/bin/python for backward compatibility with
prior Ansible releases. A future Ansible release will default to using the discovered platform python for this host. See
https://docs.ansible.com/ansible/2.9/reference_appendices/interpreter_discovery.html for more information. This feature will be removed in version 2.12.
Deprecation warnings can be disabled by setting deprecation_warnings=False in ansible.cfg.
ok: [vault02]
[DEPRECATION WARNING]: Distribution Ubuntu 18.04 on host vault03 should use /usr/bin/python3, but is using /usr/bin/python for backward compatibility with
prior Ansible releases. A future Ansible release will default to using the discovered platform python for this host. See
https://docs.ansible.com/ansible/2.9/reference_appendices/interpreter_discovery.html for more information. This feature will be removed in version 2.12.
Deprecation warnings can be disabled by setting deprecation_warnings=False in ansible.cfg.
ok: [vault03]
```

```bash
PLAY RECAP ***************************************************************************************************************************************************
consul01                   : ok=1    changed=0    unreachable=0    failed=0    skipped=0    rescued=0    ignored=0
consul02                   : ok=1    changed=0    unreachable=0    failed=0    skipped=0    rescued=0    ignored=0
consul03                   : ok=1    changed=0    unreachable=0    failed=0    skipped=0    rescued=0    ignored=0
vault01                    : ok=45   changed=3    unreachable=0    failed=0    skipped=17   rescued=0    ignored=0
vault02                    : ok=44   changed=3    unreachable=0    failed=0    skipped=18   rescued=0    ignored=0
vault03                    : ok=44   changed=3    unreachable=0    failed=0    skipped=18   rescued=0    ignored=0
```

And when this completes, take a look at our [http://192.168.250.11:8500/ui/dc1/nodes](http://192.168.250.11:8500/ui/dc1/nodes) once again.

![Consul Node Status](.images/2020-07-18-00-20-12.png)

And we're good to go again!

## License

MIT

## Author Information

Larry Smith Jr.

- [@mrlesmithjr](https://twitter.com/mrlesmithjr)
- [mrlesmithjr@gmail.com](mailto:mrlesmithjr@gmail.com)
- [http://everythingshouldbevirtual.com](http://everythingshouldbevirtual.com)
