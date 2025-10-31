# changes in failure.yml

## failure.yml
```
---
- name: Task Failure lab
  hosts: eoc-node1
  vars:
    web_package: apache
    db_package: mariadb-server
    db_service: mariadb
  tasks:
    - name: Install {{ web_package }} package
      ansible.builtin.apt:
        name: "{{ web_package }}"
        state: present
    - name: Install {{ db_package }} package
      ansible.builtin.apt:
        name: "{{ db_package }}"
        state: present
```
## adding ignore block
```
- name: Task Failure lab
  hosts: eoc-node1
  vars:
    web_package: apache
    db_package: mariadb-server
    db_service: mariadb
  tasks:
    - name: Install {{ web_package }} package
      ansible.builtin.apt:
        name: "{{ web_package }}"
        state: present
      ignore_errors: yes
    - name: Install {{ db_package }} package
      ansible.builtin.apt:
        name: "{{ db_package }}"
        state: present
```

## adding block task
```
- name: Task Failure lab
  hosts: eoc-node1
  vars:
    web_package: apache
    db_package: mariadb-server
    db_service: mariadb
  tasks:
    - name: Install {{ web_package }} package
      block:
        - name: Install {{ web_package }} package
            ansible.builtin.apt:
              name: "{{ web_package }}"
              state: present
      rescue:
        - name: Install {{ db_package }} package
          ansible.builtin.apt:
            name: "{{ db_package }}"
            state: present
      always:
        - name: start {{ db_service }} 
          ansible.builtin.service:
            name: "{{ db_service }}"
            state: started
```

## using correct package name
```
---
- name: Task Failure lab
  hosts: eoc-node1
  vars:
    web_package: apache2
    db_package: mariadb-server
    db_service: mariadb
  tasks:
    - name: Install {{ web_package }} package
      block:
        - name: Install {{ web_package }} package
          ansible.builtin.apt:
            name: "{{ web_package }}"
            state: present
      rescue:
        - name: Install {{ db_package }} package
          ansible.builtin.apt:
            name: "{{ db_package }}"
            state: present
      always:
        - name: start {{ db_service }} 
          ansible.builtin.service:
            name: "{{ db_service }}"
            state: started
```

## with two extra tasks
```
---
- name: Task Failure lab
  hosts: eoc-node1
  vars:
    web_package: apache2
    db_package: mariadb-server
    db_service: mariadb
  tasks:
    - name: check the local time
      command: date
      register: command_result
    - name: print local time
      debug:
        var: command_result.stdout
    - name: Install {{ web_package }} package
      block:
        - name: Install {{ web_package }} package
          ansible.builtin.apt:
            name: "{{ web_package }}"
            state: present
      rescue:
        - name: Install {{ db_package }} package
          ansible.builtin.apt:
            name: "{{ db_package }}"
            state: present
      always:
        - name: start {{ db_service }} 
          ansible.builtin.service:
            name: "{{ db_service }}"
            state: started
```

## with surpass change in task1
```
---
- name: Task Failure lab
  hosts: eoc-node1
  vars:
    web_package: apache2
    db_package: mariadb-server
    db_service: mariadb
  tasks:
    - name: check the local time
      command: date
      register: command_result
      changed_when: false
    - name: print local time
      debug:
        var: command_result.stdout
    - name: Install {{ web_package }} package
      block:
        - name: Install {{ web_package }} package
          ansible.builtin.apt:
            name: "{{ web_package }}"
            state: present
      rescue:
        - name: Install {{ db_package }} package
          ansible.builtin.apt:
            name: "{{ db_package }}"
            state: present
      always:
        - name: start {{ db_service }}
          ansible.builtin.service:
            name: "{{ db_service }}"
            state: started
```

