Ansible is a Configuration Management Tool.
It can do the following.
1. Configuration of servers.(i.e., installing a software in all systems exist in organization).
2. Application Deployment( deploying application in a environment like dev,test,preprod,prod)
3. Continuous testing of installed application.
4. Provisioning - using ansible we can’t install OS, but we can do post installation steps using ansible.
5. Orchestration 
6. Automation of tasks.

Ansible is agent less, means ansible can be installed on one machine(called control machine) and remaining hosts can be configured in control machine using a configuration file. 

Limitations:
Ansible cannot do below things.
1. It cannot do OS installation.
2. Ansible cannot monitor remote systems.
   It cannot track what process/user made changes to files on the system.

** Communication between Ansible control machine and remote machines is through SSH.

                        SSH
Ansible Machine      ————————>      Remote Machine

Ansible command syntax:

ansible host-pattern -m module-name [-a ‘module arguments’] [-i inventory]
ansible -m <module-name> -a <command-name> <target-server-group>

eg: ansible -m command -a “uptime” dev

command module is the default module
inventory file is taken from /etc/ansible/hosts
if hosts file is there in a different location then we need to specify that path with -i option.

command -> module name
uptime -> command name 
dev - target group where we will have multiple machines under this group.

Why Ansible?
1. It is open source.
2. Agent less - no need to install ansible on remote hosts(or managed hosts)
3. It is written in Python and playbooks are written in YAML format.
4. Flexible Configuraiton Management Tool.
5. Lot of pre defined modules are available to perform system administrative tasks.
6. we can also write our own Custom modules.
7. We can rollback incase of any issues in running ansible playbook.
8. Ansible can raise it permissions from normal user to root user.

In Ansible we have 2 types of hosts.
1. control node - where ansible is installed.
2. managed hosts - remote/client machines.

These managed hosts are listed in a host inventory in ansible configuration file.
we can also use wildcards for hostnames.

Ansible use SSH as communication protocol, so client machines must be SSH enabled.
During execution of ansible command, modules referenced in the playbook are copied to the managed hosts.


sample commands:

ansible -m ping dev-group

ansible -m command -a “uname -a” prod-group

ansible -m command -a “hostname” test-group

Ansible Inventory:

[webservers]
web1.example.com
web2.example.com:1234
[dbservers]
db1.example.com
db2.example.com ansible_connection=ssh ansible_user=scott


In the above inventory file 
we defined 2 target groups 
webservers and dbservers

If you see this line
web2.example.com:1234
It is using 1234 as ssh port 


Below line is login into managed host(db2.example.com) with “scott” user. 
db2.example.com ansible_connection=ssh ansible_user=scott

The default location for host inventory file is
/etc/ansible/hosts

Hosts can also be specified using ranges like
[test-group]
192.168.[4:7].[0:255]
server[01:20].example.com


192.168.[4:7].[0:255] -> Ip Adresses from 192.168.4.0 to 192.168.7.255
server[01:20].example.com -> server01.example.com to server20.example.com


How to execute some tasks with privileged rights:
we have to add below section in ansible.cfg file

[privilege_escalation]
become=True
become_method=sudo
become_user=root
become_ask_pass=True


Ansible Inventory File:
[ec2-instances:vars]
ansible_ssh_private_key_file=/Users/Leela/Desktop/AWS/EC2-keypair-mumbai.pem

[ec2-instances]
ec2-user@35.154.179.247 

Ansible commands
ansible -m command -a "uptime" ec2-instances

Below command will fail as lvdisplay needs root user
ansible -m command -a "lvdisplay" ec2-instances


Telling become command to raise its permissions to root user
ansible -m command -a "lvdisplay" ec2-instances --become

Ansible commands

Below command copies copied.png to remote host with normal user as the owner
ansible -m copy -a "src=/Users/Leela/Desktop/AWS/copied.png dest=/home/ec2-user/copied.png" ec2-instances

Below command copies copied.png to remote host with root as the owner
ansible -m copy -a "src=/Users/Leela/Desktop/AWS/copied.png dest=/home/ec2-user/copied.png" ec2-instances —become

Lists hosts in a ec2-instances target group.
ansible --list-hosts ec2-instances

pings all servers in ec2-instances target group
ansible -m ping ec2-instances


ansible -m copy -a "src=/Users/Leela/Desktop/AWS/copied.png dest=/home/ec2-user/copied.png mode=777 owner=unixadm" --become ec2-instances

Command to delete a file
ansible -m file -a "path=/home/ec2-user/copied.png state=absent" --become ec2-instances

Overriding contents of a file:
ansible -m copy -a 'content="Modified content\n" dest=/home/ec2-user/sample.txt' --become ec2-instances

Difference between shell and command module:
command module allows administrator to quickly  execute commands on managed hosts, but these commands are not processed by shell so we cannot use shell variables and we cannot pipe the output to next command, to do this we will shell module instead of command module.

Ansible Playbook to copy a file:

- hosts: ec2-instances
  become: true
  tasks:
    - name: Copy a file
      copy:
        src: /Users/Leela/Desktop/AWS/copied.png
        dest: /home/ec2-user/copied.png
        owner: unixadm
        group: root
        mode: 0777



ansible-playbook copyfile.yml


Ansible Dry Run:
Below command will do a dry execution meaning changes are not done actual on the client machine, but will tell you whether our playbook commands will be successful or not on the client machine
ansible-playbook -C <yaml-file>


Playbook Syntax Checking
ansible-playbook —syntax-check <yaml-file>

To execute tasks step by step
ansible-playbook —step <yaml-file>



To display all facts in a specific server
ansible <server-name> -m setup
ansible ec2-user@13.233.250.248 -m setup

while retrieving facts we can limit to specific field name
ansible <server-name> -m setup -a ‘filter=fact_name’
ansible ec2-user@13.233.250.248 -m setup -a ‘filter=ansible_kernel’

Variables:
it is just a normal variables
variables has following precedence
Environment Variables > ansible.cfg Variables > Playbook Variables

Variable Syntax:
{{var1}}

Facts:
Facts are system level variables of MANAGED HOSTS.
eg:
ansible_kernel
ansible_hostname
ansible_default_ipv4.address


Host Variables and Group Variables:
These variables are defined in inventory file /etc/ansible/hosts

Host Variables will be applied to single host
Group Variables will be applied to specific target group e.g.: ec2-instances

precedence:
Host Variables > Group Variables

Host Variable:
[group1]
host1.example.com user=leela

Group Variables:
[group2;vars]
user=leela

[group2]
host2.example.com
host3.example.com



——
[group1]
host1.example.com

[group2]
host2.example.com
host3.example.com

[groups:children]
group1
group2

[groups:vars]
user=leela

Here it is applied to all servers (host1, host2, host3)
[<group-name>:children] - can accept other target groups like group1, group2


Conditional Tasks:
we can execute tasks based on a condition

Handlers:
handlers are common task that can be reused in different tasks
these common tasks like restarting http or reboots etc
Handlers are executed at the end of the playbook only
