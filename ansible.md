

# Getting started with Ansible

The software development industry has grown over the years, from simple software running in one machine to complex systems running on multiple servers in the cloud. Provisioning and managing complex server architecture across different environments can be a challenge. This in most cases is the major cause of product releases and iterations.

The traditionally we would manually provision servers, install all the dependencies, then launch software in them. This approach has its flaws. Assuming that your infrastructure gets corrupted or fails, to spin new servers you will have to go through the same painful process all over again. Frustrating right? Introducing IaC infrastructure as Code.

## Infrastructure As Code.

Infrastructure as Code (IaC) is the process of managing and provisioning computing and networking infrastructure and their configuration through machine-processable definition files (Code), rather than physical hardware configuration or the use of interactive configuration tools. Such files can be kept in source control to allow audibility and reproducible builds, subject to testing practices, and the full discipline of continuous delivery.

There are many tools that are used to achieve this such as [Puppet][1], [Chef][2], [Ansible][3] etc. However, in this article, we will be looking into Ansible.

## What Is Ansible?

[Ansible][4] is an open source automation platform that is used for configuration management, application deployment, task automation e.t.c. It can also do IT orchestration to run tasks in sequence and create a chain of events which must happen on several different servers or devices. In simple terms, Ansible enables you to define your infrastructure as code in a simple declarative manner.

## Why Use Ansible?

When it comes to choosing any tool, there is always the question, Why should I use it? What is the deal breaker? There are many reasons why you would choose Ansible as you configuration management tools. Here are some of them.

### It is agentless.

As compared to [Chef][3] or [Puppet][2], Ansible does not make use of the agent in the remote host rather it makes use of ssh to manage and provision systems. This a good news since you don't have to configure anything on the host before you can use it. This approach makes it easier to set up and to use.

### Parallel execution.

Ansible makes use the ad-hoc mode to run shell commands across multiple machines. This can come in handy if you are provisioning many servers. This reduces provision time make it easier and faster to replicate your infrastructure.

### Automatic reporting.

It is recommended to name all the Ansible tasks in a very descriptive manner in your provisioning script. When the script is executed, Ansible will provide descriptive reports whether or not the task succeeded with or without changes. The messages are also coloured providing nice tidy reports.

### Easy to use.

Ansible uses `YAML` as its configuration syntax. This makes it easier to the user as compared to using a bash script. Taking into consideration that `YAML` is easy to learn, therefore reduces the learn curve.

### Separation of concern

Ansible has been designed to be modular in nature. With the use of roles, we can build components to accomplish specific tasks. This approach makes it easy to modify [playbooks][5] without affecting the general execution of the playbooks. Besides we can easily reuse this component in other setups thus reducing duplication.

### It is Testable

Did I just mention tests? Yes, Ansible playbooks can be tested. We have adopted TDD in many of our software development workflows, but with Ansible, we can introduce TDD to develops. Apart from checking for syntax, you can write tests to check if the servers are being provisioned and-and have all the required dependencies.

What we have mentioned above are few among many reasons why Ansible is awesome and why you should start using it right away. If you are reading this article there is a chance you have already decided to use it or you are looking to learn to use it. Worry not by the end of this article you should have enough information to get you started.

## Ansible Basic Concepts

In this article, we are looking to cover the key concepts that you should know to get started with Ansible. I will not be diving deeper into these concepts rather I will be providing guidelines and to get you going. This means it will be up to you to take the extra step to practice and research in these concepts. Nothing beats practice!

Here are the concepts we will be looking into. It is important that you get to understand them, how they are used and why. If you need more information, you can reference [Ansible documentation][6] \- Which is awesome by the way.

## Inventory

Ansible works against multiple systems in your infrastructure at the same time. It does this by selecting portions of systems listed in Ansible's inventory file, which defaults to being saved in the location `/etc/ansible/hosts`.

Through inventory files, you can specify meaningful groups of hosts that Ansible will provision. You also can specify group variables or host variables that will help to control how Ansible interacts with remote hosts and they will be available later in playbooks.

Below is an example of an inventory file specify a group of hosts that Ansible will provision.
    
    
    [webservers]
    web.one.com
    web.two.com
    
    [dbservers]
    db.one.com
    db.two.com
    db.three.com
    

