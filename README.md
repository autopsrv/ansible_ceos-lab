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
- [add_ssh_key.yml](https://github.com/autopsrv/ansible_docker-topo/blob/main/playbooks/add_ssh_key.yml)a playbook file to set SSH key.
### Ansible for Provisioning
#### EVPN/VXLAN overlay & EBGP underlay networks deployment
```
# ansible-playbook playbooks/overlay_config_DCs.yml
```
#### Reset Lab Env
```
# ansible-playbook playbooks/lab-reset.yml
```
#### Set SSH key
```
# ansible-playbook playbooks/add_ssh_key.yml
```
