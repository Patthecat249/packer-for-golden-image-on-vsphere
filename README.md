# packer-for-golden-image-on-vsphere

## Requirements
- packer-build-host
- packer 1.10
- vCenter (tested with 8.0.x)
- Internet-Access on your `packer-build-host`

## Definition
### What is the packer-build-host?
It is your future system, where you will build your vm-templates for vmware with packer and its hcl-files. In my scenario, it is a VM based on Rocky-Linux, which is installed manually in vCenter. But trust me, it will be your last manual Installation. You can choose your linux-distribution of your choice for the `packer-build-host`.


### What you will need on your packer-build-host later
You need a file called `.vsphere-secrets.json` in the packer-installation-directory of the packer-build-host so that packer can connect to your vcenter. This file stores sensitive information about your login-credentials to your vcenter-server. 

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

## How to get started
- Download the github-repository onto your `packer-build-host`
- go into the playbooks-folder and execute the playbooks in the mentioned order. 
- Customize your inventory-file. 
- ssh-copy-id your public-key to your packer-build-host.
```bash
git clone https://github.com/Patthecat249/packer-for-golden-image-on-vsphere.git
cd packer-for-golden-image-on-vsphere/playbooks
ansible-playbook 01-prepare-the-packer-build-host.yaml
ansible-playbook 02-create-a-vm-template-with-packer.yaml

# Some examples how to create a vm-template with extra vars
ansible-playbook 02-create-a-vm-template-with-packer.yaml -e "packer_build_version=9-3"
```

## Details about the workflow
This repository contains two ansible-roles in the roles-folder and two playbooks in the playbooks-folder. The first playbook prepares your packer-build-host. It will download `packer` and `nginx` and installs and configures both of them.

The second playbook `02-create-a-vm-template-with-packer.yaml` will render the kickstart-file `kickstart-template.j2`  and copies it to your nginx-webserver. Then it renders the packer-hcl-template-file `packer-hcl-template.j2` which is in the `templates`-folder and copies it to your packer-installation-directory `/opa/sva/packer/`.

The almost last step is, that the playbook-task initializes `packer` on your `packer-build-host` so that it downloads the vsphere-plugin and can create your first vm-template in your vcenter-environment.

## Customization needed?
Please adjust the vars-files and default-files as you need.
```bash
# these are the config-file-locations
- roles/01-prepare-packer-builder-host/vars/main.yaml
- roles/01-prepare-packer-builder-host/defaults/main.yaml
- roles/02-build-vm-template-with-packer/vars/main.yaml
- roles/02-build-vm-template-with-packer/defaults/main.yaml
```