The headings in brackets `[]` are group names, which are used in classifying systems and deciding what systems you are controlling at what time and for what purpose. It is ok to put systems in more than one group, for instance, a server could be both a web server and a DB server, as showcased in the example above.

## Modules

Ansible ships with a number of modules (called the 'module library') that can be executed directly on remote hosts or through playbooks. These modules can control system resources, like services, packages, or files (anything really), or handle executing system commands.

Modules use available context ("Facts") to determine what actions to execute, based on the state of the host machine. As we mentioned earlier, Ansible is idempotent, by using existing context, Ansible modules can determine if a task is to be executed or not. This ensures that not matter how many times you run Ansible scripts they will always remain in the same state. A good example is an `apt` module.

Let us look at an example for installing Nginx and updating system cache.
    
    
    sudo apt-get install nginx
    sudo apt-get update

The above commands through the use of `apt` module can be translated to:
    
    
    ...
    - name: Update repositories cache and install "nginx" package
      apt:
        name: nginx
        update_cache: yes
    ...

The above task will only install Nginx once as compared to the actual command that will install it even though it exists.

Can I create my own modules? Yeah, why not! Ansible allows accommodates the creation of custom modules, in case you don't find what you are looking for don't despair you can create your own package and use it to your discretion. For more details refer to [Ansible modules docs][7].

## Tasks

These are actions or steps that define the expected state of the host machine at a particular time. Ansible uses tasks as a way to order actions to be executed when a play is run. They are run sequentially this means that a task will only run when the previous task has been completed. It is recommended to name this tasks in a descriptive manner. As we mentioned above this comes I handy when it comes to reports.

Tasks are mostly used together with modules to accomplish a particular outcome. It is important to note a task can only do one operation at a time. This means one task for one operation. Sometimes a task can do more that one operation through the use of loops.

The example above in the module section is a good example of a task.

## Playbooks

Playbooks are Ansible's configuration, deployment, and orchestration language. They can describe a policy you want your remote systems to enforce or a set of steps in a general IT process. They basically are YAML files that describe the desired state of the host or a group of hosts declared in the inventory file. They are designed to be human-readable and are developed in a basic text language (YAML).

> If Ansible modules are the tools in your workshop, playbooks are your instruction manuals, and your inventory of hosts are your raw material. - Ansible docs.

At a basic level, playbooks can be used to manage configurations of and deployments to remote machines. However, they can sequence multi-tier rollouts involving rolling updates, delegate actions to other hosts, interacting with monitoring servers and load balancers.
    
    
    # sample playbook.
    ---
    - hosts: dbservers
      roles:
        - mysql
    

The playbook above targets hosts under `dbservers` group and uses the role `mysql` to provision them. I know you are asking what are roles? We will look into roles in later sections in this article.

## Roles

While it is possible to write a playbook in one very large file eventually you'll want to reuse files and start to organise things. Roles in Ansible build on the idea of including files and combining them to form clean, reusable abstractions. This allows you to focus more on the big picture and only dive down into the details when needed.

With this approach, we can build Ansible playbooks that are modular and reusable. It also enables developers to share solutions. If you don't like to reinvent the wheel you can refer to the [Ansible-galaxy][8] and explore community roles.

## Variables

Variables is not a new name to you, they are like containers that hold values that can be reused during program execution. While automation exists to make it easier to do repetitive tasks, it is not an excuse for you to repeat yourself.

Taking into consideration that all of your systems are not alike, it is also likely that you want to set some behaviour or configuration slightly different from others. In some cases, the state of the system might need to influence how they are configured.

At times you may want to generate configuration files e.g. Nginx to different servers which vary slightly. Situations like these require variables that can be used to handle these differences.

In Ansible variables are used to deal with differences between systems and to ensure that each system is provisioned based on its state and purpose. You can reference Ansible docs for more details.

Variables can be defined in `host_vars/`, `group_vars/` or `vars/` folders under your roles directory. These variables can be used to override existing defaults where necessary.
    
    
    # group_vars/all.yml
    host_name: db.one.com
    databases: 4
    
    database_names:
        - cia
        - airforce
        - marine
        - navy
    

These variables can be used in tasks or templates through the use of curly brackets `{{ variable_name }}` expressions.

## Templates

