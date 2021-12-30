# TP-11: JinJa (test mise en prod d’une webapp)

• L’objectif de ce TP est de déployer une application web sur plusieurs de nos serveurs de production à l’aide de Ansible 

NB: L’application devra être déployée de façon dynamique de telle sorte que les pages d’accueil diffèrent fonction du serveur sur lequel on se trouve 

Le site web à déployer se trouve sur le repo suivant :
https://github.com/diranetafen/static-website-example.git

1) Modifiez manuellement des noms de vos serveurs et pensez en modifiez le fichier

/etc/hosts afin d’ajouter la nouvelle entrée


- Serveur Ansible : AnsibleMaster
- Worker01: AnsibleWorker01
- Worker02: AnsibleWorker02

# Avant
```ruby
sudo hostnamectl set-hostname AnsibleMaster
sudo vi /etc/hosts

172.31.8.10 AnsibleMaster
172.31.6.70 AnsibleWorker01
172.31.13.73 AnsibleWorker02
```
# Apres
```ruby
pre_tasks:
    - name: set host name
      command: "sudo hostnamectl set-hostname {{ hostname }}"

    - name: edit the hosts file
      command: "echo '127.0.0.1 {{ hostname }}' >> /etc/hosts"
```

2) Trouvez la méthode la plus efficace afin de déployer cette application à l’aide d’un playbook ansible webapp.yml sur nos hôtes du groupe prod (worker01 et worker02)

`mkdir /home/ubuntu/webapp2`

`vi /home/ubuntu/webapp2/prod.yaml`

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
        ansible_user: ubuntu
        ansible_password: ubuntu
        ansible_ssh_common_args: '-o StrictHostKeyChecking=no'

      hosts:
        worker01:
          ansible_host: 172.31.6.70
          hostname: AnsibleWorker01

        worker02:
          ansible_host: 172.31.13.73
          hostname: AnsibleWorker02
```

# Création du template index.html.j2
`mkdir /home/ubuntu/webapp2/templates`

`vi /home/ubuntu/webapp2/templates/index.html.j2`

```ruby
.
.
<h1>Dimension : {{ ansible_hostname }}</h1>
.
.
```

# Avec git

`vi /home/ubuntu/webapp2/webapp2.yaml`

```ruby
---
- name: "attribute hostname on Master"
  hosts: ansible
  become: true
  vars:
    hostname: AnsibleMaster
  tasks:
    - name: set host name
      command: "sudo hostnamectl set-hostname {{ hostname }}"

    - name: edit the hosts file
      command: "echo '127.0.0.1 {{ hostname }}' >> /etc/hosts"


- name: "Deploy an webapp in prod env"
  hosts: prod
  become: true
  vars:
    ansible_sudo_pass: ubuntu

  pre_tasks:
    - name: set host name
      command: "sudo hostnamectl set-hostname {{ hostname }}"

    - name: edit the hosts file
      command: "echo '127.0.0.1 {{ hostname }}' >> /etc/hosts"

  tasks:
    - name: "install nginx"
      package:
        name: nginx
        state: present

    - name: "start nginx"
      service:
        name: nginx
        enabled: yes
        state: started

    - name: "copy website files"
      git:
        repo: 'https://github.com/diranetafen/static-website-example.git'
        dest: /var/www/html/
        force: yes

    - name: "delete index.html if exist"
      file:
        path: /var/www/html/index.html
        state: absent

    - name: "modifier un fact"
      set_fact: #Cette action permet de définir les variables associées à l'hôte courant.
         ansible_hostname: "{{ hostname }}"

    - name: "copy index file"
      template:
        src: /home/ubuntu/webapp2/templates/index.html.j2
        dest: /var/www/html/index.html
```

`ansible-playbook -i /home/ubuntu/webapp2/prod.yaml /home/ubuntu/webapp2/webapp2.yaml`

```ruby
PLAY [attribute hostname on Master] ********************************************************************************************************************

TASK [Gathering Facts] *********************************************************************************************************************************
ok: [localhost]

TASK [set host name] ***********************************************************************************************************************************
changed: [localhost]

TASK [edit the hosts file] *****************************************************************************************************************************
changed: [localhost]

PLAY [Deploy an webapp in prod env] ********************************************************************************************************************

TASK [Gathering Facts] *********************************************************************************************************************************
ok: [worker02]
ok: [worker01]

TASK [set host name] ***********************************************************************************************************************************
changed: [worker01]
changed: [worker02]

TASK [edit the hosts file] *****************************************************************************************************************************
changed: [worker01]
changed: [worker02]

TASK [install nginx] ***********************************************************************************************************************************
ok: [worker01]
ok: [worker02]

TASK [start nginx] *************************************************************************************************************************************
ok: [worker02]
ok: [worker01]

TASK [copy website files] ******************************************************************************************************************************
changed: [worker01]
changed: [worker02]

TASK [delete index.html if exist] **********************************************************************************************************************
changed: [worker02]
changed: [worker01]

TASK [modifier un fact] ********************************************************************************************************************************
ok: [worker01]
ok: [worker02]

TASK [copy index file] *********************************************************************************************************************************
changed: [worker02]
changed: [worker01]

PLAY RECAP *********************************************************************************************************************************************
localhost                  : ok=3    changed=2    unreachable=0    failed=0    skipped=0    rescued=0    ignored=0
worker01                   : ok=9    changed=5    unreachable=0    failed=0    skipped=0    rescued=0    ignored=0
worker02                   : ok=9    changed=5    unreachable=0    failed=0    skipped=0    rescued=0    ignored=0

```

3) Lors du déploiement de l’application, la page d’accueil doit varier fonction de l’hote (le titre doit être Dimension : <hostname>) donc soit Dimension : AnsibleWorker01 ou Dimension : AnsibleWorker02

```ruby
 curl http://3.92.42.244/ | grep h1

  <h1>Dimension : AnsibleWorker01</h1>

 curl http://3.216.79.121/ | grep h1
 
   <h1>Dimension : AnsibleWorker02</h1>
```

