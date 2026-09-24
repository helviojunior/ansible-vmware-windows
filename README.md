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

## Windows 10 / 11 (ISOs "consumer editions")

As ISOs retail do Windows 10/11 trazem varias edicoes num mesmo `install.wim`,
e o setup usa a chave de produto para saber qual instalar. Sem `<ProductKey>`
no answer file a instalacao para com:

```
Windows cannot read the <ProductKey> setting from the unattend answer file.
```

Preencha `windows_product_key` no `vars.yml` (uma chave generica serve: ela so
escolhe a edicao, nao ativa o Windows):

```yaml
windows_iso: "en-us_windows_11_consumer_editions_version_22h2_updated_july_2023_x64_dvd_f69501d4.iso"
windows_product_key: "VK7JG-NPHTM-C97JM-9MPGT-3V66T"   # Windows 11 Pro
windows_bypass_requirements: true                       # TPM/Secure Boot/RAM/disco
vm_guest_id: windows9_64Guest                           # windows11_64Guest no ESXi 8+
vm_memory_mb: 4096
vm_disk_gb: 64
```

Com a chave preenchida o `<InstallFrom>` sai do XML e a edicao vem da chave;
para forcar uma imagem especifica use `windows_image_index` ou
`windows_image_name` ("Windows 11 Pro"). Para listar o que tem na ISO:

```bash
# no Linux, com wimtools instalado
wiminfo /mnt/iso/sources/install.wim
```

`windows_bypass_requirements` grava as chaves `HKLM\SYSTEM\Setup\LabConfig`
(`BypassTPMCheck`, `BypassSecureBootCheck`, `BypassRAMCheck`,
`BypassStorageCheck`, `BypassCPUCheck`) ainda no WinPE — e o que faz o Windows
11 instalar na VM com BIOS legado e sem TPM que o `tasks/deploy.yml` cria.

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