Ansible uses [Jinja][9] templating to enable dynamic expressions and access to variables. This makes templates a powerful resource that Ansible uses to generate files on the host machines. Ansible has also extended Jinja filters and tests as well as adding new plugins that can be used to dynamically resolve variables while generating files.

All the templates are placed in `templates`folder and have an extension of `.j2` an indication that they are templates, Ansible can process to get a required output. Below is an example of an Ansible template for Nginx.
    
    
    server {
      listen {{ listen_port }};
    
      location / {
        return 302 https://$host$request_uri;
      }
    }
    
    server {
      listen 443 ssl spdy;
      ssl_certificate    {{ ssl_certificate_path }};
      ssl_certificate_key    {{ ssl_key_path }};
      server_name {{ server_name }} {{ Ansible_eth0.ipv4.address }};
      location / {
        root   {{ web_root }};
        index  index.html index.htm;
      }
    }
    

If we had a Nginx role we can use the above template to generate configuration file as follows:
    
    
    - hosts: webservers
      gather_facts: yes
      remote_user: ubuntu
      sudo: yes
      vars:
        ssl_certificate_path: "/etc/ssl/certs/mysite.crt"
        ssl_key_path: "/etc/ssl/private/mysite.key"
        server_name: "www.mysite.com"
        web_root: "/var/www/public"
        listen_port: 443
      roles:
        - nginx
    

These variables are passed down to the Nginx role tasks which will, in turn, us the template, compile it and copy these file to its appropriate location on the host machine.

## Conditionals

Often the result of a play may depend on a value of a variable, facts or previous task result. In some cases, the values of variables may depend on other variables. This means that we have to run this tasks only when specific conditions are met. Let take a scenario where we are installing the MySQL database and later creating databases and users. We can only do this once and only when MySQL has been installed and the service is running.

Based on the above scenario, first, check if MySQL service is running, register the result and only create users and database when the result returned is Truthy. This is done by using `when` clause.

### The When statement.

As we have stated above, you might to skip or run tasks only when the certain condition is fulfilled. This could be as simple as installing packages, clean up or database configuration. With when clause in Ansible we can achieve this functionality very easily.
    
    
    ...
    
    # Create database user
    tasks:
        - name: check if MySql is running
        - command: bash -c 'service mysql start'
          register: mysql
    
        - name: Add user and credentials to the database
          mysql_user:
            name: bob
            password: '*EE0D72C1085C46C5278932678FBE2C6A782821B4'
            encrypted: yes
            priv: '*.*:ALL'
            state: present
         when: mysql|success

In the above example, the mysql_user task will only run if the check for MySQL database returns a zero output. In other words when the task succeeds.

## Loops

In some instances, you'll want to do many things in one task, install a lot of packages, or repeat a polling step until a certain result is reached. You can do this by doing one task per operation, but this does not scale. So What to we do?

Ansible provides us with a way to handle this kind of situation by implementing loops. Through loops, we can install packages or run operations passed to a task as a list.
    
    
    - name: add several users
      user: name={{ item }} state=present groups=wheel
      with_items:
         - testuser1
         - testuser2
    

With the example above, Ansible will repeat this task pass until all the two users have been added. The user's names are passed as `item` to the task.

There are many complex loops you can achieve depending on the situation. Refer to [ansible loops docs][10] for more details.

## Conclusion

Ansible is an open-source automation engine that automates cloud provisioning, configuration management, and application deployment. It provides an easy way for us to manage our infrastructure through easy human readable syntax. The concepts we have looked at in the above sections is a mere drop in the ocean, they are only guidelines to get you started. So it is up to you to take the step further and explore what Ansible has to offer.

Now that we have understood what Ansible is all about, it is a good time to have some practice. So prepare yourself for hands-on experience in my next article.


[1]: https://puppet.com/
[2]: https://www.chef.io/chef/
[3]: https://www.ansible.com/
[5]: https://docs.ansible.com/ansible/playbooks.html
[6]: https://docs.ansible.com/ansible/index.html
[7]: https://docs.ansible.com/ansible/modules.html
[8]: https://galaxy.ansible.com
[9]: http://jinja.pocoo.org/docs/2.9/
[10]: https://docs.ansible.com/ansible/playbooks_loops.html

  
