# Ansible Role: cloud_localds

An ansible role to create cloud-init config disk images.
This role is a wrapper around the ```cloud-localds``` command.

## Requirements

```cloud-localds```

### Supported GNU/Linux Distributions

It should work on most GNU/Linux distributions.

On Linux distributions that have the ```cloud-localds``` package available the ```cloud-localds``` command is used to create an iso image with the
cloud-init configuration.

On Distributions that don't provide the ```cloud-localds```, the ```xorriso``` command is used to create the iso image.

On FreeBSD is the ```xorriso``` provider is used.

The role is tested on the operating systems/distributions below.

* Archlinux
* Debian
* Centos 7 ( cloud_localds provider )
* Centos 8 & 9 ( xorriso provider )
* RedHat 7 ( cloud_localds provider )
* RedHat 8 & 9 ( xorriso provider )
* Ubuntu
* FreeBSD (xorriso provider)

If your operating system is not listed you can install ```cloud-localds``` or ```xorriso``` manually and set the ```provider``` variable.

### Installation

#### Ansible galaxy

The role is available on [Ansible Galaxy](https://galaxy.ansible.com/ui/standalone/roles/stafwag/cloud_localds/).

To install the role from Ansible Galaxy execute the command below.

```bash
$ ansible-galaxy role install stafwag.cloud_localds
```

## Source Code

If you want to use the source code directly.

Clone the role source code.

```bash
$ git clone https://github.com/stafwag/ansible-role-cloud_localds stafwag.cloud_localds
```

and put into the [role search path](https://docs.ansible.com/ansible/2.4/playbooks_reuse_roles.html#role-search-path)

## Role tasks, tags, variables and templates

### Tasks

* **install**

    All installation-related tasks are defined in the ```install``` playbook. This allows you to install the
    required packages and start/enable the required service with ```tasks_from``` in the ```include_role```,
    ```import_role```, … ansible modules.

    See example below.

### Tags

* **install**

  Install the required packages.

### Playbook related variables

* **cloud_localds**: "namespace"
  * **dest**: The destination image
  * **hostname**:  The hostname
  * **dir**: optional default: ```/var/lib/libvirt/images```. The destination directory when **dest** is not defined.
  * **config**: The user-data configuration
  * **network_config** The network configuration
  * **config_template:** Use an ansible template for the user-data configuration. 
  * **network_config_template:** Use an ansible template for the network configuration.
  * **owner**: uid default 0  The file owner of the destination image
  * **group**: gid default 0  The file group owner of the destination image 
  * **mode**:  mode default '0400'  The permissions of the destination image
  * **overwrite**: boolean default: false Overwrite destination iso if already exists.
  * **provider**: cloud_localds|xorriso default: cloud_localds

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


## Example Playbooks

### Install the cloud-localds package with include_role

```bash
---
- name: Install libvirt & co
  gather_facts: true 
  hosts: all
  become: true
  tasks:
    - name: Install the requirements
      include_role:
        name: "{{ item }}"
        tasks_from:
          install
      with_items:
        - stafwag.libvirt 
        - stafwag.qemu_img
        - stafwag.cloud_localds
      tags:
        - install
```

### Create a cloud-init iso with the dest defined
 
```bash
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

```bash
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

### Use ansible templates 

```bash
---
- name: Create config.iso
  gather_facts: true 
  become: true
  hosts: localhost
  roles:
    - role: stafwag.cloud_localds
      vars:
        cloud_localds:
          hostname: tstdebian 
          config_template: "files/debian/debian.j2"
          network_config_template: "files/debian/debian.j2"
```

## License

MIT/BSD

## Author Information

Created by Staf Wagemakers, email: staf@wagemakers.be, website: http://www.wagemakers.be.
