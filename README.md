
## Overview

This package is tailored toward home lab work. It was frustrating for me to deal with having to specify host IPs, usernames, and passwords in different playbook folders, as well as ensuring they were in a nearby directory. Maintaining a global list of variables seems a bit easier if you don't have that many servers.

Further improvement needs to be done so the passwords can only be seen when decrypting the vault. I'm still a noob when it comes to Ansible so I don't know how to do this without something like Tower. I have yet to find a doc that allows you to retrieve a secret from a vault and assign it to a global variable on demand.

At least with this method if you want to back up all your main Ansible playbook folder to Git, you don't have to accidentally worry about uploading a playbook that has your credential or host data in it.

## IMPORTANT:

Note: The Ansible ESXi create VM calls will not work with the free ESXi license (this is not documented, I submitted a doc pull request about this). You will need at least the ESXi essentials license or vCenter. You can find a paid license online if you look hard enough.

Install the necessary modules for ESXi / ProxMox

---

Proxmox module install command:
```
ansible-galaxy collection install community.general
```
see  - https://galaxy.ansible.com/community/general


----

VMware modules:
```
ansible-galaxy collection install community.vmware
```

VMware - https://galaxy.ansible.com/community/vmware

---

## To Use

Set up global variables containing your hosts and credentials at the bottom of your global hosts file. E.g.

    vi /etc/ansible/hosts

Go to the bottom of the file

Set up global vars like seen below. Note, you MUST use equal signs when assigning the values, colons will not work *ugh*.
Also note that `gbl` is not required, it's just a friendly prefix I used to designate that I'm referencing a global variable in templates. )

```
[all:vars] # Designates that this can be read from every playbook.

# ESXi info
gbl_esxi_host1_username=root
gbl_esxi_host1_pw="your super secret password"
gbl_esxi_host1_addr=10.0.0.10

#proxmox info
gbl_prox_api_user=jdoe@pve # @pve means using the local Linux PVE auth, not PAM, if you want PAM use @pam
gbl_prox_api_pw="your super secret password"
gbl_prox_api_host=192.168.9.11
```

Now any playbooks you create under ~/ansible will be able to use these global variables:
```
E.g. you can specify playbooks in any of these paths:
 ~/ansible/playbook1.yml
 ~/ansible/esxi/playbook4.yml
 ~/ansible/routers/cisco/playbook1.yml
```
To call the variables in the playbooks you just call them like you would any other variable.

e.g.
```
  tasks:
    - name: Create a virtual machine...
      vmware_guest:
        hostname: "{{ gbl_esxi_host1_addr }}"
        username: "{{ gbl_esxi_host1_username }}"
        password: "{{ gbl_esxi_host1_pw }}"
    ...
```

Have fun.
