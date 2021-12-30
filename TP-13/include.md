# TP-13: Template
Le but est de refaire le TP précedent en utilisant le concep du include
1) Il faudra créer deux playbook ansible (main.yml et install.yml)
2) Le playbook install.yml devra contenir les actions nécessaires d’installation des applications
3) Le playbook main devra contenir une seule action qui appelera le template (include) install.yml
4) Pensez à appliquer la boucle à la tache principale dans qui sera définie dans le main.yml afin que cette dernière s’applique au deux taches conditionnelles de notre playbook install.yml
5) Le but ici étant de vous montrer comment s’y prendre pour appliquer des structure conditionnelles ou bloc de répétition à plusieurs taches en simultané

`mkdir /home/ubuntu/webapp4/group_vars`

`vi /home/ubuntu/webapp4/prod.yaml`
```ruby
all:
  children:
    ansible:
      hosts:
        localhost:
          ansible_connection: local
          ansible_user: ubuntu
    prod:
      vars:
        env: production

      hosts:
        worker01:
          ansible_host: 172.31.6.70
          env: worker01

        worker02:
          ansible_host: 172.31.13.73
          
```

`vi /home/ubuntu/webapp4/group_vars/prod.yaml`
```ruby
env: prod
ansible_user: ubuntu
ansible_password: ubuntu
ansible_ssh_common_args: '-o StrictHostKeyChecking=no'
```

# lancer le playbook d'installation

`vi /home/ubuntu/webapp4/main.yaml`

```ruby
---
- name: "Install nginx and git with include task"
  hosts: prod
  become: true
  
  tasks:

    - name: Include task Install nginx
      include_tasks: /home/ubuntu/webapp4/install.yaml
      when: ansible_distribution == "Ubuntu"  
      loop:
        - nginx
        - git
      
    
    - name: "start nginx"
      service:
        name: nginx
        enabled: yes
        state: started

    - name: create index file for nginx
      copy:
        dest: /var/www/html/index.html
        content: "Bonjour Oussama"
```

## ou

```ruby
---
- name: "Install nginx and git with include task"
  hosts: prod
  become: true
  
  tasks:

    - name: Include task Install nginx
      include_tasks: /home/ubuntu/webapp4/install.yaml
      when: ansible_distribution == "Ubuntu"  
      loop:
        - nginx
        - git
```
`vi /home/ubuntu/webapp4/install.yaml`

```ruby
---
- name: "Install nginx"
  apt:
    name: "{{ item }}"
    state: present
```

## ou

`vi /home/ubuntu/webapp4/install.yaml`

```ruby
---
- name: "Install nginx"
  apt:
    name: "{{ item }}"
    state: present

- name: "start nginx"
  service:
    name: nginx
    enabled: yes
    state: started

- name: create index file for nginx
  copy:
    dest: /var/www/html/index.html
    content: "Bonjour Oussama"
```

##  lancer le playbook d'installation
`ansible-playbook -i /home/ubuntu/webapp4/prod.yaml /home/ubuntu/webapp4/main.yaml`
```ruby
PLAY [Install nginx and git with include task] *********************************************************************************************************

TASK [Gathering Facts] *********************************************************************************************************************************
ok: [worker01]
ok: [worker02]

TASK [Include task Install nginx] **********************************************************************************************************************
included: /home/ubuntu/webapp4/install.yaml for worker01, worker02 => (item=nginx)
included: /home/ubuntu/webapp4/install.yaml for worker01, worker02 => (item=git)

TASK [Install nginx] ***********************************************************************************************************************************
ok: [worker01]
changed: [worker02]

TASK [Install nginx] ***********************************************************************************************************************************
ok: [worker01]
ok: [worker02]

TASK [start nginx] *************************************************************************************************************************************
ok: [worker02]
ok: [worker01]

TASK [create index file for nginx] *********************************************************************************************************************
ok: [worker01]
ok: [worker02]

PLAY RECAP *********************************************************************************************************************************************
worker01                   : ok=7    changed=0    unreachable=0    failed=0    skipped=0    rescued=0    ignored=0
worker02                   : ok=7    changed=1    unreachable=0    failed=0    skipped=0    rescued=0    ignored=0
```