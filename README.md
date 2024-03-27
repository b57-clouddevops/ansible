# ansible

1) Ansible is openSource 
2) This is 100% Agentless
3) This works both on push and pull mechanism 

### What is the prerequisite for ansible to work?

 ```The only that you need to make sure is that your ANSIBLE NODE should be able to SSH to the nodes that needs configuration management.```


### Ansible can be operated in 2 ways : 

    1) Manual way    ( No Code Approach and it's 100% not recommended )
    2) Automated way ( YAML : Playbooks : 100% recommended approach )

#### Install Ansible :

curl https://gitlab.com/thecloudcareers/opensource/-/raw/master/lab-tools/ansible/install.sh | sudo bash

To ansible, we need to supply the list of Servres that needs to be managed by ANSIBLE and we call that file as INVENTORY file.

    Inventory File 
    Destination Server Username & Password


### Ansible is all about modules 
ansible all -i inv -e ansible_user=centos -e ansible_password=DevOps321 -m shell -a uptime

If you go with tihs approach, you can run or execute only one command/action at a time

Automated way of dealing with ANSIBLE is with PLAYBOOKS. And playbooks can be written by using YAML ( Presentation Language )

Can I learn YAML ? Yes, in just 5 minutes : 

    1) Dictionary    ( a key value pair)
    2) List          ( a key with multiple values )
    3) Map           ( a key with multiple keyValue pairs)

YAML is indendation specific, that means spaces matters a lot.

    A Key with a value is referred as dictionary 
        name: ansible ( correct )
        name:ansible ( incorrect )

    A Key with multiple values is refereed as LIST 

        courses:
            - devops
            - cloud
            - kuberntes 

# What is a Playbook ?
    ``` scripts in ansible are referred as playbooks```

A playbook is a list of plays!!!
A play is a list of tasks!!!
A task is nothing an aciton that you want ansible to perform

Ansible expected the playbooks to have .yaml or yml as extensions


### How to run a playbook ?

    ```$ ansible-playbook -i inv -e ansible_user=centos -e ansible_password=aasa11122xzs pbName.yaml```



# Roles In Ansible 

    Roles let you automatically load related vars, files, tasks, handlers, and other Ansible artifacts based on a known file structure. After you group your content into roles, you can easily reuse them and share them with other users.

    The advantage of using ROLES is code re-usability and things will be dry.


    roles/
        common/               # this hierarchy represents a "role"
            tasks/            #
                main.yml      #  <-- tasks file can include smaller files if warranted
            handlers/         #
                main.yml      #  <-- handlers file
            templates/        #  <-- files for use with the template resource
                ntp.conf.j2   #  <------- templates end in .j2
            files/            #
                bar.txt       #  <-- files for use with the copy resource
                foo.sh        #  <-- script files for use with the script resource
            vars/             #
                main.yml      #  <-- variables associated with this role
            defaults/         #
                main.yml      #  <-- default lower priority variables for this role
            meta/             #
                main.yml      #  <-- role dependencies



# Ensure you have 
* sudo dnf install mysql-devel -y
* sudo pip3 install mysqlclient



# When to use PUSH and PULL Mechanisms ?

    1) Push Mechanism Is Used, if your inventory is STATIC ( typically on-prem ) or wher is not concept of scaling, then you can use PUSH. Because your intentory maintenance is a one time job.  

    2) Pull Mechanism Is User, if your inventory is dynamic that means servers would be coming up and down as a part of the SCALE OUT & SCALE IN Policies.

       * In Pull Based Mechanism, the expectation is that when a server is provisioned, CM should also be coming up as a part of the startup
       * For pull based mechanism, ensure the server should have the ansible Installed. 
       * Ansible Installation can be done either by 

                1) Placing the ANSIBLE Installation Script as a part of the system start up 
                2) Ensure the AMI that you're using for your server creation should have Ansible Installed 
                3) As of today, ansible-pull can only fetch the code from GIT based repositories only


    