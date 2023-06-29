#### Methods of Looping in Ansible

>#### 1. Looping with loop Keyword  
>Starting from Ansible 2.5, a new loop syntax using the loop keyword was introduced.  
>It provides more flexibility and allows you to loop over various data structures, including lists, dictionaries, and strings. Here's an example:  
  
>```  
>- name: Create users using loop keyword  
>  user:   
>    name: "{{ item.name }}"  
>    state: "{{ item.state }}"  
>  loop:  
>    - { name: user1, state: present }  
>    - { name: user2, state: absent }  
>```
>
>In the above example, the user module is used to create users, and the loop keyword is used to iterate over a list of user dictionaries. Each user is created with the specified name and state.   
>
>#### 2. Specialized Loops  
>Ansible provides several specialized loops that allow you to handle specific scenarios. These loops are denoted by the with_* prefix. Let's explore some commonly used specialized loops:    
> 
>#### a. with_dict Loop   
>
>The with_dict loop iterates over a dictionary's keys and values. Here's an example:  
>
>```
>- name: Configure network interfaces using with_dict loop
>  template:
>    src: interface.j2
>    dest: "/etc/network/interfaces.d/{{ item.key }}"
>  loop: "{{ interfaces }}"
>```
>  
>In the above example, the template module is used to configure network interfaces.   
>The with_dict loop iterates over the interfaces dictionary, where each key represents the interface name and the corresponding value holds configuration details.   
>
>#### b. with_file Loop
>
>The with_file loop iterates over the lines of a file. Here's an example:  
>
>```
>- name: Set up firewall rules using with_file loop
>  ufw:
>    rule: "{{ item }}"
>  loop: "{{ lookup('file', 'firewall_rules.txt').splitlines() }}"
>```
>In the above example, the ufw module is used to set up firewall rules. The with_file loop reads the contents of the firewall_rules.txt file, splits it into lines, and iterates over each line to configure the firewall rule.  
>  
>#### c. with_sequence Loop  
>  
>The with_sequence loop iterates over a range of numbers. Here's an example:  
>
>```
>- name: Create multiple directories using with_sequence loop
>  file:
>    path: "/data/directory{{ item }}"
>    state: directory
>  loop: "{{ range(1, 5) | list }}"
>```
>In the above example, the file module is used to create multiple directories. The with_sequence loop generates a sequence of numbers and iterates over each number to create a directory with the corresponding path.  
>  
>#### d. with_nested Loop
>  
>The with_nested loop allows you to iterate over nested loops. Here's an example:  
>```
>- name: Create user directories using with_nested loop
>  file:  
>    path: "/home/{{ item.0 }}/{{ item.1 }}"
>    state: directory
>  loop: "{{ users }}"
>  with_nested:
>    - ["user1", "user2"]
>    - ["documents", "pictures"]
>```
>  
>In the above example, the file module is used to create user directories.
>The with_nested loop iterates over the users list and performs a nested loop with the values specified in the nested lists. 
>It creates directories for each user in the specified subdirectories.
>  
>#### Recommendation
>  
>The choice of the loop method depends on the specific requirements and the data structure you need to iterate over.
>
>If you have a simple list of items, the traditional with_items loop can still be used, but it is recommended to migrate to the newer loop keyword for more flexibility.
>If you need to iterate over complex data structures, such as dictionaries or files, the specialized with_* loops provide a more suitable approach.
>Take advantage of the with_nested loop when you need to iterate over multiple nested loops.
>
>#### Migrating from with_X to loop  
  
>In most cases, loops work best with the loop keyword instead of with_X style loops. The loop syntax is usually best expressed using filters instead of more complex use of query or lookup.  
  
>These examples show how to convert many common with_ style loops to loop and filters.  
  
>#### with_list

>with_list is directly replaced by loop.
>```  
>- name: with_list
>  ansible.builtin.debug:
>    msg: "{{ item }}"
>  with_list:
>    - one
>    - two
>
>- name: with_list -> loop
>  ansible.builtin.debug:
>    msg: "{{ item }}"
>  loop:
>    - one
>    - two
>with_items
>```
>with_items is replaced by loop and the flatten filter.
>  
- name: with_items
  ansible.builtin.debug:
    msg: "{{ item }}"
  with_items: "{{ items }}"

