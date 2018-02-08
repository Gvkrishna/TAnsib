


#  Ansible 

* * *

###  Ansible

In this episode series, we will be looking at Ansible, which is an easy to use configuration management and orchestration tool. My goal for this series, is to show you what Ansible is, how it works, and the steps to get going on your own.

#### Series Overview

I have split this episode series into four parts, mainly because I wanted to give a bit of a crash course on what configuration management is, along with how Ansible fits into that picture. In this episode, part one, we will look at a high level overview of what Ansible is, how it works, and what you might want to use it for via some example use-case scenarios. I am mostly just going to show you what Ansible is via diagrams in this episode, along with some external reference links, no command line bits just yet. We are also going to look at configuration management in general, and how this is an improvement over doing things manually, in the hopes that this will give Ansible some context. In part two, we are going to setup a bunch of virtual machines using Vagrant. We will work through installing Ansible from scratch, how it operates at the command line, what the configuration files look like, and how communication works between nodes. Then it part three, we will look at using Ansible for configuration management tasks, by taking generic virtual machines from our Vagrant environment, and turning them into a web cluster, using haproxy and some nginx web servers. Finally, in part four, we will use Ansible to do a zero-downtime rolling software deployment across a cluster of web nodes. The idea is that we can deploy code across a fleet of machines, without any downtime by using Ansible to orchestrate the various tasks for us, this will come in really handy for continues deployment workflows. Throughout each of these episodes, I will give you all the code, and commands used, in the hopes you will duplicate my results, as I think it is a great way of learning. So, now that you have a general idea of what this series is about, lets jump into part one.

![Ansible Episode Series Overview][1]

#### Before Configuration Management

I thought it might make sense to give you a brief overview of my take on the problem Ansible is trying to solve. Imagine for a minute that you have two machine. The one of the left could be your laptop, desktop, or even a server you have access too, we will call it the management node. The one on the right, is a fresh Ubuntu virtual machine that we have just installed and booted, in reality this could be bare metal physical box, or even a cloud instance. Lets say we wanted to turn this freshly installed Ubuntu machine into a web server, say for example we are running Apache hosting a website, a rails app, or something similar. How would we go about doing that? Well, we could do this manually by sshing into the machine, using its IP address 10.0.15.21, and running the commands to install your application stack, editing the configuration files by hand, and finally copying over our application code. Once you are all done, you disconnect, and the machine is configured a working. This is pretty common practice, but this is also pretty manual work, and even if we have the steps documented somewhere, each machine is generally its own little snowflake depending on who installed it. This manual work can quickly compound if you need to do this across tens, or hundreds of machines. It is also a real pain when one of the machines dies, because we are not really sure how it was created, or how we go about recreating it quickly. Do not get me wrong, I have manually installed many hundreds of machines, so I know this issue well, and it is extremely unpleasant when something important dies, and you are not really sure how to recreate it quickly. In that, you have a general idea of what was on there, but you engage in the same type of manual work that created this mess in the first place, potentially wasting many hours of your time on drop work type tasks. Constantly installing packages, dependencies, checking to see if it works, installing more packages, and tweaking things manually until the service is restored. It turns into this vicious cycle, and we are recreating the exact scenario that caused this mess in the first place.

![Manual Deployment Without Ansible][2]

#### Enter Ansible

There has to be a better way right? Well, this is where configuration management tools come in. At a basic level, they are tools designed to automate away much of this manual work. Saving you time, reducing stress, and generally improving the process of creating machines in a timely manner. So, lets chat about Ansible. Ansible is a free and open-source tool, mainly use on UNIX-like machines, which is directly targeted at solving this type of manual work problem. Lets start our example over again, and have a look at how we would solve this problem using Ansible. We start with our two machines again, the one of the left could be our laptop, desktop, or even a server, but this time call it the Ansible management node, because this is where we will install the Ansible software. The one on the right, is our freshly install Ubuntu machine, and this can be anywhere from our local machine running a VM, a physical box, or a cloud instance. Once Ansible is installed on the management node, you will typically need two configuration files, something called a host inventory, and the other a playbook. The host inventory, is basically just a listing of hostnames, or IP addresses, for machines that we want to manage, and how they should be group together. In the example here, our Ubuntu box has the address 10.0.15.21, so we would just add this address to our host inventory, likely under a web group, since we want its end state to be a web server.

