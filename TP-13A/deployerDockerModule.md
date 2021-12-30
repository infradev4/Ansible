`mkdir /home/ubuntu/TP_mario/group_vars`

`vi /home/ubuntu/TP_mario/prod.yaml`
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

`vi /home/ubuntu/TP_mario/group_vars/prod.yaml`
```ruby
env: prod
ansible_user: ubuntu
ansible_password: ubuntu
ansible_ssh_common_args: '-o StrictHostKeyChecking=no'
```

# Playbook permettant de déployer 

`vi /home/ubuntu/TP_mario/deploy_marioV2.yaml `

```ruby
---
- name: "Install docker with import playbook"
  hosts: prod
  become: true
  
  tasks:

    - name: Include task Install docker
      Import_playbook: /home/ubuntu/TP_mario/dockerv2.yaml

    - name: Include task Deploy container
      Import_playbook: /home/ubuntu/TP_mario/marioV2.yaml
      
```

# Playbook permettant d'installer docker

Les exigences ci-dessous sont nécessaires sur l'hôte qui exécute ce module. python >= 2.6 docker-py >= 0.3.0

python3-pip
docker-py

`vi /home/ubuntu/TP_mario/dockerv2.yaml`

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

# Playbook permettant de déployer le conteneur Mario

`vi /home/ubuntu/TP_mario/marioV2.yaml`

```ruby
---
- name: "Install docker"
  hosts: prod
  become: true
  vars:
    ansible_sudo_pass: ubuntu

  tasks:
    - name: "create container mario"
      docker_container:
        name: mario
        image: pengbai/docker-supermario
        ports:
          - "8600:8080"

   

##  lancer le playbook d'installation
`ansible-playbook -i /home/ubuntu/TP_mario/prod.yaml /home/ubuntu/TP_mario/deploy_marioV2.yaml`