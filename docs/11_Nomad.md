# 11. Nomad - Single Node

In this scenario, we will simply spin up a single node Nomad server which will
provide us a learning environment to get familiar with basic Nomad concepts.

## Spinning Up

First we need to export our scenario configuration for Vagrant:

```bash
export SCENARIO=scenarios/nomad.yml
```

Now that we've exported our scenario configuration, we are ready to spin up
our environment:

```bash
vagrant up
```

And off we go! You will see a lot going on here as Vagrant and Ansible do their
job.

```bash
❯ vagrant up
Bringing machine 'nomad01' up with 'virtualbox' provider...
...
PLAY RECAP *********************************************************************
nomad01                    : ok=20   changed=10   unreachable=0    failed=0    skipped=10   rescued=0    ignored=0
```

### Nodes

This scenario has the following nodes when completed.

|  Node   |     IP #1      | IP #2 |
| :-----: | :------------: | :---: |
| nomad01 | 192.168.250.41 |       |

## CLI

Now we are ready to begin exploring our Nomad server using the CLI. So, to do
this. Let's SSH into our `nomad01` node.

```bash
vagrant ssh nomad01
```

```bash
❯ vagrant ssh nomad01
Welcome to Ubuntu 18.04.5 LTS (GNU/Linux 4.15.0-112-generic x86_64)

 * Documentation:  https://help.ubuntu.com
 * Management:     https://landscape.canonical.com
 * Support:        https://ubuntu.com/advantage

New release '20.04.1 LTS' available.
Run 'do-release-upgrade' to upgrade to it.

Last login: Mon Oct 26 20:41:54 2020 from 10.0.2.2
```

Now that we've logged in, let's change into the `/etc/nomad.d` directory and
see what files/directories are present.

```bash
cd /etc/nomad.d
ls -la
```

```bash
vagrant@nomad01:~$ cd /etc/nomad.d
vagrant@nomad01:/etc/nomad.d$ ls -la
total 12
drwxr-xr-x  2 root root 4096 Oct 26 20:43 .
drwxr-xr-x 77 root root 4096 Oct 26 20:43 ..
-rw-r--r--  1 root root  703 Oct 26 20:43 nomad.json
```

### CLI - Configuration

Let's first take a quick look at our Nomad configuration while we're here.

```bash
cat nomad.json
```

```bash
vagrant@nomad01:/etc/nomad.d$ cat nomad.json
{
    "advertise": {
        "http": "192.168.250.41",
        "rpc": "192.168.250.41",
        "serf": "192.168.250.41"
    },
    "bind_addr": "0.0.0.0",
    "client": {
        "enabled": true,
        "servers": [
            "127.0.0.1:4646"
        ]
    },
    "consul": {
        "address": "127.0.0.1:8500",
        "auto_advertise": true,
        "client_auto_join": true,
        "client_service_name": "nomad-client",
        "server_auto_join": true,
        "server_service_name": "nomad"
    },
    "data_dir": "/opt/nomad/data",
    "datacenter": "dc1",
    "enable_syslog": true,
    "server": {
        "bootstrap_expect": 1,
        "enabled": true,
        "server_join": {}
    }
}
```

### CLI - Agent Info

We can get information about our Nomad agent by executing the following:

```bash
nomad agent-info
```

```bash
vagrant@nomad01:~$ nomad agent-info
client
  heartbeat_ttl = 13.745464535s
  known_servers = 192.168.250.41:4647
  last_heartbeat = 2.450598719s
  node_id = ef1b0bcf-e50f-1669-bbf5-fcda3096296e
  num_allocations = 0
nomad
  bootstrap = true
  known_regions = 1
  leader = true
  leader_addr = 192.168.250.41:4647
  server = true
raft
  applied_index = 25
  commit_index = 25
  fsm_pending = 0
  last_contact = 0
  last_log_index = 25
  last_log_term = 2
  last_snapshot_index = 0
  last_snapshot_term = 0
  latest_configuration = [{Suffrage:Voter ID:192.168.250.41:4647 Address:192.168.250.41:4647}]
  latest_configuration_index = 0
  num_peers = 0
  protocol_version = 2
  protocol_version_max = 3
  protocol_version_min = 0
  snapshot_version_max = 1
  snapshot_version_min = 0
  state = Leader
  term = 2
runtime
  arch = amd64
  cpu_count = 1
  goroutines = 130
  kernel.name = linux
  max_procs = 1
  version = go1.14.7
serf
  coordinate_resets = 0
  encrypted = false
  event_queue = 0
  event_time = 1
  failed = 0
  health_score = 0
  intent_queue = 0
  left = 0
  member_time = 1
  members = 1
  query_queue = 0
  query_time = 1
vault
  token_expire_time =
  token_ttl = 0s
  tracked_for_revoked = 0
```

