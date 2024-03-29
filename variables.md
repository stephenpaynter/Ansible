#### Variable Name Rules

Ansible has a strict set of rules to create valid variable names. Variable names can contain only letters, numbers, and underscores and must start with a letter or underscore.   
Some strings are reserved for other purposes and aren’t valid variable names, such as Python Keywords or Playbook Keywords.  

>#### Best Practices for Using Variables and Variable Declaration in Ansible  

>Consistent Variable Naming:
>  
>Maintaining a consistent naming convention for variables improves readability and reduces confusion.   
>Use lowercase letters with underscores (_) to separate words in variable names. For example:  

>#### Bad practice
>myVariable = "value"

>#### Good practice
>my_variable = "value"

>Use the following standards for initial variable declaration, 
>including var: register and set_fact
>Underscore prefix where variables will be available only inside the role
>
>```
>- variable: _variable
>```
>and No prefix where variables are to be exported outside of the play or role.
>
>```
>- variable: variable
>```

>If a variable is set using set_fact then it could start with and underscore.
>This would donote a varibale is being set within a play and not gathered from a play
>Consistency is key, so choose a naming convention and apply it consistently throughout your playbooks.  

>#### Argument Spec: 

>Argument spec in Ansible is a mechanism for defining and enforcing a structure >for the inputs that your tasks and roles require. 
>It allows you to specify the expected arguments, their data types, default   >values, and additional constraints.   
>By defining argument spec, you can improve the reusability,   
>clarity, and reliability of your Ansible code.  

>Example of an argument spec file (my_task_args.yml):
>
>```
>---
>
>argument_specs:
>  main:
>    short_description: "Install and configure ntp on your system."
>    description: >
>      Install all required packages, configure required files and manage services to use NTP services.
>    author: Robert de Bock
>    options:
>      ntp_state:
>        type: "str"
>        default: started
>        description: "The state of the NTP service."
>        choices:
>          - started
>          - stopped
>      ntp_enabled:
>        type: "bool"
>        default: yes
>        description: "The state of the NTP service at boot."
>      ntp_interfaces:
>        type: "list"
>        elements: str
>        default:
>          - address: "127.0.0.1"
>        description: "A list of IP addresses to listen on."
>      ntp_pool:
>        type: "list"
>        elements: str
>        default:
>          - name: "0.pool.ntp.org iburst"
>          - name: "1.pool.ntp.org iburst"
>          - name: "2.pool.ntp.org iburst"
>          - name: "3.pool.ntp.org iburst"
>        description: "A list of NTP pools and their options."
>      ntp_servers:
>        type: "list"
>        elements: str
>        required: no
>        description: "A list of NTP servers and their options."
>      ntp_timezone:
>        type: "str"
>        default: "Europe/Amsterdam"
>        description: "The timezone."
>```
>
> Place any Argument Spec in the Meta file.  
>#### Variable Declaration:    
Ansible provides multiple methods for declaring variables.   
>Let's explore some best practices for each approach:  

>#### Inline Variable Declaration:
>Inline variable declaration can be useful for simple, short-lived variables.   
>However, it is recommended to limit its usage, especially for complex or lengthy expressions. Instead, prefer declaring variables using the vars keyword within a playbook or role. For example:  

>```
>- name: Inline variable declaration (avoid for complex expressions)
>  hosts: localhost
>  gather_facts: false
>  tasks:
>    - debug:
>        var: my_variable
>      vars:
>        my_variable: "value"
>```
>  
>#### Playbook-Level Variable Declaration:
>
>Defining variables at the playbook level using the vars keyword makes them accessible across all tasks within the playbook.  
>This approach helps maintain consistency and simplifies playbook maintenance.  

>For example:  

>```
>- name: Playbook-level variable declaration
>  hosts: localhost
>  gather_facts: false
>  vars:
>    my_variable: "value"
>  tasks:
>    - debug:
>        var: my_variable
>```

>#### Role-Level Variable Declaration:  
>When working with roles, declare variables within the defaults/main.yml file of the role.  
>This ensures that the variables are accessible within the role and can be easily overridden by users.   

>For example:
>  
>```
># Role: my_role
># Path: roles/my_role/defaults/main.yml
>
>my_variable: "default_value"
>```

>#### Variable Precedence and Overriding:  
>Ansible follows a specific order of precedence when resolving variables. Understanding this order is essential to ensure desired variable values.  
>The order of precedence, from highest to lowest, is as follows:     
>Variables defined explicitly in the task.    
>Variables passed through the vars section of the playbook.  
>Variables defined within the role's defaults/main.yml.  
>Variables provided through inventory, including host_vars and group_vars.  
>To override variables at runtime, pass them as extra variables using the -e flag or through an inventory file.   

>For example:
>```
>ansible-playbook my_playbook.yml -e "my_variable=new_value"  
>```
>  
>#### Variable Encryption:
>Sensitive information such as passwords, API keys, or private keys should be encrypted when stored in variables. 
>Ansible provides the ansible-vault command to encrypt and decrypt files containing sensitive data. Avoid storing plaintext sensitive information in variable files or playbooks.  
  
>#### Variable Files and Organization:  
>Organizing variable files can greatly improve playbook maintenance and readability. Consider the following recommendations:  
>Use separate variable files for different environments or specific roles.  
>Group related variables within the same file.  
>Use the group_vars and host_vars directories to store variables specific to groups or individual hosts, respectively.  
