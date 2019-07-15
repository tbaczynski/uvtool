# UVTool

## users
Exist two different variable layouts for users, based on dictinary or hash, default is hash in `uvtool_users_hash` variable. 
To use dictinary vars, you must to define users in `uvtool_users_dict` and set `uvtool_users_vars: dict`

in `uvtool_users_hash` we define files with ssh keys not keys itself. In templates this content of this files will be readed and included in user-data.

## network
if you want to setup static IPs , you can create file `{{ vm.name }}.interfaces` in `./files` directory. This file (if exist) will be included in metadata template.  
Example is in `files/example_vm.interfaces`.

## how to use
0. pre-requirements
- install libvirt
- configure bridge interface

1. add role
```yaml
  roles:
  - name: uvtool
```
2. define users & VMs (based on examples in `defaults/main.yml`)
3. sync images manually (or you can wait till sync can happen by cron job) based on commant in `/etc/cron.d/uvt_sync_ubuntu_img`.
4. create VMs by `{{ uvtool_dir }}/create.{{ vm.name}}.sh`


## issues
1. on first boot with dhcp cfg VM registered (in dhcp) as `ubuntu.{{ uvtool_zone }}`  
workaround on bionic: 
```yaml
uvtool_runcmd:
  - 'netplan generate && netplan apply'
```
workaround on xenial:  reboot node :-(

2. uid (when you use `uvtool_users_dict`) is not setting up.

