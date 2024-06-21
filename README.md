## Anisble Notes

- Ansibe is a configuration management tool, that does the configuration on different OS levels.
- It consists of `control node` and `manage node` systems.
- It is an `agentless architecute`, it means on target nodes we need NOT to install any agents or software. On control node we need ansible.
- Ansible is a `push based configuration` that doesn't need an agent to be installed on the nodes.

#### why ansible?

In earlier days, sysadmin were used to install or update packages on each and every system which became hell. Different OS levels have different package manage tools.
- Hence puppet, chef and ansible came to act as an interface between the user and server with command YAML syntax that converts to different target packages suits for it.
- Puppet, chef are an agent architecute and it is a quite  complex in chefbooks which is in ruby etc.
Ansible works for provisioning CICD, configuration management ans also to automate networking.

When to use Python, Ansible and Shell scripts.

Python uses mostly to talk with APIs, SDK's and serverless computing.
Shell can be used for basic and supported version of OS and low no. of VM's
ANsible for larger.differnt OS to get installed/updated packages.

* Python is mandatory at both control and managed to nodes to work with ansible.

## Ansible Passwordless authentication

In simple, passwordless authentication mean when control node tries to connect with managed nodes, it should not ask for password.
- Passwordless authentication has 2 types - ssh-key and password.
- here when we create or for existing vm we have ssh-key or pem key to login in server. Intially execute this command -
` ssh-copy-id -F "-o IdentityFile ~/path to you pem file " ubuntu@<ipAdd>` Once this is done, try ssh now   ` ssh ubuntu@<ipAdd>`  - now this should login in vm without asking any password.

- For password authentication to server - once login into server, need to edit a file called `/etc/ssh/sshd_config.d/60-cloudimg-setting.conf` and edit passwordAuthenication to yes.
- Restart sshd - systemctl restart sshd
- now you can login into server using password - ssh ubuntu@iPadd

- similalry for password disable for ansible, Once you do `ssh ubuntu@<ipAdd>` then it will prompt for Password and once pass checks in. it logs in.
- Now similarly we need to do `ssh-copy-id ubuntu@<iPAdd>` and it asks for password and once logged in, thats all! 
- now check with `ssh@ubuntu<IpAdd>` it shouldn't prompt for pass and should login succeceded.

Once familiar with this passwordless authentication mechansim. Now can configure this hosts in ansible inventory.

#### Inventory file

- Inventory file is a hosts file where you keep target/managed node Ip adderess to connect with control node.
- Inventory file by default localtion is under cd /etc/ansible/hosts.
- We can place this inventory file anywhere but needs to pass path to command while executing and format is text file and file extension can be .ini
- The file structure looks like as `username@IpAdd` and we can group the servers for eg: [webservers] 'servers'

[webservers]
ubuntu@1.1.1.1
[dbservers]
ubuntu@2.2.2.2

Syntax for adhoc commands - 
 ansible -i <path to inventory file> -m "module" -a "args" all (hosts)
 eg - ansible -i inventory.ini -m "shell" -a "apt update" dbservers

 #### Ansible Playbooks - YAML

YAML - yet another markup language.

Yaml is serialization format of inputs written in a file.
It has list, documents and list & documents.

### List:

family:
  - mother
  - father
  - sister
  - brother
Documents is the format that consists key value pair under list, i.e:

address:
   Street : xxxxxxx
   Village: xxxxxxx
   City: xxxxxxx
   Pincode: xxxxxx

Now, point to be noted is, we don't mention "-" hypen in documents(key value pair). We only mention this hypen for lists.

Listed Documents:

 Family:
   Address:
      key  : value
      key  : value
      key  : value
      key  : value

Sample playbook:

- sample playbook
- - - 
- hosts: all 
  become: true
  tasks:
    - name  : INstall nginx 
      ansible.builtin.apt:
        name: nginx
        state: present
        
====

##### Roles
Roles are like a bunch of files altogether, structured to maintain robust for a module (similar to modules)
It consists of vars, default, tasks etc.

we can publish the roles in ansible collections and we can utilize from public roles as well.
Roles will execute on target nodes (it ssh into target instances and execute the requirements)

##### collections
Collections are similar to modules, but talks with third party API's
for eg: create an instance on AWS, then collections will help to create an instance. we need to install collections on local machine and execute the requiement.

Collections will execute on localhost and talks with APIs to create services on Cloud etc.

##### vault
Ansible will have bydefault vault, which is capable of encyrpt and decrypt the sensitive files.

create a vault.pass file with your password and use it for encrypt the senstive files. 
for eg: `ansible-vault create group_vars/all/password.yaml --vault-password-file vault.pass`

enter you variables as key value pairs and pass this vault.pass to decrypt them while exeuting your require command
for eg - `ansible-playbook ec2.yaml --vault-password-file vault.pass`