From the above output we get the following subsystem information:

| Subsystem | Description                                          |
| :-------- | :--------------------------------------------------- |
| client    | Status of the local Nomad client                     |
| nomad     | Status of the local Nomad server                     |
| serf      | Gossip protocol metrics and information              |
| raft      | Status information about the Raft consensus protocol |
| runtime   | Various metrics from the runtime environment         |

### CLI - Node Info

Let's first view our registered Nomad nodes:

```bash
nomad node status
```

```bash
vagrant@nomad01:~$ nomad node status
ID        DC   Name     Class   Drain  Eligibility  Status
ef1b0bcf  dc1  nomad01  <none>  false  eligible     ready
```

As you can see from the output above, we only have a single node. We will get
a better view of a true Nomad cluster when we get to our Nomad cluster scenario.

But from the output we can get a view of the following:

| Field       | Value    | Description                              |
| :---------- | :------- | :--------------------------------------- |
| ID          | ef1b0bcf | Random generated UUID                    |
| DC          | dc1      | Datacenter the node is registered within |
| Name        | nomad01  | Name of registered node                  |
| Class       | none     |                                          |
| Drain       | false    |                                          |
| Eligibility | eligible |                                          |
| Status      | ready    |                                          |

To get a list of members which are functioning servers, also part of the gossip
ring:

> NOTE: A gossip ring is comprised of all nodes running a server agent and are
> participating in the Gossip Protocol.

```bash
nomad server members
```

```bash
vagrant@nomad01:~$ nomad server members
Name            Address         Port  Status  Leader  Protocol  Build   Datacenter  Region
nomad01.global  192.168.250.41  4648  alive   true    2         0.12.3  dc1         global
```

You can also get a more detailed view of our server members:

```bash
nomad server members -detailed
```

```bash
vagrant@nomad01:~$ nomad server members -detailed
Name            Address         Port  Tags
nomad01.global  192.168.250.41  4648  raft_vsn=2,vsn=1,id=f98d1f77-c462-c91f-7ad8-de4b49b0052b,bootstrap=1,port=4647,role=nomad,dc=dc1,expect=1,region=global,mvn=1,build=0.12.3,rpc_addr=192.168.250.41
```

We can also obtain the node status of the current node we are logged into. Which
will give us a good bit of information about our node. We can do this by adding
the `-self` parameter to the `nomad node status` command:

```bash
nomad node status -self
```

```bash
vagrant@nomad01:~$ nomad node status -self
ID              = ef1b0bcf-e50f-1669-bbf5-fcda3096296e
Name            = nomad01
Class           = <none>
DC              = dc1
Drain           = false
Eligibility     = eligible
Status          = ready
CSI Controllers = <none>
CSI Drivers     = <none>
Uptime          = 2m54s
Host Volumes    = <none>
CSI Volumes     = <none>
Driver Status   = docker,exec

Node Events
Time                  Subsystem  Message
2020-10-27T02:14:01Z  Cluster    Node registered

Allocated Resources
CPU         Memory       Disk
0/2494 MHz  0 B/985 MiB  0 B/28 GiB

Allocation Resource Utilization
CPU         Memory
0/2494 MHz  0 B/985 MiB

Host Resource Utilization
CPU         Memory           Disk
0/2494 MHz  150 MiB/985 MiB  2.5 GiB/32 GiB

Allocations
No allocations placed
```

If we'd like to also include some stats along with our node status we can add
the `-stats` parameter to the `nomad node status -self` command:

```bash
nomad node status -self -stats
```

