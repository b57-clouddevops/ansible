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

    1) Dictionary 
    2) List
    3) Map

YAML is indendation specific, that means spaces matters a lot.