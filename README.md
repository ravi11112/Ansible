# Ansible



## What is Ansible?

**Ansible** is an open-source automation tool used for configuration management, application deployment, and task automation. It allows you to define infrastructure as code using simple, human-readable YAML files. Ansible uses SSH (Secure Shell) for communication, making it agentless and easy to set up. With Ansible, you can automate the provisioning, configuration, and management of servers and services in a consistent and repeatable way.

## Uses of Ansible

1. **Configuration Management**: Ansible can manage the configuration of servers and ensure they are in a desired state. It simplifies tasks like installing software, configuring settings, and ensuring security compliance across a fleet of servers.

2. **Application Deployment**: Ansible automates the deployment of applications, making it easy to roll out updates and new features consistently across multiple servers or environments.

3. **Task Automation**: It can automate routine operational tasks, such as backups, log rotation, and user management, reducing the need for manual intervention.

4. **Orchestration**: Ansible allows you to coordinate complex workflows involving multiple servers and services, ensuring they work together seamlessly.

5. **Infrastructure as Code (IaC)**: With Ansible, you can describe your infrastructure in code, making it easier to version, replicate, and share infrastructure configurations.


## Difference Between Pull-Based and Push-Based

**Push-Based**: In a push-based configuration management system, a central server or controller pushes configurations and updates to managed nodes. Ansible primarily follows this approach. The controller initiates communication and pushes configurations to the managed servers via SSH. This method provides real-time control over managed nodes.

**Pull-Based**: In a pull-based system, managed nodes periodically pull configurations from a central repository or server. Tools like **Puppet** and **Chef** follow this approach. Managed nodes check for updates on their own schedules, which can be less immediate but is suitable for environments with unreliable connectivity.

## Terraform vs. Ansible

**Terraform** and **Ansible** serve different purposes but can be complementary:

- **Terraform** is an infrastructure as code (IaC) tool used to provision and manage cloud resources and infrastructure. It focuses on creating and managing the structure of your environment, such as virtual machines, networks, and storage.

- **Ansible** is a configuration management and automation tool for tasks like software installation, configuration, and application deployment on existing infrastructure. It operates at a higher level of abstraction than Terraform.

In many cases, using both Terraform and Ansible together can provide a comprehensive solution. Terraform provisions the infrastructure, and Ansible configures and deploys applications on that infrastructure. This combination ensures a complete end-to-end automation solution for your infrastructure and applications.




## connect your host ansible to the target using SSH


To connect your Ansible host with a node using SSH, you can use Ansible to generate an SSH key pair on the host and copy the public key to the node's authorized_keys file. Here are the steps to do this:
Generate an SSH key pair on the Ansible host using the **ssh-keygen** command. This will create a public key file **(id_rsa.pub)** and a private key file (id_rsa) in the ~/.ssh directory.
Copy the public key to the node's **authorized_keys file**.



## AD-hoc commands use for simple task   and inventory to store ip,hostname,ssh,...


// *Ad-hoc commands* are one-off commands that you can run on one or more hosts using the `ansible` command-line tool. Ad-hoc commands are useful for tasks that you don't need to run frequently or that don't require a full playbook. Ad-hoc commands are specified using the `-m` flag, which stands for "module", and the `-a` flag, which stands for "arguments". For example, the following ad-hoc command uses the `shell` module to run the `touch` command on all hosts in the inventory file:


 <b> ansible -i inventory all -m "shell" -a "touch ravi" </b>
 

This command will create a file named `ravi` in the current directory on all hosts in the inventory file.


// *Inventory files* are files that contain a list of hosts that Ansible can manage. Inventory files can be in various formats, such as INI or YAML, and can include information about the hosts, such as their IP addresses, hostnames, and SSH credentials. Inventory files can also include groups of hosts, which can be useful for running commands on subsets of hosts. For example, the following inventory file defines two groups of hosts:


[web]
web1.example.com
web2.example.com


[db]
db1.example.com
db2.example.com


This inventory file defines a `web` group with two hosts (`web1.example.com` and `web2.example.com`) and a `db` group with two hosts (`db1.example.com` and `db2.example.com`).


// *Groups* in Ansible are collections of hosts that can be targeted by playbooks or ad-hoc commands. Groups can be defined in inventory files using square brackets (`[]`) and can include one or more hosts. Groups can also be nested, which allows you to define more complex relationships between hosts. For example, the following inventory file defines a `web` group that includes two hosts and a `prod` group that includes the `web` group:


[web]
web1.example.com
web2.example.com


[prod:children]
web


This inventory file defines a `web` group with two hosts and a `prod` group that includes the `web` group using the `children` keyword. This means that any playbook or ad-hoc command that targets the `prod` group will also target the `web` group.



## YAML syntax for an Ansible playbook file

