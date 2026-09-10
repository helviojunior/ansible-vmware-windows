# How to build a Windows VM from scratch with Ansible

* [x] Vmware ESXi
* [x] vSphere
* [x] vCenter

## Installing

```bash
ansible-galaxy collection install community.vmware
ansible-galaxy collection install --force git+https://github.com/helviojunior/ansible-vmware-floppy.git
```

Install pyvmomi lib from my repo, because I made a fix aboult Free ESXi licence limitation
```bash
python3 -m pip install git+https://github.com/helviojunior/pyvmomi
```

`tasks/base.yml` does this for you when a lib is missing, adding
`--break-system-packages` if the controller python is externally managed
(PEP 668, Debian 12+). Two optional flags:

```bash
# force a reinstall of the controller libs (e.g. to update the pyvmomi fork)
ansible-playbook -i $ip, deploy_windows.yml -e install_controller_deps=true

# pin urllib3==1.26.6 — only needed for old ESXi with legacy SSL
ansible-playbook -i $ip, deploy_windows.yml -e pin_urllib3=true
```

## Executing

```bash
ip="10.10.10.10"; # Vmware server IP
ansible-playbook -i $ip, deploy_windows.yml
```

## Common error

```
objc[7453]: +[__NSCFConstantString initialize] may have been in progress in another thread when fork() was called.
objc[7453]: +[__NSCFConstantString initialize] may have been in progress in another thread when fork() was called. We cannot safely call it or ignore it in the fork() child process. Crashing instead. Set a breakpoint on objc_initializeAfterForkError to debug.
```

To solve this error run
```bash
export OBJC_DISABLE_INITIALIZE_FORK_SAFETY=YES
```

## Inspiration
- [How to build a Windows VM from scratch with Ansible](https://madlabber.wordpress.com/2019/06/23/how-to-build-a-windows-vm-from-scratch-with-ansible/comment-page-1)
- [Using autounattend.xml to enable Ansible support in Windows](https://madlabber.wordpress.com/2019/06/19/using-autounattend-xml-to-enable-ansible-support-in-windows/)