# Ansible Interview Questions 

### PS: Pleaes ensure you prepare the topics and then draft and prepare your own answers and that's the correct Approach)

1) Why ansible is famouse and list me 3 points on why do I need to adopt ?

    ```
        * Ansible Is Agentless that means you don't have to install any agent on any of the managed nodes
        * This is openSource and it works on default ssh mechanism.
        * This is modular rich, works on YAML.
        * It has the capability to work both on Push and Pull
    ```

2) What is the default ansible mechanism ?

```
    1) Ansible uses SSH as the connection mechanism either by Key Pair based or Password based.
    2) By default, it works on Push, but right now Ansible also supports Pull Based Mechanism, it can pick the playbooks from GIT Only
```

3) If you're asked to decided on the usage of Pull vs Push, what would you select and would be the factors that determine the usage ?

```
If the Infra is Dynamic , that means where the infra scales-out and in dynamically in this case we prefer going with PULL based mechanism.
If the infra is stratic, we go with PUSH Based mechanism by managing the inventory.
```

4) Assume your infra is dynamic that scales out & in dynamically and in this case, would you prefer to use push or pull ?

```
    Since the infra is dynamic we would keep the playbooks on GIT and call them by using ansible-pull either from provisioners or from the user-data.
```

5) What is the need of ROLES in ansible ?
```
Ansible Roles keeps the code dry and usage and here are the reasons on why we use Roles In Ansible.
    1) Reusability
    2) Modularity
    3) Organizing the role and defining the role-dependencies
```

6) If the values of the variables are declared both in vars/main.yml and default/main.yml, which among these will have priority ?
```
    vars/ will have high priority, but we delae default values in default/ . But in case if the varaible is delcared in both the places then it goes by priority on vars/
```

7) What is the eariest way to organize the varaibles in files based on the environment ? Can we supply the vairablesin files ?

```
    The easiest way to organize is by organizing them by using roles.
    But we can also supply the values based on the files as well, we can declare the values in dev.yaml or qa.yaml and decalre that from the playbook

        Ex: 
            - name: Demo on using variables on playbooks  
            hosts: all 
            vars:                                 # Global Variables : Variables can be acessed by all the tasks of the play. 
                URL: play.google.com
            vars_files:
                - dev-vars.yml
```

8) How to call a role from another role using the task name ?

```
When you call a role by default the main.yaml file will be picked up, but if want to call another file in the tasks/ folder, we can do that by using the below syntax : 

- name: Creating App User 
  ansible.builtin.include_role:
    name: common
    tasks_from: create_user

create_user is the task called from another role common
```

9) What all modules have you used in Ansible and which version of ansible are you using ?

```
Mention all of the moduels you've used in the training. But the answer should be based on the requirement I use the ansible documentation 
    get_url 
    wget 
    copy
    template
    block_in_file
    line_in_file
    service
    systemd
    register
    set_fact
    shell
    package
    yum
    debug
```

10) What is the notify keyword in Ansible? Scenario, I want to make sure that service should only be restarted if there is a change in the file, how can you accomplish that ?

```
notify helps to run a particular task if some task is executed or changed. It's just like a reactive action.

Ex: https://github.com/b54-clouddevops/ansible/blob/main/roles/frontend/tasks/main.yml#L27

# If the source you're trying to copy is on the remote machine, ensure you give the remote_src as yes
- name: Copying the proxy config
  ansible.builtin.template:
    src: roboshop.conf
    dest: /etc/nginx/default.d/roboshop.conf
  notify: Retarting Nginx


If there is any change in the mentioned task, then the task mentioned in the folder handlers with the task name "Retarting Nginx" will be called.
```

11) What is ROLE DEPENDENCY and what's the purpose of it and when to use that ?
```
Role dependencies define a specific order for running roles within a playbook. Here's the concept explained with an example:

    *   It specifies roles that need to be executed before the current role can function properly.
    *   This ensures the target system is in a prepared state for the current role's tasks.

Imagine you have two roles:

webserver: This role installs and configures a web server like Apache.
webapp: This role deploys your web application code and configures it to work with the web server.
Here, the webapp role clearly depends on the webserver role being run first. The web server needs to be installed and configured before you can deploy your web application.


dependencies:
  - webserver  # This role needs webserver to be run first

Example : https://github.com/b54-clouddevops/ansible/blob/main/roles/frontend/meta/main.yml
```
12) What is the purpose of the become directive in Ansible? Can we run a playbook with 5 tasks as a root user and x-task in that 5 tasks as centos user 