- name: with_items -> loop
  ansible.builtin.debug:
    msg: "{{ item }}"
  loop: "{{ items|flatten(levels=1) }}"
with_indexed_items

with_indexed_items is replaced by loop, the flatten filter and loop_control.index_var.

- name: with_indexed_items
  ansible.builtin.debug:
    msg: "{{ item.0 }} - {{ item.1 }}"
  with_indexed_items: "{{ items }}"

- name: with_indexed_items -> loop
  ansible.builtin.debug:
    msg: "{{ index }} - {{ item }}"
  loop: "{{ items|flatten(levels=1) }}"
  loop_control:
    index_var: index
with_flattened

with_flattened is replaced by loop and the flatten filter.

- name: with_flattened
  ansible.builtin.debug:
    msg: "{{ item }}"
  with_flattened: "{{ items }}"

- name: with_flattened -> loop
  ansible.builtin.debug:
    msg: "{{ item }}"
  loop: "{{ items|flatten }}"
with_together

with_together is replaced by loop and the zip filter.

- name: with_together
  ansible.builtin.debug:
    msg: "{{ item.0 }} - {{ item.1 }}"
  with_together:
    - "{{ list_one }}"
    - "{{ list_two }}"

- name: with_together -> loop
  ansible.builtin.debug:
    msg: "{{ item.0 }} - {{ item.1 }}"
  loop: "{{ list_one|zip(list_two)|list }}"
Another example with complex data

- name: with_together -> loop
  ansible.builtin.debug:
    msg: "{{ item.0 }} - {{ item.1 }} - {{ item.2 }}"
  loop: "{{ data[0]|zip(*data[1:])|list }}"
  vars:
    data:
      - ['a', 'b', 'c']
      - ['d', 'e', 'f']
      - ['g', 'h', 'i']
with_dict

with_dict can be substituted by loop and either the dictsort or dict2items filters.

- name: with_dict
  ansible.builtin.debug:
    msg: "{{ item.key }} - {{ item.value }}"
  with_dict: "{{ dictionary }}"

- name: with_dict -> loop (option 1)
  ansible.builtin.debug:
    msg: "{{ item.key }} - {{ item.value }}"
  loop: "{{ dictionary|dict2items }}"

- name: with_dict -> loop (option 2)
  ansible.builtin.debug:
    msg: "{{ item.0 }} - {{ item.1 }}"
  loop: "{{ dictionary|dictsort }}"
with_sequence

with_sequence is replaced by loop and the range function, and potentially the format filter.

- name: with_sequence
  ansible.builtin.debug:
    msg: "{{ item }}"
  with_sequence: start=0 end=4 stride=2 format=testuser%02x

- name: with_sequence -> loop
  ansible.builtin.debug:
    msg: "{{ 'testuser%02x' | format(item) }}"
  loop: "{{ range(0, 4 + 1, 2)|list }}"
The range of the loop is exclusive of the end point.

with_subelements

with_subelements is replaced by loop and the subelements filter.

- name: with_subelements
  ansible.builtin.debug:
    msg: "{{ item.0.name }} - {{ item.1 }}"
  with_subelements:
    - "{{ users }}"
    - mysql.hosts

- name: with_subelements -> loop
  ansible.builtin.debug:
    msg: "{{ item.0.name }} - {{ item.1 }}"
  loop: "{{ users|subelements('mysql.hosts') }}"
with_nested/with_cartesian

with_nested and with_cartesian are replaced by loop and the product filter.

- name: with_nested
  ansible.builtin.debug:
    msg: "{{ item.0 }} - {{ item.1 }}"
  with_nested:
    - "{{ list_one }}"
    - "{{ list_two }}"

- name: with_nested -> loop
  ansible.builtin.debug:
    msg: "{{ item.0 }} - {{ item.1 }}"
  loop: "{{ list_one|product(list_two)|list }}"
with_random_choice

with_random_choice is replaced by just use of the random filter, without need of loop.

- name: with_random_choice
  ansible.builtin.debug:
    msg: "{{ item }}"
  with_random_choice: "{{ my_list }}"

- name: with_random_choice -> loop (No loop is needed here)
  ansible.builtin.debug:
    msg: "{{ my_list|random }}"
  tags: random
