# TP-13: Déployer un conteneur docker

• Il est question pour ce TP de voir comment deployer un conteneur docker via Ansible from scratch
• On l'appliquera à l'image de mario https://hub.docker.com/r/pengbai/docker-supermario
• On va créer un nouveau répertoire de travail, nommé TP_mario dans lequel On reprendra l'inventaire du TP précédent.
• Il faut créer trois playbook :

• docker.yml permettant d'installer docker
• mario.yml permettant de déployer le conteneur Mario
• deploy_mario.yaml qui appelle les deux autres

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

# Playbook permettant d'installer docker

`vi /home/ubuntu/TP_mario/deploy_mario.yaml `

```ruby
---
- name: Include task Install docker
  import_playbook: /home/ubuntu/TP_mario/docker.yaml

- name: Include task Deploy container
  import_playbook: /home/ubuntu/TP_mario/mario.yaml
```

# Playbook permettant d'installer docker

`vi /home/ubuntu/TP_mario/docker.yaml`

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
```

# Playbook permettant de déployer le conteneur Mario

`vi /home/ubuntu/TP_mario/mario.yaml`

```ruby
---
- name: "Install docker"
  hosts: prod
  become: true
  vars:
    ansible_sudo_pass: ubuntu

  tasks:
    - name: "create container mario"
      command: "docker run --name mario -d -p 8080:8080 pengbai/docker-supermario"
```


   

##  lancer le playbook d'installation
`ansible-playbook -i /home/ubuntu/TP_mario/prod.yaml /home/ubuntu/TP_mario/deploy_mario.yaml`


