# Ansible


Ansible is software that automates software provisioning, configuration management, and application deployment.


## Architecture

As with most configuration management software, Ansible has two types of servers: controlling machines and nodes. 

First, there is a single controlling machine which is where orchestration begins. 
Nodes are managed by a controlling machine over SSH. 
The controlling machine describes the location of nodes through its inventory.To orchestrate nodes, Ansible deploys modules to nodes over SSH. 

Modules are temporarily stored in the nodes and communicate with the controlling machine through a JSON protocol over the standard output.




### Ansible uses an agentless architecture. 

diff b/w agentless vs agent-based

With an agent-based architecture, nodes must have a locally installed daemon that communicates with a controlling machine. 
With an agentless architecture, nodes are not required to install and run background daemons to connect with a controlling machine. 




## Design goals

The design goals of Ansible include:

Minimal in nature. Management systems should not impose additional dependencies on the environment.
Secure. Ansible does not deploy agents to nodes. Only OpenSSH and Python are required on the managed nodes.
Highly reliable. When carefully written, an Ansible playbook can be idempotent, in order to prevent unexpected side-effects on the managed systems.[16] It should be noted, however, that it is entirely possible to have a poorly written playbook that is not idempotent.
Minimal learning required. Playbooks use an easy and descriptive language based on YAML and Jinja templates.

## Modules
Modules are considered to be the units of work in Ansible. 
Each module is mostly standalone and can be written in a standard scripting language (such as Python, Perl, Ruby, Bash, etc.). 
One of the guiding properties of modules is idempotency, which means that even if an operation is repeated multiple times (e.g., upon recovery from an outage), it will always place the system into the same state.


## Inventory 

The Inventory is a description of the nodes that can be accessed by Ansible. 
By default, the Inventory is described by a configuration file, in INI format, whose default location is in */etc/ansible/hosts*. The configuration file lists either the IP address or hostname of each node that is accessible by Ansible. In addition, nodes can be assigned to groups.[12]

An example inventory:
```
192.168.6.1

[webservers]
foo.example.com
bar.example.com
192.168.33.10
```

This configuration file specifies three nodes: the first node is specified by an IP address and the latter two nodes are specified by hostnames. Additionally, the latter two nodes are grouped under the webservers group.

Ansible can also use a custom *Dynamic Inventory script* , which can dynamically pull data from a different system.


# Playbooks

  * Playbooks express configurations, deployment, and orchestration in Ansible.
  * The Playbook format is YAML. 
  * Each Playbook maps a group of hosts to a set of roles. 
  * Each role is represented by calls to Ansible tasks.