#### Hosts Inventory

Actually, let me just show you what an example host inventory looks like, since this will likely make much more sense. So, the host inventory INI file is basically just a listing of hosts that you want to manage with Ansible, you can group them together under arbitrary headings too. You use these brackets to create a group, then put the group name inside, in this case we have a load balances group, and it has three hosts assigned to it. For hosts you want to manage with Ansible, you can use their fully qualified hostnames names, short hostnames, or IP addresses. You can see down here in the webservers group, we added our Ubuntu virtual machines IP address from the diagrams. You can also call these groups anything you want, lets change the name from webserver, to just web. Now that you know what the host inventory looks like, lets just jump back to the diagrams, and chat about playbooks. I should mention that you do not need to know too much about this right now, we will cover these files heavily in the other episode parts, for right now, it is just important to know these configuration files exist.
    
    
    [loadbalancer]
    haproxy-01.example.com
    haproxy-02
    10.0.15.15
    
    [web]
    web1
    web2
    10.0.15.21
    
    [testing]
    demo.example.com
    

#### Playbooks

Playbooks are configuration files that outline tasks that should be performed against hosts in the hosts inventory. In our example today, we are setting up a web server on our freshly installed Ubuntu box, so we would likely want to install apache, or nginx, update the web server configuration files, deploy our application content, restart the server, etc. Basically, all of the tasks we would have done manually, just documented in a configuration file called a playbook, so that Ansbile knows what to do. Playbooks allow you to define each of these steps, in a simple, and quick to understand format, that pretty much anyone not familiar with Ansible can read and understand. Lets quickly have a look at an example web server playbook. The configuration format is called YML, but you do not really need to understand this to work with Ansible, as it is pretty straight forward. So, here we have defined our hosts group which we want to run this playbook against, this should look familiar from our hosts inventory. We have this sudo line here, this allows Ansible to connect as a non-root user, then elevate to root for tasks like installing packages, adding users, or deploying configuration files. Next we have a series of tasks that we want to run against each host in hosts inventory web group. You can see here that we want to install nginx, update our custom nginx configuration file, deploy our website code via a git pull from version control, and finally make sure nginx is started. This pretty closely follows what we would manually do in real life. Again, do not worry about understanding all of this right now, we are going to look at many live examples throughout this episode series. I should also mention, that in our example today, we are going to be mainly looking at using playbooks, these are configure files that outlines tasks that should be run, but you can also run Ansible in an ad-hoc mode, basically using it as a parallel command execution engine across a fleet of machines. We will look at this heavily in parts two, three, and four of this episode series, but this ad-hoc mode can be thought of as a smart parallel ssh engine, at least that is what I think of it as, just with a lot more cool functionality built in.
    
    
    ---
    - hosts: web
      sudo: yes
      
      tasks:
    
      - name: install nginx
        apt: name=nginx state=installed update_cache=yes
    
      - name: write our nginx.conf
        template: src=templates/nginx.conf.j2 dest=/etc/nginx/nginx.conf
        notify: restart nginx
    
      - name: deploy website content
        git: repo=https://github.com/jweissig/episode-47.git
             dest=/usr/share/nginx/html/
             version=release-0.01
    
      - name: start ntp
        service: name=nginx state=started
    
      handlers:
    
      - name: restart nginx
        service: name=nginx state=restarted
    

#### With Configuration Management

