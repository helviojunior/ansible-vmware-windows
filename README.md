# How to build a Windows VM from scratch with Ansible

## Installing

```bash
ansible-galaxy collection install community.vmware
ansible-galaxy collection install ansible-galaxy collection install git+https://github.com/helviojunior/ansible-vmware-floppy.git
```

Install pyvmomi lib from my repo, because I had an fix aboult Free ESXi licence limitation
```bash
python3 -m pip install git+https://github.com/helviojunior/pyvmomi
```

## Executing

```bash
ip="10.10.10.10"; ansible-playbook -i $ip, deploy_windows.yaml
```


## Libs
https://github.com/ansible-collections/vmware.vmware_rest