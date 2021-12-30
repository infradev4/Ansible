# TP-14 

# Lancez un conteneur docker avec Ansible

Conteneurisez l’application web du TP 8 à l’aide d”un conteneur apache dans lequel vous monterez votre fichier index.html

https://github.com/diranetafen/static-website-example.git


`mkdir -p webappApache/{templates,group_vars}`

`vi /home/ubuntu/webappApache/group_vars/prod.yaml`

`vi /home/ubuntu/webappApache/prod.yaml`
```ruby
---
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

`vi /home/ubuntu/webappApache/group_vars/prod.yaml`
```ruby
---
env: prod
ansible_user: ubuntu
ansible_password: ubuntu
ansible_ssh_common_args: '-o StrictHostKeyChecking=no'
```

# Playbook permettant d'installer docker

Les exigences ci-dessous sont nécessaires sur l'hôte qui exécute ce module. python >= 2.6 docker-py >= 0.3.0

python3-pip
docker-py

`vi /home/ubuntu/webappApache/docker.yaml`

```ruby
---
- name: "Install docker"
  hosts: prod
  become: true
  vars:
    ansible_sudo_pass: ubuntu

  tasks:
    - name: Download Install docker script
      get_url:
        url: "https://get.docker.com"
        dest: /home/ubuntu/get-docker.sh

    - name: run script to install docker
      command: "sh /home/ubuntu/get-docker.sh"

    - name: give the privilège to ubuntu
      user:
        name: ubuntu
        append: yes
        groups:
          - docker

    - name: install python pip
      apt:
        name: python3-pip
        state: present
      when: ansible_distribution == "Ubuntu"

    - name: install docker-py module
      pip:
        name: docker-py
        state: present
```

# Playbook déployer conteneur Apache avec static-website

`vi /home/ubuntu/webappApache/apache.yaml`

```ruby
---
- name: "Install container apache with static-website"
  hosts: prod
  become: true
  vars:
    ansible_sudo_pass: ubuntu

  tasks:
    - name: "Create directory"
      file:
        path: "/home/ubuntu/static-website"
        state: directory

    - name: "Git Clone"
      git:
        repo: "https://github.com/diranetafen/static-website-example.git"
        dest: "/home/ubuntu/static-website"
        force: yes

    - name: "Copy Index File From Template"
      template:
      #  <h1>Dimension : {{ ansible_hostname }}</h1>
        src: /home/ubuntu/webappApache/templates/index.html.j2
        dest: /home/ubuntu/static-website/index.html

    - name: "Create httpd container"
      docker_container:
        name: webapp
        image: httpd
        ports:
          - "8081:80"
        volumes:
          - "/home/ubuntu/static-website:/usr/local/apache2/htdocs/"
```


# Playbook permettant de déployer docker Container avec Appache 

`vi /home/ubuntu/webappApache/deploy_apache.yaml`

```ruby
---
- name: Include playbook Install docker
  import_playbook: /home/ubuntu/webappApache/docker.yaml

- name: Include playbook Deploy container apache
  import_playbook: /home/ubuntu/webappApache/apache.yaml   
```

##  Lancer le playbook d'installation
cd /home/ubuntu/webappApache
`ansible-playbook -i /home/ubuntu/webappApache/prod.yaml /home/ubuntu/webappApache/deploy_apache.yaml`

`ubuntu@AnsibleMaster:~/webappApache$` ansible-playbook -i prod.yaml deploy_apache.yaml
```ruby
PLAY [Install docker] **********************************************************************************************************************************************************************************

TASK [Gathering Facts] *********************************************************************************************************************************************************************************
ok: [worker01]
ok: [worker02]

TASK [Download Install docker script] ******************************************************************************************************************************************************************
ok: [worker01]
ok: [worker02]

TASK [run script to install docker] ********************************************************************************************************************************************************************
changed: [worker01]
changed: [worker02]

TASK [give the privilège to ubuntu] ********************************************************************************************************************************************************************
ok: [worker01]
ok: [worker02]

TASK [install python pip] ******************************************************************************************************************************************************************************
ok: [worker01]
ok: [worker02]

TASK [install docker-py module] ************************************************************************************************************************************************************************
ok: [worker01]
ok: [worker02]

PLAY [Install container apache with static-website] ****************************************************************************************************************************************************

TASK [Gathering Facts] *********************************************************************************************************************************************************************************
ok: [worker01]
ok: [worker02]

TASK [Create directory] ********************************************************************************************************************************************************************************
ok: [worker02]
ok: [worker01]

TASK [Git Clone] ***************************************************************************************************************************************************************************************
changed: [worker01]
changed: [worker02]

TASK [Copy Index File From Template] *******************************************************************************************************************************************************************
changed: [worker01]
changed: [worker02]

TASK [Create httpd container] **************************************************************************************************************************************************************************
ok: [worker01]
ok: [worker02]

PLAY RECAP *********************************************************************************************************************************************************************************************
worker01                   : ok=11   changed=3    unreachable=0    failed=0    skipped=0    rescued=0    ignored=0
worker02                   : ok=11   changed=3    unreachable=0    failed=0    skipped=0    rescued=0    ignored=0
```


