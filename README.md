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

In order to create more personalized playbooks use the ansible documentation and modules docs. to create a playbook that applies to your needs. ([Documentation](https://docs.ansible.com/ansible/latest/index.html) / [Modules](https://docs.ansible.com/ansible/latest/collections/all_plugins.html))