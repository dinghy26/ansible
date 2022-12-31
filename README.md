# Ansible Implementation Overview

This server is located in Proxmoz-Jax Server and the name is Ubuntu-Ansible and it can be reached remotely using Tailscale. All Playbooks and .cfg files can be found on the [dinghy26](https://github.com/dinghy26/ansible) GitHub server. 

## Ansible Setup Steps

For easy access to the servers, we will create ssh keys to add to all servers in the inventory. the type of key that we are going to use is ed25519. we will create two keys one with a passphrase and one without. the one with a passphrase will be used for the initial access to the machines being worked on and the second key will be used by ansible for easy access. Make sure you change the name of the second key to ansible so you don't override the first one.

 The following command will facilitate the creation of the key:

```plaintext
ssh-keygen -t ed25519 -C "ansible-passphrase"
ssh-keygen -t ed25519 -C "ansible"
```

-   After you create the keys you have to add to all the workstations or servers that you want to service. For this process, we will use SSH to transfer the key to the machines.
-   the following command will facilitate the addition of the keys:
-   `ssh-copy-id -i ~/.ssh/NAMES-OF-KEYS.pub xxx.xxx.xxx.xxx`

> To access the machines using the ssh keys use the following command:
> 
> `ssh -i ~/.ssh/NAMES-OF-KEYS xxx.xxx.xxx.xxx`

### Git setup and Use

We will store all ansible files on a repository on Github ([dinghy26/ansible](https://github.com/dinghy26/ansible)). Create a repository and the name you want. we will use git hub to use version control so we cant track any changes thought the time.

we are going to use one of the keys created earlier(Passphrase Key) to add to GitHub for easy synchronization through SSH. Use the following steps to add the key to your Github profile.

```plaintext
clik:
profile icon/
SSH and GPG keys/
New SSH Key/
add a title/
cat ~/.ssh/NAMES-OF-KEYS.pub (copy and pase key)
```

> Now clone the repository to our ansible server using the following command:
> 
> `git clone git@github.com:dinghy26/ansible.git` (change the command to your actual repository)
> 
> Now we tell git our info with the folowing command:
> 
> ```plaintext
> git config --global user.name "dinghy26"
> git config --global user.email "dinghy26@gmail.com"
> ```
> 
> To  add to our repository use the following command:
> 
> ```plaintext
> git status #to chek the status of your files
> git diff #to see the changes inside the file
> git add FILE_NAME #to add the file to the repo.
> git commit -m "ADD A MESSAGE" #to commit the file and add a message of what change
> git push origin main # to finally add the file to the repo
> ```

### Ansible Setup

In this section, we will create an inventory file to store all the hosts that we want to manage. create the file inside the repository you download from git hub.

Use the following command:

`nano inventory`

Add the IP addresses of the servers we want to manage.

```plaintext
192.168.XXX.XXX
192.168.XXX.XYZ
```

To make sure ansible is working use the following command:

`ansible all --key-file ~/.ssh/ansible -i inventory -m ping`

Now we are going to create an ansible config file to automate the use of our ssh key and the use of our inventory file.

-   create the .cfg file inside the ansible folder:

`nano ansible.cfg`

-   add the defaults using the following:

```plaintext
[defaults]
inventory = inventory
private_key_file = ~/.ssh/ansible
```

The creation of this file inside the ansible folder will override the ansible.cfg file that's inside /etc/ansible and it will create a mush personalize configuration for the admin. Now we can omit the ssh key and inventory file when we use our ansible commands.

`ansible all -m ping`

> ansible usefull commands to view hosts:
> 
> ```plaintext
> ansible all --list-hosts #list hosts 
> ansible all -m gather_facts #ghater facts about hosts
> ansible all -m gather_facts --limit 192.168.XXX.XXX # limit to just one host
> ```

## Ansible-Playbooks

In order to make automation easy, we use Ansible playbooks to gather all the commands in a simple .yml file. (inside ansible folder)

`nano NAME-OF-FILE.yml`

This is an example of a Playbook used for Linux servers:

```plaintext
---

- hosts: all
  become: true
  tasks:

  - name: Update Machines
    package:
      update_cache: yes
    when: ansible_distribution_version in ["Debian", "Ubuntu"]

# add qemu agent to vms 
  - name: Add QMU agent to VMs
    package:
      name: qemu-guest-agent
    when: ansible_virtualization_type == "kvm"
```

> To execute this playbook use the following command:
> 
> `ansible-playbook --ask-become-pass apt_update.yml --limit remote`
> 
> This command applies the changes only to the remote group on our inventory file.

In order to create more personalized playbooks use the ansible documentation and modules docs. to create a playbook that applies to your needs. ([Documentation](https://docs.ansible.com/ansible/latest/index.html) / [Modules](https://docs.ansible.com/ansible/latest/collections/all_plugins.html))

For automating the addition of users and the transfer of the keys I create an ansible*user.yml. To transfer all to a new server you need the initial server user and password. for my servers*, *I normally create the same user (server*\_admin).

in order to use a username and password to access the brand new server, you have to install *sshpass* in the Ansible server.

```plaintext
sudo apt-get install sshpass
```

> after running ansible*user.yml you have to update your ansible.cfg file and add a remote*user:
> 
> ```plaintext
> [defaults]
> inventory = inventory
> private_key_file = ~/.ssh/ansible
> remote_user = server_admin
> ```
> 
> After you use the ansible\_user.yml play book you dont have to use --ask-become-pass any more. Use the Following command:
> 
> `ansible-playbook apt_update.yml --limit remote`

- Also in addition to the methods discussed above i create a local.yml file that in conjunction with Ansible pull will preset a configuration in a new linux build as long ansible and flatpak is installed before running the playbook. the following is the command to run the playbook:

> ```plaintext
> ansible-pull -o -U https://github.com/dinghy26/ansible.git
```
