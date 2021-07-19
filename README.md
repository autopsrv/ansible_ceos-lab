## Ansible EVPN/VXLAN Demo
### Lab Topology
![alt text](https://github.com/autopsrv/ansible_docker-topo/blob/main/l5-topo.jpeg?raw=true)

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
- [add_ssh_key.yml](https://github.com/autopsrv/ansible_docker-topo/blob/main/playbooks/add_ssh_key.yml): a playbook file to set SSH key.
### Ansible for Provisioning
#### EVPN/VXLAN overlay & EBGP underlay networks deployment
```
# ansible-playbook playbooks/overlay_config_DCs.yml 

PLAY [spines] ***************************************************************************************************************************************************************************

TASK [Register variables] ***************************************************************************************************************************************************************
Tuesday 20 July 2021  00:00:17 +0800 (0:00:00.020)       0:00:00.020 ********** 
ok: [spine2-DC1]
ok: [spine1-DC1]
ok: [spine1-DC2]
ok: [spine2-DC2]

TASK [Create Spine config] **************************************************************************************************************************************************************
Tuesday 20 July 2021  00:00:21 +0800 (0:00:04.382)       0:00:04.404 ********** 
changed: [spine1-DC1]
changed: [spine1-DC2]
changed: [spine2-DC2]
changed: [spine2-DC1]

TASK [Upload config] ********************************************************************************************************************************************************************
Tuesday 20 July 2021  00:00:27 +0800 (0:00:05.904)       0:00:10.309 ********** 
ok: [spine2-DC1]
ok: [spine1-DC2]
ok: [spine2-DC2]
ok: [spine1-DC1]

PLAY [leafs] ****************************************************************************************************************************************************************************

TASK [Register variables] ***************************************************************************************************************************************************************
Tuesday 20 July 2021  00:00:37 +0800 (0:00:10.472)       0:00:20.783 ********** 
ok: [borderleaf1-DC1]
ok: [leaf4-DC1]
ok: [leaf2-DC1]
ok: [leaf1-DC1]
ok: [leaf3-DC1]

TASK [Create Leaf config] ***************************************************************************************************************************************************************
Tuesday 20 July 2021  00:00:46 +0800 (0:00:08.137)       0:00:28.922 ********** 
changed: [leaf3-DC1]
changed: [leaf2-DC1]
changed: [leaf1-DC1]
changed: [leaf4-DC1]
changed: [borderleaf1-DC1]

TASK [Upload config] ********************************************************************************************************************************************************************
Tuesday 20 July 2021  00:00:53 +0800 (0:00:07.098)       0:00:36.021 ********** 
ok: [leaf2-DC1]
ok: [leaf1-DC1]
ok: [leaf3-DC1]
ok: [leaf4-DC1]
ok: [borderleaf1-DC1]

PLAY [leafs] ****************************************************************************************************************************************************************************

TASK [Register variables] ***************************************************************************************************************************************************************
Tuesday 20 July 2021  00:01:07 +0800 (0:00:14.129)       0:00:50.153 ********** 
ok: [leaf1-DC2]
ok: [leaf4-DC2]
ok: [borderleaf1-DC2]
ok: [leaf2-DC2]
ok: [leaf3-DC2]

TASK [Create Leaf config] ***************************************************************************************************************************************************************
Tuesday 20 July 2021  00:01:18 +0800 (0:00:11.404)       0:01:01.559 ********** 
changed: [leaf2-DC2]
changed: [leaf3-DC2]
changed: [leaf1-DC2]
changed: [leaf4-DC2]
changed: [borderleaf1-DC2]

TASK [Upload config] ********************************************************************************************************************************************************************
Tuesday 20 July 2021  00:01:25 +0800 (0:00:07.121)       0:01:08.681 ********** 
ok: [borderleaf1-DC2]
ok: [leaf1-DC2]
ok: [leaf2-DC2]
ok: [leaf4-DC2]
ok: [leaf3-DC2]

PLAY [borderleafs] **********************************************************************************************************************************************************************

TASK [Register variables] ***************************************************************************************************************************************************************
Tuesday 20 July 2021  00:01:38 +0800 (0:00:12.970)       0:01:21.655 ********** 
ok: [borderleaf1-DC1]
ok: [borderleaf1-DC2]

TASK [Create Borderleaf config] *********************************************************************************************************************************************************
Tuesday 20 July 2021  00:01:42 +0800 (0:00:03.919)       0:01:25.576 ********** 
changed: [borderleaf1-DC2]
changed: [borderleaf1-DC1]

TASK [Upload config] ********************************************************************************************************************************************************************
Tuesday 20 July 2021  00:01:45 +0800 (0:00:02.308)       0:01:27.885 ********** 
ok: [borderleaf1-DC2]
ok: [borderleaf1-DC1]

PLAY [DCI] *******************************************************************************************************************************************************************

TASK [Register variables] *******************************************************************************************************************************************************************
Tuesday 20 July 2021  00:01:49 +0800 (0:00:04.200)       0:01:32.085 ********** 
ok: [DCI]

TASK [Create DCI config] *******************************************************************************************************************************************************************
Tuesday 20 July 2021  00:01:50 +0800 (0:00:00.944)       0:01:33.029 ********** 
changed: [DCI]

TASK [Upload config] *******************************************************************************************************************************************************************
Tuesday 20 July 2021  00:01:50 +0800 (0:00:00.717)       0:01:33.747 ********** 
ok: [DCI]

PLAY RECAP *******************************************************************************************************************************************************************
DCI                        : ok=3    changed=1    unreachable=0    failed=0    skipped=0    rescued=0    ignored=0   
borderleaf1-DC1            : ok=6    changed=2    unreachable=0    failed=0    skipped=0    rescued=0    ignored=0   
borderleaf1-DC2            : ok=6    changed=2    unreachable=0    failed=0    skipped=0    rescued=0    ignored=0   
leaf1-DC1                  : ok=3    changed=1    unreachable=0    failed=0    skipped=0    rescued=0    ignored=0   
leaf1-DC2                  : ok=3    changed=1    unreachable=0    failed=0    skipped=0    rescued=0    ignored=0   
leaf2-DC1                  : ok=3    changed=1    unreachable=0    failed=0    skipped=0    rescued=0    ignored=0   
leaf2-DC2                  : ok=3    changed=1    unreachable=0    failed=0    skipped=0    rescued=0    ignored=0   
leaf3-DC1                  : ok=3    changed=1    unreachable=0    failed=0    skipped=0    rescued=0    ignored=0   
leaf3-DC2                  : ok=3    changed=1    unreachable=0    failed=0    skipped=0    rescued=0    ignored=0   
leaf4-DC1                  : ok=3    changed=1    unreachable=0    failed=0    skipped=0    rescued=0    ignored=0   
leaf4-DC2                  : ok=3    changed=1    unreachable=0    failed=0    skipped=0    rescued=0    ignored=0   
spine1-DC1                 : ok=3    changed=1    unreachable=0    failed=0    skipped=0    rescued=0    ignored=0   
spine1-DC2                 : ok=3    changed=1    unreachable=0    failed=0    skipped=0    rescued=0    ignored=0   
spine2-DC1                 : ok=3    changed=1    unreachable=0    failed=0    skipped=0    rescued=0    ignored=0   
spine2-DC2                 : ok=3    changed=1    unreachable=0    failed=0    skipped=0    rescued=0    ignored=0   

Tuesday 20 July 2021  00:01:54 +0800 (0:00:03.667)       0:01:37.415 ********** 
=============================================================================== 
Upload config ------------------------------------------------------------------------------------------ 12.98s
Register variables ------------------------------------------------------------------------------------- 11.40s
Upload config ------------------------------------------------------------------------------------------ 10.47s
Create Leaf config -------------------------------------------------------------------------------------- 7.12s
Create Spine config ------------------------------------------------------------------------------------- 5.90s
Register variables -------------------------------------------------------------------------------------- 4.39s
Upload config ------------------------------------------------------------------------------------------- 4.20s
Register variables -------------------------------------------------------------------------------------- 3.92s
Upload config ------------------------------------------------------------------------------------------- 3.67s
Create Borderleaf config -------------------------------------------------------------------------------- 2.31s
Register variables -------------------------------------------------------------------------------------- 0.94s
Create DCI config --------------------------------------------------------------------------------------- 0.72s
```
#### Reset Lab Env
```
# ansible-playbook playbooks/lab-reset.yml
```
#### Set SSH key
```
# ansible-playbook playbooks/add_ssh_key.yml
```
