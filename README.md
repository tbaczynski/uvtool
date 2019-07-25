# UVTool

## users
Exist two different variable layouts for users, based on dictinary or hash, default is hash in `uvtool_users_hash` variable. 
To use dictinary vars, you must to define users in `uvtool_users_dict` and set `uvtool_users_vars: dict`

in `uvtool_users_hash` we define files with ssh keys not keys itself. In templates this content of this files will be readed and included in user-data.

## vm definition
- standard VM, example:
```yaml
uvtool_vms:
  - name: 'vm1'
    release: 'bionic'
    cpu: '1'
    mem: '1024'
    disk: '10'
    bridge: 'br0'
```

- if you want to setup static IPs , you need to define vars for this.  
example cfg for vm with static IPs:
```yaml
uvtool_vms:
  - name: 'vm3'
    release: 'bionic'
    bridge: 'br0'
    interfaces:
      device: 'ens3'
      inet: 'static'  # static, dhcp
      address: '192.168.55.101/23'
      gw: '192.168.55.1'
      ns:
				- '8.8.8.8'
				- '8.8.4.4'
```

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

