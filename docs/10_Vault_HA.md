# 10. Vault - HA

In this scenario, we will spin up a three node Consul cluster along with three
Vault servers configured in HA. This scenario is perfect to learn a bit about
how Vault and Consul function together in HA.

## Spinning Up

First we need to export our scenario configuration for Vagrant:

```bash
export SCENARIO=scenarios/vault_ha.yml
```

Now that we've exported our scenario configuration, we are ready to spin up
our environment:

```bash
vagrant up
```

And off we go! You will see a lot going on here as Vagrant and Ansible do their
job.

### Nodes

This scenario has the following nodes when completed.

|   Node   |     IP #1      | IP #2 |
| :------: | :------------: | :---: |
| consul01 | 192.168.250.11 |       |
| consul02 | 192.168.250.12 |       |
| consul03 | 192.168.250.13 |       |
| vault01  | 192.168.250.21 |       |
| vault02  | 192.168.250.22 |       |
| vault03  | 192.168.250.23 |       |
