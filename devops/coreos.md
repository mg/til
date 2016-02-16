# CoreOS
*Source material: [1](https://deis.com/blog/2016/coreos-overview-p1/)*, [2](https://deis.com/blog/2016/coreos-overview-p2), [3](https://deis.com/blog/2016/coreos-overview-p3)

CoreOS uses containers over packages to manage applications. *systemd* is used to manage lifecycle of containers.
### OS Updates
CoreOS uses an *active/passive dual partition* scheme to boot and update. If the OS boots on paritition **A** it will use partition **B** to install a new version and then switch to partition **B** on next restart.

Strategy can be one of ``best-effort``, ``etcd-lock``, ``reboot``, or ``off``; and is defined in ``/etc/coreos/cloud-config``:
```yaml
#cloud-config
coreos:
  update:
    reboot-strategy: best-effort
```
### Release channels
Current release channel is defined in ``/etc/coreos/update.conf`` and can be one of ``alpha``, ``beta``, or ``stable``.
```
GROUP=stable
```
After changing the channel run ``sudo systemctl restart update-engine`` to restart update engine.
### Cluster discovery

### cloud-config
Configuration for the bootstrap mechanic in CoreOS, *coreos-cloudinit*. A *YAML* formatted file (use <https://coreos.com/validate/> to validate) that must start with the line ``#cloud-config``. Frequently used sections are
- ``coreos`` for handling CoreOS specifics like ``update`` reboot strategy, ``etcd2``, ``fleet``, ``fannel``, ``units`` etc.
- ``write_files`` defines a set of files to create on the local system.
- ``ssh_authorized_keys`` adds public SSH keys to authorize the core user.

See more at <https://coreos.com/os/docs/latest/cloud-config.html>.
### etcd
A distributed key-value store used by CoreOS and Kubernetes to share config data across a cluster. Optimal size of an etcd cluster is an odd number. <https://discovery.etcd.io> provides a free discovery service to set up a cluster, e.g.
```yaml
coreos:
  etcd2:
    # generate a new token for each unique cluster from https://discovery.etcd.io/new
    discovery: https://discovery.etcd.io/60887e46255f4efew37077ccf12b5f06a54a
    initial-advertise-peer-urls: http://$private_ipv4:2380
    # listen on both the official ports and the legacy ports
    # legacy ports can be omitted if your application doesn't depend on them
    listen-client-urls: http://0.0.0.0:2379,http://0.0.0.0:4001
    listen-peer-urls: http://$private_ipv4:2380,http://$private_ipv4:7001
    data-dir: /var/lib/etcd2
```

For a worker machine (not a service machine) we can start *etcd* in a proxy mode with ``proxy: on``.
### Units
*systemd* uses *unit* files to manage services
```ini
[Unit]
Description=HelloWorldApp
After=docker.service
Requires=docker.service

[Service]
TimeoutStartSec=0
ExecStartPre=-/usr/bin/docker rm busybox1
ExecStartPre=/usr/bin/docker pull busybox
ExecStart=/usr/bin/docker run --rm --name busybox1 busybox /bin/sh -c "while true; do echo Hello World; sleep 1; done"
ExecStop=/usr/bin/docker stop busybox1

[Install]
WantedBy=multi-user.target
```
To tell *systemd* to start a service and see the output, use:
```
sudo systemctl enable /etc/systemd/system/hello.service
sudo systemctl start hello.service
journalctl -f -u hello.service
```
### Fleet
Controls *systemd* at the cluster level.
```
export FLEETCTL_TUNNEL=remote_machines_ip
fleetctl start hello_world.service
fleetctl status hello_world.service
fleetctl stop hello_world.service
fleetctl cat hello_world.service
fleetctl submit hello_world.service
fleetctl journal hello_world.service
fleetctl ssh hello_world.service
fleetctl list-units
fleetctl list-machines
```
Fleet unit files are like *systemd* unit files with additional section ``[X-Fleet]``:
- ``MachineID``: schedule unit on macine with *id*
- ``MachineOf``: limit to machines that host a specific unit
- ``MachineMetadata``: limit machines to metadata
- ``Conflicts``: to prevent conflicts between units
- ``Global``: schedule on all machines in cluster