```
become is a directive used for privilege escalation in ansible and this helps to run the playbook as a root user.

If you want to run specific tasks with a specific user we use "become_user: userName"
```

13) What's the purpose of rescue module and when do you use that ?

If any of mentioned tasks in the block of tasks fails, then the tasks mentioned in the rescue will be executed.

```
https://github.com/b57-clouddevops/ansible/blob/main/roles/mysql/tasks/main.yml#L33

- name: validating the password 
  block:
    - name: Get MySQL version with non-default credentials
      community.mysql.mysql_info:
        login_user: root
        login_password: "{{MYSQL_PSW}}"
        filter: version
  rescue:
    - name: Capturing the bash exit code 
      ansible.builtin.set_fact:
        DEFAULT_PASSWORD: "{{psw_log.content|b64decode | regex_findall('.*temporary password.*')| join('') | split(' ')|last}}"

    - name: Changing the default password 
      ansible.builtin.shell: echo "ALTER USER 'root'@'localhost' IDENTIFIED BY 'RoboShop@1'" | mysql  --connect-expired-password -uroot -p"{{DEFAULT_PASSWORD}}"

    - name: Uninstalling Password Validate Plugin
      ansible.builtin.shell: echo "uninstall plugin validate_password" | mysql -uroot -pRoboShop@1 
```

14) Situation, if any of the 3 tasks in a playbook fails then I would like to run 5 specific tasks on the 3 tasks failure, how can we deal that ?

This is doable with the usage of block+rescue

```
Example :

- name: validating the password 
  block:
    - name: Get MySQL version with non-default credentials
      community.mysql.mysql_info:
        login_user: root
        login_password: "{{MYSQL_PSW}}"
        filter: version
  rescue:
    - name: Capturing the bash exit code 
      ansible.builtin.set_fact:
        DEFAULT_PASSWORD: "{{psw_log.content|b64decode | regex_findall('.*temporary password.*')| join('') | split(' ')|last}}"

    - name: Changing the default password 
      ansible.builtin.shell: echo "ALTER USER 'root'@'localhost' IDENTIFIED BY 'RoboShop@1'" | mysql  --connect-expired-password -uroot -p"{{DEFAULT_PASSWORD}}"

    - name: Uninstalling Password Validate Plugin
      ansible.builtin.shell: echo "uninstall plugin validate_password" | mysql -uroot -pRoboShop@1 
```
if any of the tasks in block fails then, all the tasks of the rescue will be executed.


15) What is a BLOCK in ansible ?

```
This helps us to run a block of tasks with a single condition.
```

16) How can we extract a specific string consider a password from a file that's available on a remote host 

```
This can be done by using the slurp module and get the file locally and then run the filters. Here is a classic example on using this scenario

- name: Extracting {{COMPONENT}} password file 
  ansible.builtin.slurp:
    src: /var/log/mysqld.log
  register: psw_log

- name: validating the password 
  block:
    - name: Get MySQL version with non-default credentials
      community.mysql.mysql_info:
        login_user: root
        login_password: "{{MYSQL_PSW}}"
        filter: version
  rescue:
    - name: Capturing the bash exit code 
      ansible.builtin.set_fact:
        DEFAULT_PASSWORD: "{{psw_log.content|b64decode | regex_findall('.*temporary password.*')| join('') | split(' ')|last}}"
```

17) What's the main purpose of SLURP function in ansible ?

```
slurp module helps in fetching the file on the remote machine and running the commands/filters of your choice on the file locally.
```

18) What is the different between collections vs module ?

```
Both of them are same. From version version 3.0, we are calling the modules as collections.

Also from collections, they have diversified the building, thridparty or community supplied can be seen.
```

19) How can we control the facts not to be collected by Ansible Controller ?

```
facts is nothing but the properties collected by the ansible-controller.

By default facts are collected and if you want to stop them, you can delcare it by using the below mentioned way :

Reference Line : https://github.com/b57-clouddevops/ansible/blob/main/003-facts.yaml#L10
```

20) What is the port number used by ansible ?

```
Ansible is agentless and it works on SSH mechanism and that means it goes by port 22
```

21) What are the packages that needs to be installed on remote nodes that needs to be controlled by ansible ?

```
Ansible is agentless and it works on SSH mechanism and that means it goes by port 22. So you don't need to install anything specific.
```

22) Why ansible is referred as AGENT-lESS
```
it works on SSH mechanism and that means it goes by port 22. So you don't need to install anything specific.
```

