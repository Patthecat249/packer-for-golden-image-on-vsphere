# packer-for-golden-image-on-vsphere

# Requirements
- packer-build-host
- packer 1.10
- vCenter (tested with 8.0.x)
- Internet-Access on your packer-build-host

## Definition
### What is the packer-build-host?
It is your future system, where you will build your vm-templates for vmware. In my scenario, it is a VM, which is installed manually in vCenter. Trust me, it will be your last manual Installation

You need a file called `.vsphere-secrets.json` in the packer-installation-directory of the packer-build-host so that packer can connect to your vcenter
```bash
# for example
/opt/sva/packer/.vsphere-secrets.json
```
with the content of
```json
{
    "vcenter_username": "<your-vcenter-admin-user>",
    "vcenter_password": "<your-vcenter-admin-user-password>"
}
```

## First
Download the github-repository onto your `packer-build-host`
```bash
git clone https://github.com/Patthecat249/packer-for-golden-image-on-vsphere.git
cd packer-for-golden-image-on-vsphere/playbooks
ansible-playbook 01-prepare-the-packer-build-host.yaml
ansible-playbook 02-create-a-vm-template-with-packer.yaml
```