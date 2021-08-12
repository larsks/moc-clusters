Some notes on iDRAC access:

## IPMI Access

Some of these playbooks assume that you can access `kzn-ipmi-gw`. For
example, the `delegate_to` directive in a task like this instructs
Ansible to connect to the `ipmi_proxy` host (defined in your
inventory) and run the `ipmitool` command:

```
- delegate_to: ipmi_proxy
  command: >-
    ipmitool
    -I lanplus
    -H {{ ipmi_host }}
    -U {{ ipmi_username }}
    -P {{ ipmi_password }}
    power {{ power_action }}
  register: power
```

## iDRAC Access

Some of these playbooks interact with `racadm` and assume that you can
directly ssh to the iDRAC addresses.  For example, in a task like
this, the `delegate_to` directive instructs Ansible to ssh to the
address stored in the `ipmi_host` variable:


```
- name: ensure system is powered off
  tags: [power]
  delegate_to: "{{ ipmi_host }}"
  raw: racadm serveraction powerdown
  register: res
  failed_when: >-
    "error" in res.stdout.lower()
```

This assumes that hosts in your inventory have an `ipmi_host` defined.