So, now that you have a basic idea of the prerequisites for Ansible, those being that you have to install Ansible onto a management node, define a hosts inventory, and have some playbooks defined. Lets, have a look at how we would turn our Ubuntu virtual machine into a web server using Ansible. You run Ansible Playbook on the management node, it looks through the playbook that you have defined as a command argument, and notices that we are targeting nodes in the web group. Ansible then reads in the hosts inventory to find nodes assigned to the web group. At this point, Ansible is ready to get to work, so it will remotely connect via ssh to the defined machines, typically you will want to have some type of ssh trust established via pre-shared keys, so that you do not have to enter the password all the time. Ansible will then start to step through the playbook tasks, one task at a time, going through them sequentially, from top to bottom, just like you would have done if logging in manually. So, it installs the packages, updates the configuration files, deploys our website code by using git, and finally starts our web service. When Ansible is happy that everything worked as expected you will get a status report saying that everything is good.

![Ansible Deployment Workflow][3]

So, that is basically the default Ansible workflow in a nutshell. But, you will soon notice that Ansible is a pretty flexible tool, and there are exceptions to pretty much everything I have shown you so far. For example, the hosts inventory can be a database if you have thousands of hosts, you also do not always need playbooks, you can run ad-hoc commands too. You can swap out ssh for a queue type system, if you find that performance is an issue for your number of hosts. But, for these examples today, I thought I would just show you what I think is the default mode of operation, when first playing around with Ansible.

#### How is Ansible is different?

You might be wondering how Ansible is different from other configuration management tools, like Puppet and Chef for example, so I thought it would be useful to mention a couple items, before we move onto more complex examples. Probably the most glaring difference is that Ansible pushes the configuration out to each managed machine via ssh. Ansible only requires that you install the Ansible software onto a management node, and that the remote machines is running ssh with python installed, which every major distribution come with by default, there are no remote agents that you need to install, and everything is done via ssh from the management node. This make getting going with Ansible very easy, and upgrades are a snap, because you only need to update the Ansible install on one machine.

The Ansible documentation is absolutely fantastic, you can read the manual pages and quickly understand how Ansible functions, I honestly cannot say enough good things about them. You will often hear to term, batteries included, when reading about Ansible, that is because there are over 250 helper modules, or functions included with Ansible. These allow you to construct playbooks to smartly add users, install ssh keys, tweak permissions, deploy code, install packages, interact with cloud providers for things like launch instances or modifying a load balancer, etc. Each module has a dedicated page on the Ansible documentation site, along with detailed examples, and I have found this a major bonus to working with Ansible. I should mention that, even though Ansible is using ssh to connect to these remote machines, your playbooks and ad-hoc commands will almost always be using these 250 plus modules to smartly do things. I guess what I am trying to say, is that Ansible is not simply running remote commands like a shell script would do, for automating package installs, adding users, etc. There is tons of logic built into these modules, and I encourage you to check these manual pages, just to get a sense of what can be done.

One of the biggest pros to using Ansible, is that since playbooks are basically configuration files, you do not need to be an experience configuration management expert, the bar is set pretty low for getting going, and you can quickly read and understand existing playbooks. I think is a big reason Ansible is quickly becoming popular, in that you are not really writing code, just working with something that looks like a configuration file.

Ansible is also great for both ops and dev, because you do not have to give out root. Ansible is just using ssh to log into the remote machines, so there is clear separation of duties if needed, based on what account Ansible is using. One added bonus of using any type of configuration management tool, including Ansible, is that these configuration playbooks are self documenting. I guess what I mean, is that you have a clear pictures of how these machines are made, and how to re-make them, if needed, because you have a step by step outline in the playbook. One last pro for Ansible before we move on, there is a published Ansible Best Practices guide, and it is absolutely fantastic in the advice it offers. Things like how to layout your files, using version control, naming, how to keep things simple. This is some of the best published, and put together advice, I have seen out of all the popular configuration management software, by far.
