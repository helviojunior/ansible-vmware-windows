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

## Inspiration
- [How to build a Windows VM from scratch with Ansible](https://madlabber.wordpress.com/2019/06/23/how-to-build-a-windows-vm-from-scratch-with-ansible/comment-page-1)
- [Using autounattend.xml to enable Ansible support in Windows](https://madlabber.wordpress.com/2019/06/19/using-autounattend-xml-to-enable-ansible-support-in-windows/)