```
---
- name: [name of the playbook]
  hosts: [name of the host group or specific host]
  become: [yes or no, indicating whether to run the playbook with elevated privileges]
  tasks:
    - name: [name of the task]
      [name of the module]:
        [module parameters]
    - name: [name of another task]
      [name of another module]:
        [module parameters]

```

- The playbook starts with "---" .
- The "name" field is a description of the playbook.
- The "hosts" field specifies the target hosts or host group.
- The "become" field specifies whether to run the playbook with elevated privileges.
- The "tasks" field contains a list of tasks to be executed.
- Each task has a "name" field and a module name, followed by any necessary parameters for that module.


<b>For example, the following playbook installs the nginx web server and starts it</b>


```
---
- name: Install and start nginx
  hosts: webservers
  become: yes
  tasks:
    - name: Install nginx
      yum:
        name: nginx
        state: present
    - name: Start nginx
      service:
        name: nginx
        state: started
    - name: Copy configuration file to node
      copy:
       src: /path/to/config/file
       dest: /etc/config/file

```
#ansible-playbook  -vv -i inventory my_playbook.yml



## Modules in Ansible 

<b> yum : for install some package
(present , latest ,installed,absent)  </b>

<b> service: to start the service
(started,stopped,restarted,reloaded) </b>

<b> copy: copy some file from host to node.</b>




## Variable in Ansible 

// in similar identation of host we define the variable

like **vars:**

then <b> var name: var value to store </b>

for call the variable we use jinja2 like <b> {{ var name}} </b>


you can also create a **varfiles**  in this define the variable.

and in playbook define a section like 

<b> var_files:

   - varfiles  
 </b>


**variable example**


```
---
- name: Example playbook with variables
  hosts: all
  become: yes
  vars:                            #var_files:
    my_var: "Hello, world!"           # - varfiles
  tasks:
    - name: Print variable
      debug:
        msg: "{{ my_var }}"
```



## Ansible Handlers

- Handlers are just like other tasks in a playbook, the difference being that they are triggered using the notify directive, and are only run when there is a change of state.

- A handler should have a globally unique name within the playbook.

- If multiple handlers are defined with the same name, only the first one will be called. The remaining handlers will be ignored.

- Handlers always run in the order in which they are defined in the handlers section, and not in the notify section.

- If the state of a task remains unchanged, the handler will not run. As such, handlers help in achieving idempotency. Hence a handler task only runs if there is a change in state, else it doesn't.

- To define a handler, the notify and handlers directives are used. The notify directive triggers the execution of the task(s) specified in the handlers section.


**Handlers example**

```
---
- hosts: staging
  name: Install
  become: yes
  tasks:
    - name: Install Apache2 on Ubuntu server
      apt:
        name: apache2
        state: present
        update_cache: yes
      notify:
        - Restart apache2
  handlers:
    - name: Restart apache2
      service:
        name: apache2
        state: restarted
```

## Ansible Vault 

is a feature in Ansible that allows users to encrypt sensitive data such as passwords, API keys, and other confidential information by using symmetric encryption to encrypt files and their contents


. Ansible Vault can encrypt any structured data file used by Ansible, including group variables files from inventory, host variables files from inventory, variables files passed to ansible-playbook with -e @file.yml or -e @file.json, variables files loaded by include_vars or vars_files, variables files in roles defaults files in roles tasks files, handlers files, binary files, or other arbitrary files


To encrypt a file named secrets.yml using Ansible Vault, run the following command: 

#ansible-vault encrypt secrets.yml

set password 

To decrypt the secrets.yml file, run the following command:

#ansible-vault decrypt secrets.yml


# Ansible Roles

<b> Ansible Roles are a way to organize and package your automation tasks into reusable components. They provide a structured approach to managing your playbook code and make it easier to share and maintain your Ansible automation </b>



**Ansible roles are organized into different sections, each with a specific purpose. Here are the different sections in an Ansible role and a brief explanation of each:**

- tasks: This section contains the main list of tasks that the role executes. It is the entry point for a role, and all tasks derive from it.

- vars: This section contains variables that are used by the role. These variables can be overridden by the user.
  
- handlers: This section contains handlers, which are tasks that are only executed when notified by another task. Handlers are useful for restarting services or triggering other actions that depend on the success of a task.

- files: This section contains static files that are copied to the target system.

- templates: This section contains files that are used to generate configuration files or other text files.

- meta: This section contains metadata about the role, such as its author, dependencies, and version.

- defaults: This section contains default variables that are used by the role if no other value is specified. These variables can also be overridden by the user.

- library: This section contains custom modules that are used by the role.


#ansible-galaxy init <role_name>

to execute the playbook 
#ansible-playbook -i inventory my_playbook.yml

