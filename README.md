## Ansible EVPN/VXLAN Demo
### Lab Topology
![alt text](https://github.com/autopsrv/ansible_docker-topo/blob/main/topo.jpeg?raw=true)

> Note: use [docker-topo](https://github.com/networkop/docker-topo/) to build lab topology.
### Laptop Environment:
```
# python3 -V
Python 3.7.10
```
```
# pip3 install ansible
```
```
# ansible --version
ansible [core 2.11.2] 
  config file = None
  configured module search path = ['/root/.ansible/plugins/modules', '/usr/share/ansible/plugins/modules']
  ansible python module location = /usr/local/lib/python3.7/dist-packages/ansible
  ansible collection location = /root/.ansible/collections:/usr/share/ansible/collections
  executable location = /usr/local/bin/ansible
  python version = 3.7.10 (default, Feb 20 2021, 21:21:24) [GCC 5.4.0 20160609]
  jinja version = 3.0.1
  libyaml = True
```
```
# ansible-galaxy collection install arista.eos
# ansible-galaxy collection list | grep arista
arista.eos                    2.1.2
```
### Ansible Configuration files

- [ansible.cfg](https://github.com/autopsrv/ansible_docker-topo/blob/main/ansible.cfg): Ansible configuration file.
- [hosts.ini](https://github.com/autopsrv/ansible_docker-topo/blob/main/hosts.ini): Ansible inventory file.
- [overlay-DCs.yml](https://github.com/autopsrv/ansible_docker-topo/blob/main/vars/overlay-DCs.yml): includes underlay & overlay network variables declared for individual switch, will be loaded by playbook automatically.
- [leaf-overlay.j2](https://github.com/autopsrv/ansible_docker-topo/blob/main/templates/leaf-overlay.j2): a template to generate leaf configuration.
- [borderleaf-overlay.j2](https://github.com/autopsrv/ansible_docker-topo/blob/main/templates/borderleaf-overlay.j2): a template to generate borderleaf configuration.
- [spine-overlay.j2](https://github.com/autopsrv/ansible_docker-topo/blob/main/templates/spine-overlay.j2): a template to generate borderleaf configuration.
- [DCI-overlay.j2](https://github.com/autopsrv/ansible_docker-topo/blob/main/templates/DCI-overlay.j2): a template to generate DCI configuration.
- [lab-reset.j2](https://github.com/autopsrv/ansible_docker-topo/blob/main/templates/lab-reset.j2): a template to generate lab-reset configuration.
- [overlay_config_DCs.yml](https://github.com/autopsrv/ansible_docker-topo/blob/main/playbooks/overlay_config_DCs.yml): a playbook file to configure and deploy EVPN/VXLAN overlay & EBGP underlay networks.
- [lab-reset.yml](https://github.com/autopsrv/ansible_docker-topo/blob/main/playbooks/lab-reset.yml): a playbook file to reset lab environment.
- [backup.yml](https://github.com/autopsrv/ansible_docker-topo/blob/main/playbooks/backup.yml): a playbook file to create a full backup of running-config.
- [add_ssh_key.yml](https://github.com/autopsrv/ansible_docker-topo/blob/main/playbooks/add_ssh_key.yml): a playbook file to set SSH key.
### Ansible for Provisioning
#### EVPN/VXLAN overlay & EBGP underlay networks deployment
```
# ansible-playbook playbooks/overlay_config_DCs.yml 

PLAY [leaf1-DC1,leaf1-DC2] *****************************************************

TASK [Register variables] ******************************************************
Tuesday 20 July 2021  15:52:52 +0800 (0:00:00.018)       0:00:00.018 ********** 
ok: [leaf1-DC2]
ok: [leaf1-DC1]

TASK [Create leaf1-DCs lab-reset config] ***************************************
Tuesday 20 July 2021  15:52:53 +0800 (0:00:01.264)       0:00:01.284 ********** 
ok: [leaf1-DC2]
ok: [leaf1-DC1]

TASK [Upload config] ***********************************************************
Tuesday 20 July 2021  15:52:56 +0800 (0:00:02.914)       0:00:04.199 ********** 
[WARNING]: To ensure idempotency and correct diff the input configuration lines
should be similar to how they appear if present in the running configuration on
device including the indentation
changed: [leaf1-DC1]
changed: [leaf1-DC2]

PLAY [spines] ******************************************************************

TASK [Register variables] ******************************************************
Tuesday 20 July 2021  15:53:03 +0800 (0:00:06.878)       0:00:11.077 ********** 
ok: [spine2-DC2]
ok: [spine1-DC1]
ok: [spine2-DC1]
ok: [spine1-DC2]

TASK [Create Spine config] *****************************************************
Tuesday 20 July 2021  15:53:08 +0800 (0:00:04.913)       0:00:15.992 ********** 
changed: [spine1-DC1]
changed: [spine1-DC2]
changed: [spine2-DC2]
changed: [spine2-DC1]

TASK [Upload config] ***********************************************************
Tuesday 20 July 2021  15:53:13 +0800 (0:00:05.589)       0:00:21.581 ********** 
changed: [spine1-DC1]
changed: [spine2-DC1]
changed: [spine1-DC2]
changed: [spine2-DC2]

PLAY [leafs] *******************************************************************

TASK [Register variables] ******************************************************
Tuesday 20 July 2021  15:53:27 +0800 (0:00:13.584)       0:00:35.167 ********** 
ok: [borderleaf1-DC1]
ok: [leaf2-DC1]
ok: [leaf1-DC1]
ok: [leaf3-DC1]
ok: [leaf4-DC1]

TASK [Create Leaf config] ******************************************************
Tuesday 20 July 2021  15:53:36 +0800 (0:00:08.964)       0:00:44.133 ********** 
changed: [leaf4-DC1]
changed: [leaf1-DC1]
changed: [leaf2-DC1]
changed: [leaf3-DC1]
changed: [borderleaf1-DC1]

TASK [Upload config] ***********************************************************
Tuesday 20 July 2021  15:53:43 +0800 (0:00:07.327)       0:00:51.461 ********** 
changed: [leaf2-DC1]
changed: [leaf3-DC1]
changed: [leaf4-DC1]
changed: [borderleaf1-DC1]
changed: [leaf1-DC1]

PLAY [leafs] *******************************************************************

TASK [Register variables] ******************************************************
Tuesday 20 July 2021  15:54:02 +0800 (0:00:18.587)       0:01:10.052 ********** 
ok: [leaf4-DC2]
ok: [borderleaf1-DC2]
ok: [leaf3-DC2]
ok: [leaf1-DC2]
ok: [leaf2-DC2]

TASK [Create Leaf config] ******************************************************
Tuesday 20 July 2021  15:54:12 +0800 (0:00:09.758)       0:01:19.812 ********** 
changed: [leaf3-DC2]
changed: [leaf4-DC2]
changed: [leaf2-DC2]
changed: [borderleaf1-DC2]
changed: [leaf1-DC2]

TASK [Upload config] ***********************************************************
Tuesday 20 July 2021  15:54:19 +0800 (0:00:07.624)       0:01:27.437 ********** 
changed: [leaf3-DC2]
changed: [leaf4-DC2]
changed: [leaf2-DC2]
changed: [borderleaf1-DC2]
changed: [leaf1-DC2]

PLAY [borderleafs] *************************************************************

TASK [Register variables] ******************************************************
Tuesday 20 July 2021  15:54:39 +0800 (0:00:19.759)       0:01:47.199 ********** 
ok: [borderleaf1-DC1]
ok: [borderleaf1-DC2]

TASK [Create Borderleaf config] ************************************************
Tuesday 20 July 2021  15:54:43 +0800 (0:00:03.776)       0:01:50.976 ********** 
changed: [borderleaf1-DC1]
changed: [borderleaf1-DC2]

TASK [Upload config] ***********************************************************
Tuesday 20 July 2021  15:54:46 +0800 (0:00:02.970)       0:01:53.947 ********** 
changed: [borderleaf1-DC1]
changed: [borderleaf1-DC2]

PLAY [DCI] *********************************************************************

TASK [Register variables] ******************************************************
Tuesday 20 July 2021  15:54:53 +0800 (0:00:06.871)       0:02:00.819 ********** 
ok: [DCI]

TASK [Create DCI config] *******************************************************
Tuesday 20 July 2021  15:54:54 +0800 (0:00:01.410)       0:02:02.230 ********** 
changed: [DCI]

TASK [Upload config] ***********************************************************
Tuesday 20 July 2021  15:54:55 +0800 (0:00:00.716)       0:02:02.947 ********** 
changed: [DCI]

PLAY RECAP *********************************************************************
DCI                        : ok=3    changed=2    unreachable=0    failed=0    skipped=0    rescued=0    ignored=0   
borderleaf1-DC1            : ok=6    changed=4    unreachable=0    failed=0    skipped=0    rescued=0    ignored=0   
borderleaf1-DC2            : ok=6    changed=4    unreachable=0    failed=0    skipped=0    rescued=0    ignored=0   
leaf1-DC1                  : ok=6    changed=3    unreachable=0    failed=0    skipped=0    rescued=0    ignored=0   
leaf1-DC2                  : ok=6    changed=3    unreachable=0    failed=0    skipped=0    rescued=0    ignored=0   
leaf2-DC1                  : ok=3    changed=2    unreachable=0    failed=0    skipped=0    rescued=0    ignored=0   
leaf2-DC2                  : ok=3    changed=2    unreachable=0    failed=0    skipped=0    rescued=0    ignored=0   
leaf3-DC1                  : ok=3    changed=2    unreachable=0    failed=0    skipped=0    rescued=0    ignored=0   
leaf3-DC2                  : ok=3    changed=2    unreachable=0    failed=0    skipped=0    rescued=0    ignored=0   
leaf4-DC1                  : ok=3    changed=2    unreachable=0    failed=0    skipped=0    rescued=0    ignored=0   
leaf4-DC2                  : ok=3    changed=2    unreachable=0    failed=0    skipped=0    rescued=0    ignored=0   
spine1-DC1                 : ok=3    changed=2    unreachable=0    failed=0    skipped=0    rescued=0    ignored=0   
spine1-DC2                 : ok=3    changed=2    unreachable=0    failed=0    skipped=0    rescued=0    ignored=0   
spine2-DC1                 : ok=3    changed=2    unreachable=0    failed=0    skipped=0    rescued=0    ignored=0   
spine2-DC2                 : ok=3    changed=2    unreachable=0    failed=0    skipped=0    rescued=0    ignored=0   

Tuesday 20 July 2021  15:55:02 +0800 (0:00:07.130)       0:02:10.077 ********** 
=============================================================================== 
Upload config ---------------------------------------------------------- 19.76s
Upload config ---------------------------------------------------------- 13.59s
Register variables ------------------------------------------------------ 9.76s
Create Leaf config ------------------------------------------------------ 7.62s
Upload config ----------------------------------------------------------- 7.13s
Upload config ----------------------------------------------------------- 6.88s
Upload config ----------------------------------------------------------- 6.87s
Create Spine config ----------------------------------------------------- 5.59s
Register variables ------------------------------------------------------ 4.92s
Register variables ------------------------------------------------------ 3.78s
Create Borderleaf config ------------------------------------------------ 2.97s
Create leaf1-DCs lab-reset config --------------------------------------- 2.91s
Register variables ------------------------------------------------------ 1.41s
Register variables ------------------------------------------------------ 1.27s
Create DCI config ------------------------------------------------------- 0.72s
```
#### EBGP underlay deployment
```
# ansible-playbook playbooks/underlay_config_DCs.yml
```
#### Reset Lab Env
```
# ansible-playbook playbooks/lab-reset.yml
```
#### Backup running-config files
```
# ansible-playbook playbooks/backup.yml
```
#### Set SSH key
```
# ansible-playbook playbooks/add_ssh_key.yml
```
