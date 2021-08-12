# MOC Cluster Management Inventory

## Inventory overview

The inventory relies on dynamic groups created by the [constructed][]
inventory plugin.  For example, with an inventory file like this in
`inventory/10-dynamic/constructed.yml`:

[constructed]: https://docs.ansible.com/ansible/latest/collections/ansible/builtin/constructed_inventory.html

```
plugin: constructed
strict: false
groups:
  ipmi_hosts: ipmi_host|default(false)
keyed_groups:
  - prefix: ""
    separator: ""
    key: moc_role
```

And a cluster inventory in `inventory/00-static/cluster1.yml`:

```
all:
  children:
    cluster1:
      children:
        cluster1_controller:
          vars:
            moc_role: openshift_controller
          hosts:
            host1:
              ipmi_host: 10.8.0.1
            host2:
              ipmi_host: 10.8.0.2
            host3:
              ipmi_host: 10.8.0.3
        cluster1_compute:
          vars:
            moc_role: openshift_compute
          hosts:
            host4:
              ipmi_host: 10.8.0.4
            host5:
              ipmi_host: 10.8.0.5
            host6:
              ipmi_host: 10.8.0.6
    cluster2:
      children:
        cluster2_controller:
          vars:
            moc_role: openshift_controller
          hosts:
            host1:
              ipmi_host: 10.9.0.1
            host2:
              ipmi_host: 10.9.0.2
            host3:
              ipmi_host: 10.9.0.3
        cluster2_compute:
          vars:
            moc_role: openshift_compute
          hosts:
            host4:
              ipmi_host: 10.9.0.4
            host5:
              ipmi_host: 10.9.0.5
            host6:
              ipmi_host: 10.9.0.6
```

Then:

- We can refer to all openshift controllers using the group
  `openshift_controller`.

- We can refer to controllers in cluster1 with the pattern
  `cluster1:&openshift_controller` (the `&` is the intersection
  operator, so that pattern means "hosts that are members of both
  `cluster1` and `openshift_controller`).

- We can refer to all hosts for which we have an `ipmi_host` using the
  group `ipmi_hosts`

Etc.