```bash
vagrant@nomad01:~$ nomad node status -self -stats
ID              = ef1b0bcf-e50f-1669-bbf5-fcda3096296e
Name            = nomad01
Class           = <none>
DC              = dc1
Drain           = false
Eligibility     = eligible
Status          = ready
CSI Controllers = <none>
CSI Drivers     = <none>
Uptime          = 10m13s
Host Volumes    = <none>
CSI Volumes     = <none>
Driver Status   = docker,exec

Node Events
Time                  Subsystem  Message
2020-10-27T02:14:01Z  Cluster    Node registered

Allocated Resources
CPU         Memory       Disk
0/2494 MHz  0 B/985 MiB  0 B/28 GiB

Allocation Resource Utilization
CPU         Memory
0/2494 MHz  0 B/985 MiB

Host Resource Utilization
CPU         Memory           Disk
0/2494 MHz  149 MiB/985 MiB  2.5 GiB/32 GiB

CPU Stats
CPU    = cpu0
User   = 0.00%
System = 0.00%
Idle   = 100.00%

Memory Stats
Total     = 985 MiB
Available = 697 MiB
Used      = 149 MiB
Free      = 246 MiB

Disk Stats
Device         = /dev/sda3
MountPoint     = /
Size           = 32 GiB
Used           = 2.5 GiB
Available      = 28 GiB
Used Percent   = 8.20%
Inodes Percent = 3.46%

Device         = /dev/sda1
MountPoint     = /boot
Size           = 945 MiB
Used           = 59 MiB
Available      = 820 MiB
Used Percent   = 6.75%
Inodes Percent = 0.49%

Allocations
No allocations placed
```

Now let's get a bit more verbose output from our node status info. We can do
this by adding the `-verbose` parameter to our `nomad node status -self` command:

```bash
nomad node status -self -verbose
```

```bash
vagrant@nomad01:~$ nomad node status -self -verbose
ID              = ef1b0bcf-e50f-1669-bbf5-fcda3096296e
Name            = nomad01
Class           = <none>
DC              = dc1
Drain           = false
Eligibility     = eligible
Status          = ready
CSI Controllers = <none>
CSI Drivers     = <none>
Uptime          = 16m6s

Drivers
Driver    Detected  Healthy  Message   Time
docker    true      true     Healthy   2020-10-27T02:13:39Z
exec      true      true     Healthy   2020-10-27T02:13:39Z
java      false     false    <none>    2020-10-27T02:13:39Z
qemu      false     false    <none>    2020-10-27T02:13:39Z
raw_exec  false     false    disabled  2020-10-27T02:13:39Z

Node Events
Time                  Subsystem  Message          Details
2020-10-27T02:14:01Z  Cluster    Node registered  <none>

Allocated Resources
CPU         Memory       Disk
0/2494 MHz  0 B/985 MiB  0 B/28 GiB

Allocation Resource Utilization
CPU         Memory
0/2494 MHz  0 B/985 MiB

Host Resource Utilization
CPU         Memory           Disk
0/2494 MHz  149 MiB/985 MiB  2.5 GiB/32 GiB

Allocations
No allocations placed

Attributes
cpu.arch                  = amd64
cpu.frequency             = 2494
cpu.modelname             = Intel(R) Core(TM) i7-4870HQ CPU @ 2.50GHz
cpu.numcores              = 1
cpu.totalcompute          = 2494
driver.docker             = 1
driver.docker.os_type     = linux
driver.docker.runtimes    = runc
driver.docker.version     = 19.03.13
driver.exec               = 1
kernel.name               = linux
kernel.version            = 4.15.0-112-generic
memory.totalbytes         = 1032994816
nomad.advertise.address   = 192.168.250.41:4646
nomad.revision            = 2db8abd9620dd41cb7bfe399551ba0f7824b3f61
nomad.version             = 0.12.3
os.name                   = ubuntu
os.signals                = SIGTRAP,SIGHUP,SIGSYS,SIGTTOU,SIGIO,SIGTERM,SIGTSTP,SIGUSR1,SIGALRM,SIGPROF,SIGTTIN,SIGURG,SIGUSR2,SIGWINCH,SIGXCPU,SIGCONT,SIGIOT,SIGPIPE,SIGSEGV,SIGBUS,SIGINT,SIGXFSZ,SIGABRT,SIGQUIT,SIGSTOP,SIGCHLD,SIGFPE,SIGILL,SIGKILL
os.version                = 18.04
unique.cgroup.mountpoint  = /sys/fs/cgroup
unique.hostname           = nomad01
unique.network.ip-address = 10.0.2.15
unique.storage.bytesfree  = 30253703168
unique.storage.bytestotal = 34753667072
unique.storage.volume     = /dev/sda3

Meta
connect.log_level     = info
connect.sidecar_image = envoyproxy/envoy:v1.11.2@sha256:a7769160c9c1a55bb8d07a3b71ce5d64f72b1f665f10d81aa1581bc3cf850d09
```
