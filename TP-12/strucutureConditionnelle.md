# TP-12: Structure conditionnelle 

• Ecrivez un playbook ansible qui permet d’installer nginx, git sur des  achines distantes à l’aide d’une seule tache

1) Ecrivez le playbook qui permettra à l’aide d’une seule tache d’installer plusieurs applications sur un hôte distant
2) Pensez à mettre également une structure conditionnelle dans votre playbook permettant de choisir le bon module fonction de la distribution Linux (Ubuntu ou Centos)
3) Testez votre playbook et consommez vos applications

`mkdir /home/ubuntu/webapp3/group_vars`

`vi /home/ubuntu/webapp3/prod.yaml`
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

`vi /home/ubuntu/webapp3/group_vars/prod.yaml`
```ruby
env: prod
ansible_user: ubuntu
ansible_password: ubuntu
ansible_ssh_common_args: '-o StrictHostKeyChecking=no'
```

# lancer le playbook d'installation

`vi /home/ubuntu/webapp3/install.yaml`

```ruby
---
- name: "Install nginx and git in client with OS ubuntu and worker01 in group prod"
  hosts: prod
  become: true
  
  tasks:
    - name: "Install nginx"
      apt:
        name: "{{ item }}"
        state: present
      when: ansible_distribution == "Ubuntu" and env == "worker01" # ou inventory_hostname = "worker01"
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


`vi /home/ubuntu/webapp3/uninstall.yaml`

```ruby
---
- name: "Desinstall nginx and git in hosts worker02"
  hosts: worker02
  become: true

  tasks:
    - name: "desinstall nginx"
      when: ansible_distribution == "Ubuntu"
      apt:
        name: nginx
        state: absent
        purge: yes
        autoremove: yes
```
##  lancer le playbook d'installation
`ansible-playbook -i /home/ubuntu/webapp3/prod.yaml /home/ubuntu/webapp3/install.yaml`

```ruby
PLAY [Install nginx and git in client with OS ubuntu and worker01 in group prod] ***********************************************************************

TASK [Gathering Facts] *********************************************************************************************************************************
ok: [worker01]
ok: [worker02]

TASK [Install nginx] ***********************************************************************************************************************************
skipping: [worker02] => (item=nginx)
skipping: [worker02] => (item=git)
ok: [worker01] => (item=nginx)
ok: [worker01] => (item=git)

TASK [start nginx] *************************************************************************************************************************************
ok: [worker01]
ok: [worker02]

TASK [create index file for nginx] *********************************************************************************************************************
changed: [worker01]
changed: [worker02]

PLAY RECAP *********************************************************************************************************************************************
worker01                   : ok=4    changed=1    unreachable=0    failed=0    skipped=0    rescued=0    ignored=0
worker02                   : ok=3    changed=1    unreachable=0    failed=0    skipped=1    rescued=0    ignored=0
```

## lancer le playbook de désinstallation
`ansible-playbook -i /home/ubuntu/webapp3/prod.yaml /home/ubuntu/webapp3/uninstall.yaml`
```ruby
PLAY [Desinstall nginx and git in hosts worker02] ******************************************************************************************************

TASK [Gathering Facts] *********************************************************************************************************************************
ok: [worker02]

TASK [desinstall nginx] ********************************************************************************************************************************
changed: [worker02]

PLAY RECAP *********************************************************************************************************************************************
worker02                   : ok=2    changed=1    unreachable=0    failed=0    skipped=0    rescued=0    ignored=0
```