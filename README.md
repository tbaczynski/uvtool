# UVTool

## users
(current) in `uvtool_users` we define files with ssh keys not keys itself. In templates this content of this files will be readed and included in user-data.

## network
if you want to setup static IPs , you can create file `{{ vm.name }}.interfaces` in `./files` directory. This file (if exist) will be included in metadata template.  
Example is in `files/example_vm.interfaces`.

## how to use
0. pre-requirements
- install libvirt
- configure bridge interface

1. add role
```yaml
- name: virt host
  hosts: srv
  become: yes
  become_user: root
  become_method: sudo
  roles:
  - uvtool
```
2. define users & VMs
```yaml
uvtool_users:
  - name: user
    comment: 'Jon Smith'
    keys:
      - '~/.ssh/id_rsa.pub'
    password: $6$xxxxxxxxxxxxxxxxxxxxxxxxxxxxx
uvtool_vms:
  - name: 'vm1'
    release: 'bionic'
    cpu: '1'
    mem: '1024'
    disk: '10'
    bridge: 'br0'
```
3. sync images
you can wait till sync can happen by cron job or do this manually

```sh
# uvt-simplestreams-libvirt sync release=bionic arch=amd64
# uvt-simplestreams-libvirt sync release=xenial arch=amd64
```
4. create VMs by `{{ uvtool_dir }}/create.{{ vm.name}}.sh`


## issues
- on first boot with dhcp cfg VM registered (in dhcp) as `ubuntu.{{ uvtool_zone }}`  
workaround on bionic: 
```yaml
uvtool_runcmd:
  - 'netplan generate && netplan apply'
```
workaround on xenial:  reboot node :-(

