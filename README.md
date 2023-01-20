ansible-role-routed-lb
======================

This will deploy an advanced Load-Balancer capable of managing routed VIPs with FRR (using BGP) and load-balance traffic with HAproxy.

Requirements
------------

For now, we test this module on CentOS 9 Stream.

Role Variables
--------------

Only `configs` needs to be set, as a dictionary:

```
configs:
  lb1:
    bgp_asn: <BGP ASN>
    bgp_neighbors:
      - ip: <IP of a BGP router>
        password: <BGP password>
    services:
      - name: <Service name (e.g. api)>
        vips:
          - <VIPs that are used to reach that service in frontend>
        min_backends: <Minimum amount of healthy backends to be alive so the VIPs are created for that service>
        healthcheck: <HAproxy backend command>
        balance: <LB algorithm>
        frontend_port: <HAproxy frontend port for the service>
        haproxy_monitor_port: <HAproxy monitoring port for the service>
        backend_opts: <HAproxy options for each backend>
        backend_port: <HAproxy backend port>
        backend_hosts:
          - name: <hostname of the backend>
            ip: <IP of the backend>
```

Have a look at `tests/vars.yml` for a complete example.

Dependencies
------------

None.

Example Playbook
----------------

Create a file named `playbook.yml`:
```
---
- hosts: lb1
  vars:
    config: lb1
  tasks:
    - name: Include vars for testing
      ansible.builtin.include_vars: vars.yml
    - name: Run the role
      include_role:
        name: routed-lb
```

And then create a file named `inventory` for the Ansible inventory:
```
all:
  hosts:
    lb1:
      ansible_host: 192.168.10.2 # IP address of your Load-Balancer
      ansible_user: cloud-user
      ansible_become: true
```

Then run:
```
ansible-playbook playbook.yml -i inventory
```

Your load-balancers should be up and running!

License
-------

Apache-2.0
