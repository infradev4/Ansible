# Mini Projet 2: Créer un rôle docker

Le but ici est d’installer docker sur plusieurs machines distantes à l’aide d’un rôle Ansible.

1) Se servir des méthodes d’installation de docker définies dans la documentation officielle https://docs.docker.com/engine/install/ afin de créer votre propre script d’installation de docker prenant en compte la distribution (Ubuntu ou CentOs) Notez bien que les structures conditionnelles dans ce script devront etre en Jinja (Template) et non de simples script shell.

2) A l’aide de ce script d’installation de docker fonction de la distribution Linux, créez un rôle permettant d’installer non seulement docker sur des machines distantes, mais également l’ensemble de prérequis nécessaires permettant d’utiliser le module docker_container depuis ces machines distantes

3) Testez le role précédemment crée et si OK poussez le vers votre repo Github sous le mom de docker_role

4) Poussez ensuite ce role vers la galaxy ansible et tentez de le consommer depuis la galaxy ansible

# Création du rôle

ubuntu@AnsibleMaster:~$ mkdir roles
ubuntu@AnsibleMaster:~$ cd roles
ubuntu@AnsibleMaster:~$ ansible-galaxy init docker
- Role docker was created successfully


Nous supprimerons ensuite le dossier tests , defaults, vars et handlers qui nous ne serviront à rien pour le moment:
rm -rf roles/docker/{tests,defaults,handlers,vars}

vi docker.yaml
```ruby

-  name:
   hosts: prod
   become: true
   roles: 
     - docker
```

vi hosts.yaml

```ruby
all:
  children:
    ansible:
      hosts:
        localhost:
          ansible_connection: local
          ansible_user: ubuntu
          ansible_password: ubuntu
          hostname: AnsibleMaster
    prod:
      vars:
        env: production

      hosts:
        worker01:
          ansible_host: 172.31.14.210
          ansible_user: ubuntu
          ansible_password: ubuntu
          ansible_ssh_common_args: '-o StrictHostKeyChecking=no'
          hostname: AnsibleWorker01

        worker02:
          ansible_host: 172.31.0.185
          ansible_user: ubuntu
          ansible_password: ubuntu
          ansible_ssh_common_args: '-o StrictHostKeyChecking=no'
          hostname: AnsibleWorker02
```

`vi install_docker.sh.j2`

```ruby
#!/bin/bash
{% if ansible_distribution == "Ubuntu" %}
    sudo apt-get install -y \
    apt-transport-https \
    ca-certificates \
    curl \
    software-properties-common
curl -fsSL https://download.docker.com/linux/ubuntu/gpg | sudo apt-key add -
sudo apt-key fingerprint 0EBFCD88
sudo add-apt-repository \
   "deb [arch=amd64] https://download.docker.com/linux/ubuntu \
   $(lsb_release -cs) \
   stable"
sudo apt-get update
sudo apt-get install -y docker-ce
sudo groupadd docker
sudo usermod -aG docker $USER
sudo systemctl enable docker
{% elif ansible_distribution == "CentOS" %}
   curl -fsSL https://get.docker.com -o get-docker.sh
   sudo sh get-docker.sh
   sudo groupadd docker
  sudo usermod -aG docker $USER
  sudo systemctl enable docker
{% else %}
   echo 'Distribution pas prise en charge'
{% endif %}
```

roles\docker_role\tasks

`vi main.yaml`

- name: install python pip for distribution Ubuntu
  apt:
    name: python3-pip
    state: present
  when: ansible_distribution == "Ubuntu"

- name: install docker-py module for distribution Ubuntu
  pip:
    name: docker-py
    state: present
  when: ansible_distribution == "Ubuntu"

- name: install python pip for distribution Centos
  yum:
    name: python3-pip
    state: present
  when: ansible_distribution == "CentOS"

- name: install docker-py module for distribution Centos
  pip:
    name: docker-py
    state: present
  when: ansible_distribution == "CentOS"

- name: Copy Script files
  template:
    src: "install_docker.sh.j2"
    dest: "/home/ubuntu/install_docker.sh"




vi docker/galaxy.yaml

---
namespace: community
name: docker_role
version: 1.0.0
readme: README.md
authors:
  - oussama ZAID
description: Install docker_container for Ansible
license_file: COPYING
tags:
  - v1
repository: https://github.com/infradev4/roleDocker.git
documentation: https://github.com/infradev4/roleDocker.git
homepage:https://github.com/infradev4/roleDocker.git
issues: https://github.com/infradev4/roleDocker.git
build_ignore:
  - .gitignore


ansible-playbook -i hosts.yaml docker.yaml