23) If there are any sensitive pieces of information on your playbook like API Tokens, what's the strategy that you would follow to encrypt them ?

```
You can use ansible-vault to encrypt the sensitive content. But it's not recommended just to use vault.

But here is the usage on how to use ansible-vault

    $ ansible-vault encrypt_string pswrd1432    ( enter the password to give you the encrypted string and supply the password when you run the playook )

    $ ansible-playbook -i inv -e ansible_user=centos -e ansible_password=xyz123 13-vault.yaml --ask-vault-password

Example Usage :
    https://github.com/b54-clouddevops/ansible/blob/main/13-vault.yaml

```

24) How can extract all the facts gather by ANSIBLE ?

```
* Ansible uses a module called as setup using that we can check the collected facts 

    $ ansible -i inventory all -m setup 
```

25) How do you manage package installations using Ansible?

```
We can use the package module and using this we we can install packages and we can also conditionalize it. Here is teh sameple usage.

https://github.com/b57-clouddevops/ansible/blob/main/010-package.yaml

```

26) What is the difference between register and set_fact? When to use what ?

Both register and set_fact are used in Ansible to manage variables, but they serve different purposes:

```
register:

    Used to capture the output of a module or task.
    Stores the output in a temporary variable accessible within the same playbook or role.
    Useful for processing or referencing module results later in the playbook.
set_fact:

    Explicitly defines a new variable with a specific value.
    The variable becomes available throughout the remaining playbook or role.
    Used to create custom variables for calculations, conditional logic, or passing data between tasks.
```

27) Can we use ansible in dry-run mode ?

```
ansible-playbook robot-dryrun.yaml -e COMPONENT=mongodb -e ansible_user=centos -e ansible_password=xyz123 -e ENV=qa
```

28) How do you handle errors in Ansible playbooks? 
    ``` Use ignore_errors or failed_when to manage task failures.```

29) What is an inventory file in ansible ?

```
This is the file where we mention all the details of the resources that needs configuration managemet
```

30) When to use ansible-playbook vs ansible-pull ?

```
Push Method can be run by using "ansible-playbook"
Push Method can be run by using "ansible-pull -u URL playbbok.yml"

```

31) If your playbook is on DRIVE, can you run it using ansible-pull ?

```
ansible-pull only supports the playbook on git and apart from git none of teh sources are not allowed.
```

32) What are the pre-requisites for ansible to operate using ansible-pull 

```
    1) Ansible to be available on that node that needs to run ansible-pull 
    2) Node should be able to access the playbook on github
```

33) How does Ansible differ from other configuration management tools like Puppet or Chef?

    * Ansible is agentless, meaning it doesn’t require any agents on managed nodes.
    * It uses YAML-based playbooks for defining tasks.
    * Ansible is easy to learn and doesn’t require a master-agent architecture.

34) What is the difference between list vs dictionary vs map object in YAML ?

```
All of them are variable types :

    1) key with a value is referred as Dictionary 
    2) key with multiple values is referred as list 
    3) key with multiple key value pairs is referred as MAP
```

35) How can you leverage Ansible Vault to manage secrets for different environments (e.g., dev, test, prod)?

36) Can we use ANSIBLE to create infrastructure on cloud ? If yes, why it's not at all preferred

37) How can you achieve variable precedence and inheritance in Ansible?

38) Discuss different ways to manage complex data structures (e.g., loops, conditionals) within Ansible playbooks. What are the conditions that you've used in ansible ?

39) Explain me how would you handle this situation, If you have 300 vm's ( Combined ) of Redhat Linux,7,8,9 and ubuntu 18,20,22. Due to the security findings, you were asked to patch the package xyz on all the Redhat 9 machines of the inventory that you have, how can you do that ?

40) Scenario : You have 500+ VM's and you would like to restart all the VM's whose uptime is greater than 100 days

41) Scenario: You need to deploy a complex application with dependencies between different servers. How would you model this in your Ansible playbooks using handlers ?

42) How would you configure secure access for your Ansible control node using SSH key management?

43) Explain best practices for managing secrets and sensitive data within Ansible playbooks using Ansible Vault.

```
Ensure you follow the secretsManager discussed in the session.
```

44) How to run the playbook on debug mode 

```
ansible-playbook robot-dryrun.yaml -e COMPONENT=mongodb -e ansible_user=centos -e ansible_password=xyz123 -e ENV=qa

```


