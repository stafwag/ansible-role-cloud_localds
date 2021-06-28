# Ansible Role: cloud_localds

An ansible role to create cloud-init config disk images.
This role is a wrapper around the ```cloud-localds``` command.

## Requirements

cloud-localds

## Role Variables

### Playbook related variables

* **cloud_localds**: "namespace"
  * **dest**: The destination image
  * **hostname**:  The hostname
  * **dir**: optional default: ```/var/lib/libvirt/images```. The destination directory when **dest** is not defined.
  * **config**: The user-data configuration
  * **network_config**: The network configuration
  * **owner**: uid default 0  The file owner of the destination image
  * **group**: gid default 0  The file group owner of the destination image 
  * **mode**:  mode default '0400'  The permissions of the destination image

The role creates an iso disk image with the cloud-init configuration
When  ```cloud_localds.dest``` is defined the following files are created:

* **{{ cloud_localds.dest }}**_config.yml the cloud-init user-data
* **{{ cloud_localds.dest }}**_net_config.yml the cloud-init network config ( if network_config is defined)
* **{{ cloud_localds.dest }}** the iso image with the cloud-init configuration.

When **cloud_localds.dest** is not defined **cloud_localds.hostname** needs to be
defined. In this case, the following files will be created:

* **{{ cloud_localds.dir }}**/**{{ cloud_localdds.hostname }}**_config.yml the cloud-init user-data 
* **{{ cloud_localds.dir }}**/**{{ cloud_localdds.hostname }}**_net_config.yml cloud-init network config ( if network_config is defined)
* **{{ cloud_localds.dir }}**/**{{ cloud_localdds.hostname }}**_cloud-init.iso: the iso image with the cloud-init configuration.

## Dependencies

None

## Example Playbooks

### Create a cloud-init iso with the dest defined
 
```
- name: Create config.iso
  gather_facts: no 
  become: true
  hosts: localhost
  roles:
    - role: stafwag.cloud_localds
      vars:
        cloud_localds:
          dest: /var/lib/libvirt/images/tstdebian_cloudinit.iso
          config: "{{ lookup('template','files/mytstdebian.j2') }}"
          network_config: "{{ lookup('template','files/mytstdebian.j2') }}"
```

### Create a cloud-init iso with the hostname defined 

```
---
- name: Create config.iso
  gather_facts: no 
  become: true
  hosts: localhost
  roles:
    - role: stafwag.cloud_localds
      vars:
        cloud_localds:
          hostname: tstdebian 
          config: "{{ lookup('template','files/mytstdebian.j2') }}"
          network_config: "{{ lookup('template','files/mytstdebian.j2') }}"
```

## License

MIT/BSD

## Author Information

Created by Staf Wagemakers, email: staf@wagemakers.be, website: http://www.wagemakers.be.
