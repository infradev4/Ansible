# Ansible

Action à l'aide de commandes impérative ou avec des play books.

Play books importés dans d'autres play books.

Configuration automatique des applications sur une ferme de serveurs

Pas besoin d'agent, connexion SSH et demande Python

Il peut être couplé avec Terraform

Il intervient sur des intras existants. Terraform crée des infras

# Liste des avantages d'Ansible :
```yaml
Gratuit: Ansible est un outil open source.

Simple : Ansible utilise une syntaxe écrite en YAML. Aucune compétence en programmation particulière n'est nécessaire pour créer les playbooks d'Ansible. Il est également simple à installer.

Puissant: Ansible vous permet de modéliser des workflows très complexes.

Flexible: Ansible vous fournit des centaines de modules prêts à l'emploi pour gérer vos tâches, quel que soit l'endroit où ils sont déployés. Vous pouvez réutiliser le même playbook sur un parc de machines Red Hat, Ubuntu ou autres.

Agentless : vous n'avez pas besoin d'installer d'autres logiciels ou d'ouvrir des ports de pare-feu supplémentaires sur les systèmes clients que vous souhaitez automatiser. Ansible réduit encore l'effort requis pour que votre équipe commence à automatiser immédiatement.

Efficace: Parce que vous n'avez pas besoin d'installer de logiciel supplémentaire, il y a plus de place pour les ressources d'application sur votre serveur.
```

# Que peut faire Ansible?
Ansible peut être utilisé de différentes manières. 

## Déploiement d'applications
Ansible vous permet de déployer rapidement et facilement des applications à plusieurs niveaux. Vous n'aurez pas besoin d'écrire du code personnalisé pour automatiser vos systèmes; vous listez les tâches à effectuer en écrivant un playbook, et Ansible trouvera comment amener vos systèmes à l'état dans lequel vous voulez qu'ils soient. En d'autres termes, vous n'aurez pas à configurer manuellement les applications sur chaque machine . Lorsque vous exécutez un playbook à partir de votre machine de contrôle, Ansible utilisera le protocole SSH pour communiquer avec les hôtes distants et exécuter toutes les tâches (Tasks).

## Orchestration
L'orchestration consiste à amener différentes éléments à interagir ensemble sans incohérence. Par exemple, avec le déploiement d'applications, vous devez gérer non seulement les services frontend, mais également les services backend comme les bases de données, le réseau, le stockage, etc... Vous devez également vous assurer que toutes les tâches sont gérées dans le bon ordre. Grâce à Ansible vous orchestrez les éléments de votre infrastructure à l'aide des playbooks Ansible, et vous pouvez les réutiliser sur différents types de machines, grâce à la portabilité des modules Ansible.

## Conformité et sécurité
Comme pour le déploiement d'applications, des politiques de sécurité de votre entreprise (telles que des règles de pare-feu ou le verrouillage des utilisateurs) peuvent être mises en œuvre avec d'autres processus automatisés. Si vous configurez les détails de sécurité sur la machine de contrôle et exécutez le playbook associé, tous les hôtes distants seront automatiquement mis à jour avec ces détails. Cela signifie que vous n'aurez pas besoin de surveiller chaque machine pour vérifier la conformité de la sécurité en continu manuellement. De plus, tous les identifiants (identifiants et mots de passe des utilisateurs admin) qui sont stockés dans vos playbooks ne sont récupérables en brut par aucun utilisateur.

## Provisionnement du cloud
La première étape de l'automatisation du cycle de vie de vos applications consiste à automatiser l'approvisionnement de votre infrastructure. Avec Ansible, vous pouvez provisionner des plateformes cloud, des hôtes virtualisés, des périphériques réseau et des serveurs physiques.

# Comment fonctionne Ansible
Dans Ansible, il existe deux catégories d'ordinateurs: 

`le nœud maître (master) et les nœuds esclaves (slaves).`

Le nœud maître est une machine sur laquelle est installé l'outil Ansible. Il doit y avoir au moins un nœud maître (nœud maître de sauvegarde est possible).
```txt
Ansible fonctionne en se connectant à vos nœuds en SSH et en y poussant de petits programmes, appelés modules. Ces modules sont définis dans un fichier nommé le Playbook. Le nœud maître, se base sur un fichier d'inventaire qui fournit la liste des hôtes sur lesquels les modules Ansible doivent être exécutés.
```
Ansible exécute ces modules en SSH et les supprime une fois terminé. La seule condition requise pour cette interaction est que votre nœud maître Ansible dispose d'un accès de connexion aux nœuds esclaves. Les clés SSH sont le moyen le plus courant de fournir un accès, mais d'autres formes d'authentification sont également prises en charge.

Voici un schéma qui reprend notre explication :

![Getting Started](/assets/ansible-works.png)

# Machine de contrôle
La machine de contrôle doit être un hôte Linux / Unix (Red Hat Enterprise Linux, Debian, CentOS, OS X, BSD, Ubuntu), et Python 2.7 ou Python 3.5+ sont requis. 

Avec une version Windows 10 Pro, il est possible d’utiliser Ansible avec WSL

# Noeuds gérés
Les noeuds gérés, s’ils sont en Linux/Unix, doivent disposer de `Python 2.6 ou version ultérieure`, mais de préférence aujourd’hui le pré-requis est `Python 3`. 

SSH est alors le mode de connexion préféré.

Depuis sa version 1.7, Ansible peut également `gérer les noeuds Windows`. Dans ce cas, on utilise `PowerShell` à distance de manière native au lieu de SSH.

Le matériel d’infrastructure réseau (“constructeurs”), pare-feux, serveurs de stockage, fournisseurs IaaS, solutions de virtualisation sont supportés via SSH, NETCONF/YANG ou encore via des API HTTP REST.

# Le dossier de projet
- Fichier de configuration
- Inventaire
- Livres de jeu Playbooks

## L’inventaire
Pour démarrer un gestion Ansible, vous avez besoin d’un inventaire Ansible.

Ansible lit les informations sur les machines que vous souhaitez gérer à partir d'un fichier nommé l'inventaire.

Par défaut, Ansible représente les machines qu’il gère à l’aide d’un fichier INI très simple qui place toutes les machines gérées dans des groupes de votre choix. On peut le représenter dans d’autres formats comme YAML.

Une fois que les hôtes d’inventaire sont répertoriés, des variables “d’inventaire”, c’est-à-dire portant sur les cibles de gestion, peuvent leur être attribuées dans des fichiers texte simples (dans un sous-répertoire appelé ‘group_vars/’ ou ‘host_vars/’) ou directement dans le fichier d’inventaire.

Les commandes ansible, ansible-playbook et ansible-inventory, qui permettent d’exploiter Ansible, exigent toujours un inventaire défini.

## Mon inventaire

`vi hosts`
```Ruby
worker01 ansible_host=172.31.6.70 ansible_user=ubuntu ansible_password=ubuntu ansible_ssh_common_args='-o StrictHostKeyChecking=no'
worker02 ansible_host=172.31.13.73 ansible_user=ubuntu ansible_password=ubuntu ansible_ssh_common_args='-o StrictHostKeyChecking=no'
```

# Modules (ping, file, exec etc...)
Ansible fonctionne en se connectant aux noeuds à gérer et en leur envoyant des petits programmes, appelés “modules Ansible”. Ansible exécute ces modules (via SSH par défaut) grâce au protocole JSON sur la sortie standard et les supprime lorsque l’action est terminée.

## Les modules 

• C’est le code python permettant de réaliser une action spécifique
• Ils sont disponibles sur la doc officielle et sur github:

    https://docs.ansible.com/ansible/latest/collections/index_module.html
    https://github.com/ansible/ansible-modules-core/


• On en distingue pour différents contextes :

    • Cloud computing
    • Virtualisation
    • Provisionning
    • Configuration Management
    • Networking
    • Conteurisation etc

## Liste des modules:

* Setup module : 
https://docs.ansible.com/ansible/latest/collections/ansible/builtin/setup_module.html
Appeler automatiquement par le playbook pour recueillir les facts

* File module : 
https://docs.ansible.com/ansible/latest/collections/ansible/builtin/file_module.html
Permet de manager les fichiers sur la cible

* Copy module : 
https://docs.ansible.com/ansible/latest/collections/ansible/builtin/copy_module.html

    Permet de copier des fichier depuis le serveur ansible sur la machine cible, ou depuis un serveur distant vers un autre serveur distant

    Le module fetch permet de faire l’inverse (target -> serveur ansible)

    Pour windows, le module win_copy existe

* Command module : 
https://docs.ansible.com/ansible/latest/collections/ansible/builtin/command_module.html

Permet de lancer des commandes sur le système distant, mais pas dans un shell, ie que $HOME, |, < , >, & etc ne sont pas valides, pour cela il faudrait utiliser le module shell

Pour les machines windows, il existe le module win_command

* Codes couleurs

    Rouge : Echec

    Jaune : Succès avec des changement

    Vert : Succès sans aucun changement

* Idempotence :

Une opération a le même effet qu'on l'applique une ou plusieurs fois.

C’est un des principes d’Ansible via ses modules, l’idée est de définir un état souhaité, si l’état est déjà satisfait alors rien n’est fait

## Plugins
Les Plugins apportent des fonctionnalités complémentaires à Ansible.

- connexions : ssh, winrm, local, …
- inventory : ini, yaml, scripts, liste, …
- become : su, sudo, enable, runas
- cache : Les plugins de cache permettent à Ansible de stocker les faits rassemblés ou d’inventorier les données sources sans avoir à les récupérer à la source.

# Exécution des tâches
Une tâche est l’appel à un module Ansible. Le module Ansible contient localement tout le code utile à l’exécution. Il est donc important de disposer du code à jour des modules.

## Le mode Ad Hoc
Le mode Ad-Hoc permet l’exécution de tâches “ad-hoc” parallèles.

Dès qu’une instance est disponible, on peut lui parler immédiatement, sans aucune configuration supplémentaire, ici sur une instance Red Hat :

`ansible client1.gaelre.eau -m yum -a "name=httpd state=installed"`

# Playbooks (Livres de jeu)
Les livres de jeu (playbooks) sont écrits selon un langage d’automatisation simple et puissant. Les livres de jeu peuvent orchestrer avec précision plusieurs parties d’une topologie d’infrastructure, avec un contrôle très détaillé du nombre de machines à traiter à la fois.

Chaque livres de jeu est composé de une ou plusieurs “séances de jeu (plays)” exposés sous forme d’une liste. Un “livre de jeu” organise des tâches en jeux. Mais il de bonne pratique de l’organiser en “roles”. Un “role” ajoute un niveau d’abstraction dans l’exécution des tâches d’un livre de jeu.

## Exemple 1

Le premier exemple montre un livre de jeu avec un seul jeu nommé “DEPLOY VLANS” qui s’applique sur les hôtes “access” d’un inventaire. Celui-ci est constitué d’une seule tâche “ENSURE VLANS EXIST”

```ruby
- name: DEPLOY VLANS
  hosts: access
  connection: network_cli
  gather_facts: no
  tasks:
    - name: ENSURE VLANS EXIST
      nxos_vlan:
        vlan_id: 100
        admin_state: up
        name: WEB
```

## Exemple 2

un livre de jeu qui lance un seul jeu constitué de cinq tâches.
```ruby
---
- name: Configure webserver with nginx
  hosts: webservers
  become: True
  become_method: sudo
  tasks:
    - name: install nginx
      apt:
        name: nginx
        update_cache: yes
    - name: copy nginx config file
      copy:
        src: files/nginx.conf
        dest: /etc/nginx/sites-available/default
    - name: enable configuration
      file:
        dest: /etc/nginx/sites-enabled/default
        src: /etc/nginx/sites-available/default
        state: link
    - name: copy index.html
      template:
        src: templates/index.html.j2
        dest: /usr/share/nginx/html/index.html
        mode: 0644
    - name: restart nginx
      service:
        name: nginx
        state: restarted
```

# Les rôles

Les rôles (roles) permettent de charger automatiquement des vars_files, des tasks, des handlers sur base d’une structure de fichier définie. Les rôles sont intéressants pour leur aspect “ré-utilisable”.

# Exécutables Ansible (petit projet)
Ansible vient avec plusieurs programmes. 

• Ideal pour démarrer un projet et tester nos configurations ansible

• S’utilise facilement avec les modules

• Permet de piloter facilement une infrastructure cible

| Syntax | Description |
| --- | ----------- |
| ansible | programme initial pour l’exécution de commandes ad-hoc |
| ansible-config | Vérifie la configuration courante d’Ansible |
| ansible-inventory | Liste les informations de l’inventaire en format JSON ou YAML |
| ansible-doc | Permet de consulter la documentation hors-ligne |
| ansible-playbook | Permet d’exécuter des livres de jeu |

# Exemple:

## Programme ansible
Le programme ansible permet d’exécuter des tâches ad hoc.

`ansible --version`

La commande ansible attend toujours un hôte ou un groupe d’hôtes d’inventaire comme argument. Ici une option -m désigne l’usage du module ping.

`ansible -m ping localhost`

## Programme ansible-config
Le programme ansible-config vérifie la configuration courante d’Ansible avec une des trois options list, dump et view.

`ansible-config dump`

## Programme ansible-doc
Le binaire ansible-doc permet de consulter la documentation hors-ligne.

Ici, pour obtenir la liste des modules documentés :
`ansible-doc -l | grep 'yum'`

Ici pour lister par exemple les plugins de connexion :
`ansible-doc -t connection -l`

Pour afficher l’aide du module “yum” :
`ansible-doc yum`

Pour afficher uniqument les arguments du module :
`ansible-doc yum -s`

## Programme ansible-playbook
Le programme ansible-playbook permet d’exécuter des livres de jeu.

`ansible-playbook playbook.yml --list-hosts`

`ansible-playbook --list-tasks playbook.yml`

## Programme ansible-inventory
Le programme ansible-inventory liste les informations de l’inventaire en format JSON ou YAML.

Avec l’action –list, l’inventaire sort en format JSON : `ansible-inventory -i inventory --list`

Mais il pourrait sortir en format YAML : `ansible-inventory -i inventory --list -y`

# Déploiement du laboratoire de travail

3 machines sous AWS

• Ansible-master: EC2; t3.medium ; Ubuntu Server 20.04 LTS

• Ansible-worker01: EC2; t2.micro ; Ubuntu Server 20.04 LTS

• Ansible-worker02: EC2; t2.micro ; Ubuntu Server 20.04 LTS

# Installation & Configuration d'Ansible
 
Il existe différentes façons d'installer le moteur Ansible. Vous avez le choix entre les méthodes suivantes :

Installation de la dernière version proposée par votre gestionnaire de package de votre OS. (version stable)

Installation avec le gestionnaire de packages python pip.(derniere version d'Ansible)

## Ansible: fichier de configuration ansible.cfg

• Par ordre croissant de priorité, on a :
```txt
/etc/ansible/ansible.cfg : fournit (ou pas si installation depuis pip ) par le package après l’installation
~/.ansible.cfg : dans votre home, créé par vos soins
./ansible.cfg : dans votre répertoire de travail
La variable d’env ANSIBLE_CONFIG
export ANSIBLE_CONFIG=un_fichier_sur_le_disque.
 ```    
• La commande ansible --version permets de savoir celui qui est pris en compte en temps réel

# TP-1: Installation de Ansible
```txt
Installation sur Ubuntu (sur votre master) avec l'utilisateur ubuntu
Toujours travaillez avec l’utilisateur ubuntu pas root pour des raisons de sécurité
On va installer via l’outil pip3 de python 3
Test de la version installée
Test du fichier de configuration chargé
```

## Debian/Ubuntu : Installation via PIP
```ruby
ubuntu@ip-172-31-8-10:~$ sudo apt update -y
ubuntu@ip-172-31-8-10:~$ python3 --version
Python 3.8.10
# Si version Python 3
ubuntu@ip-172-31-8-10:~$ sudo apt-get install python3-pip -y
ubuntu@ip-172-31-8-10:~$ sudo pip3 install ansible
ubuntu@ip-172-31-8-10:~$ ansible --version
ansible [core 2.12.1] # derniere version d'ansible
  config file = None  # Il n'y a pas de fichier de configuration avec la methode pip
  configured module search path = ['/home/ubuntu/.ansible/plugins/modules', '/usr/share/ansible/plugins/modules']
  ansible python module location = /usr/local/lib/python3.8/dist-packages/ansible
  ansible collection location = /home/ubuntu/.ansible/collections:/usr/share/ansible/collections
  executable location = /usr/local/bin/ansible
  python version = 3.8.10 (default, Nov 26 2021, 20:14:08) [GCC 9.3.0]
  jinja version = 2.10.1
  libyaml = True
ubuntu@ip-172-31-8-10:~$ sudo apt -y install sshpass
```

## A partir du gestionnaire de packets (version stable)
```ruby
sudo apt-get update
sudo apt-get install ansible
sudo yum install ansible
```
# Activation de la connexion SSH par mot de passe sur slave

1) Modifier le fichier de conf sur la machine cliente

Par defaut AWS linux interdit les connexions avec user et mdp. il faudra donc les authoriser.

sudo vi /etc/ssh/sshd_config
```ruby
avant
PasswordAuthentication no 
apres
PasswordAuthentication yes
```
ou

`sudo sed -i 's/PasswordAuthentication no/PasswordAuthentication yes/' /etc/ssh/sshd_config`

2) Redemarrer le service ssh sur slave
sudo systemctl restart ssh

3) Modifier le mot de passe du compte ubuntu sur slave
sudo -i
passwd ubuntu (puis modifiez le mot de passe)

# Activation de sshpass sur le Master 
* déja fait à l'étape d'installation ansible

Installer `sshpass` sur la machine à partir de laquelle vous tentez un connexion ssh (Master)
`sudo apt-get install sshpass -y`


# Commandes Imperative ou AD-HOC

Les modules sont des programmes qu'Ansible pousse depuis une machine de contrôle vers tous les hôtes distants. Les modules sont exécutés à l'aide de playbooks ou depuis la cli Ansible (AD-HOC), et ils contrôlent des éléments tels que les services, les packages, les fichiers et bien plus.

Dans notre exemple, on utilisera le module ping qui envoie une requête ping à tous les nœuds de votre inventaire.

# Utilisateur par défaut
Afin de ne pas avoir à définir l'utilisateur qui va executer la commande sur mon poste client (ou slave) je peux ajouter à mon fichier d'inventaire les lignes ansible_user et ansible_password.

Il est possible de définir un utilisateur par défaut sur votre inventaire avec les options:
```ruby
ansible_user=ubuntu
ansible_password=ubuntu
```

`vi hosts`
```ruby
worker01 ansible_host=172.31.6.70 ansible_user=ubuntu ansible_password=ubuntu ansible_ssh_common_args='-o StrictHostKeyChecking=no'
worker02 ansible_host=172.31.13.73 ansible_user=ubuntu ansible_password=ubuntu ansible_ssh_common_args='-o StrictHostKeyChecking=no'
```

# TP-2: Utilisation des commandes ad-hoc
• Créez un fichier d’inventaire hosts

`vi hosts`
```ruby
worker01 ansible_host=172.31.6.70 ansible_user=ubuntu ansible_password=ubuntu ansible_ssh_common_args='-o StrictHostKeyChecking=no'
worker02 ansible_host=172.31.13.73 ansible_user=ubuntu ansible_password=ubuntu ansible_ssh_common_args='-o StrictHostKeyChecking=no'
```

• Utilisez une commande ad-hoc pour tentez de pinger le client ansible

`ansible -i hosts all -m ping`
```yaml
172.31.13.73 | SUCCESS => {
    "ansible_facts": {
        "discovered_interpreter_python": "/usr/bin/python3"
    },
    "changed": false,
    "ping": "pong"
}
172.31.6.70 | SUCCESS => {
    "ansible_facts": {
        "discovered_interpreter_python": "/usr/bin/python3"
    },
    "changed": false,
    "ping": "pong"
}
```
• Utilisez une commande ad-hoc pour créer un fichier `frazer.txt` et le contenu `Bonjour AJC` sur les clients

`ansible -i hosts all -m copy -a "dest=/home/ubuntu/frazer.txt content='Bonjour AJC'"`
```yaml
172.31.13.73 | CHANGED => {
    "ansible_facts": {
        "discovered_interpreter_python": "/usr/bin/python3"
    },
    "changed": true,
    "checksum": "bd4e885dc612bea43f0c73e740f842dbfd9f6d44",
    "dest": "/home/ubuntu/frazer.txt",
    "gid": 1000,
    "group": "ubuntu",
    "md5sum": "5f0b5298069e49a35981f83527bacf6f",
    "mode": "0664",
    "owner": "ubuntu",
    "size": 11,
    "src": "/home/ubuntu/.ansible/tmp/ansible-tmp-1640689087.43161-44553-175033144969312/source",
    "state": "file",
    "uid": 1000
}
172.31.6.70 | CHANGED => {
    "ansible_facts": {
        "discovered_interpreter_python": "/usr/bin/python3"
    },
    "changed": true,
    "checksum": "bd4e885dc612bea43f0c73e740f842dbfd9f6d44",
    "dest": "/home/ubuntu/frazer.txt",
    "gid": 1000,
    "group": "ubuntu",
    "md5sum": "5f0b5298069e49a35981f83527bacf6f",
    "mode": "0664",
    "owner": "ubuntu",
    "size": 11,
    "src": "/home/ubuntu/.ansible/tmp/ansible-tmp-1640689087.4153397-44551-254516027147879/source",
    "state": "file",
    "uid": 1000
}
```

• Testez l’effet du module « setup » sur votre inventaire





# TP-3: Utilisation des commandes ad-hoc (Modules)
• Créez un fichier d’inventaire `inventaire` pour vos différentes machines

`vi inventaire`
```ruby
worker01 ansible_host=172.31.6.70 ansible_user=ubuntu ansible_password=ubuntu ansible_ssh_common_args='-o StrictHostKeyChecking=no'
worker02 ansible_host=172.31.13.73 ansible_user=ubuntu ansible_password=ubuntu ansible_ssh_common_args='-o StrictHostKeyChecking=no'
```
• Utilisez une commande ad-hoc pour tentez de pinger vos clients afin de confirmer que l’accès est opérationnel

`ansible -i inventaire all -m ping`
```ruby
worker01 | SUCCESS => {
    "ansible_facts": {
        "discovered_interpreter_python": "/usr/bin/python3"
    },
    "changed": false,
    "ping": "pong"
}
worker02 | SUCCESS => {
    "ansible_facts": {
        "discovered_interpreter_python": "/usr/bin/python3"
    },
    "changed": false,
    "ping": "pong"
}
```
• Utilisez une commande ad-hoc installer nginx sur le client1

`ansible -i inventaire worker01 -m apt -b -a "name=nginx state=present"`

• Démarrer nginx puis activez le (Enable)

`ansible -i inventaire worker01 -m service -b -a "name=nginx state=started"`

• Installez apache2 sur le client2, démarrer son service et puis activez le également

`ansible -i inventaire worker02 -m apt -b -a "name=apache2 state=present"`

• Testez à l’aide de l’IP de vos machines clientes si les serveurs webs fonctionnent correctement sur vos différentes machines (http://@IP:80; Pensez à ouvrir le port
80 au niveau de votre SG)

`curl 3.216.79.121:80`
`curl 3.92.42.244:80`

• Supprimez à l’aide des commandes ad-hoc les paquets et services nginx et apache2 sur vos clients

- nginx

`ansible -i inventaire worker01 -b -m apt -a "name=nginx state=absent purge=yes autoremove=yes"`

- apache2

`ansible -i inventaire worker02 -b -m apt -a "name=apache2 state=absent purge=yes autoremove=yes"`

# Pour avoir acces en root sur les slaves :
```
- avant
ansible -i inventaire worker01 -m apt -a "name=nginx state=present"

"-b"
- apres
ansible -i inventaire worker01 -m apt -b -a "name=nginx state=present"
```

# YAML

- Les playbooks se font en YAML ou en JSON.
- Les inventaires se font aussi en YAML.
- variable: valeur
- un espace apres :
- 2 espaces pour l'indentation

# Inventaire ansible

- 3 Formats possibles : INI, YAML, JSON
- Regrouper les machines par groupes
- Définir des groupes enfants d’un groupe
- Fournir les moyens de connexion à un hôte spécifique
- Fournir les moyens de connexion à un groupe d’hôte
- Les host_vars : inventaire d’un hôte
- Les group_vars : inventaire d’un groupe de machines


# TP-4: Inventaire au format yaml
- Modifiez le fichier hosts que vous avez écris au format INI afin qu’il soit en au format yaml

* Avant:
```Ruby
worker01 ansible_host=172.31.6.70 ansible_user=ubuntu ansible_password=ubuntu ansible_ssh_common_args='-o StrictHostKeyChecking=no'
worker02 ansible_host=172.31.13.73 ansible_user=ubuntu ansible_password=ubuntu ansible_ssh_common_args='-o StrictHostKeyChecking=no'
```

`vi hosts.yaml`

```Ruby
all:
  hosts:
    worker01:
      ansible_host:  172.31.6.70
      ansible_user:  ubuntu
      ansible_password:  ubuntu
      ansible_ssh_common_args:  '-o StrictHostKeyChecking=no'
    worker02:
      ansible_host:  172.31.13.73
      ansible_user:  ubuntu
      ansible_password:  ubuntu
      ansible_ssh_common_args:  '-o StrictHostKeyChecking=no'
```
- Testez à nouveau vos commandes ad-hoc avec le nouveau fichier d’inventaire au format yaml

`ansible -i hosts.yaml worker01 -m ping`
```Ruby
worker01 | SUCCESS => {
    "ansible_facts": {
        "discovered_interpreter_python": "/usr/bin/python3"
    },
    "changed": false,
    "ping": "pong"
}
```

# Inventaire au format INI & Yaml

Variables et mots clés
```
• all : C’est l’ancêtre de tous les groupes
• hosts :
    Dans un inventaire, sert à définir les machines d’un group
    Dans un playbook, définit les cible où appliquer les actions
• children : Pour définir les sous groupes d’un groupe
• vars : Dans le playbook ou l’inventaire, pour définir les variables d’un groupe
• vars_files : Dans le playbook, pour indiquer un fichier de variables
• vars_promt : Pour demander à l’utilisateur de saisir la valeur de la variable en début de playbook
```

# Commande ansible-inventory

- utilisé pour afficher l'inventaire configuré tel qu'Ansible le voit

`--host <HOST>`

Sortir des informations spécifiques à l'hôte, fonctionne comme un script d'inventaire

`--list`

Affiche toutes les informations sur les hôtes, fonctionne comme un script d'inventaire

`-y, --yaml`

Utiliser le format YAML au lieu du JSON par défaut


## Exemple:

ansible-inventory -i hosts.ini --list -y > hosts.yaml
ansible-inventory -i hosts.ini --list > hosts.json

# TP-5: Inventaire et variable
Créez un fichier hosts.ini au format INI avec les modalités d’inventaire suivant:


• Tous les hôtes via le groupe « all » devront avoir pour login user ubuntu

    [all:vars]
    ansible_user=ubuntu

• Les clients devrons faire partie d’un groupe appelé « prod »

    [prod]
    worker01 ansible_host=172.31.6.70 ansible_password=ubuntu ansible_ssh_common_args='-o StrictHostKeyChecking=no'
    worker02 ansible_host=172.31.13.73 ansible_password=ubuntu ansible_ssh_common_args='-o StrictHostKeyChecking=no'

• Un groupe contenant l’hote Master ansible sera présent

(la variable `ansible_connection` valorisée avec `local` est utilisé pour définir le master.)

    [ansible]
    localhost ansible_connection=local

• Le mot de passe à utiliser pour toutes connexion ssh devra être ubuntu pour toutes les machines du groupe « prod »

    [prod]
    worker01 ansible_host=172.31.6.70 ansible_password=ubuntu ansible_ssh_common_args='-o StrictHostKeyChecking=no'
    worker02 ansible_host=172.31.13.73 ansible_password=ubuntu ansible_ssh_common_args='-o StrictHostKeyChecking=no'

• La variable « env » devra être égale à « production » pour toutes les machines du groupe « prod »

    [prod:vars]
    env=production

`vi hosts.ini`

```ruby
[all:vars]
ansible_user=ubuntu

[ansible]
localhost ansible_connection=local

[prod]
worker01 ansible_host=172.31.6.70 ansible_password=ubuntu ansible_ssh_common_args='-o StrictHostKeyChecking=no'
worker02 ansible_host=172.31.13.73 ansible_password=ubuntu ansible_ssh_common_args='-o StrictHostKeyChecking=no'

[prod:vars]
env=production
```

• Créez ensuite un fichier hosts.yaml, version yaml du fichier ini

    ansible-inventory -i hosts.ini --list -y > hosts.yaml

cat hosts.yaml
```Ruby
all:
  children:
    ansible:
      hosts:
        localhost:
          ansible_connection: local
          ansible_user: ubuntu
    prod:
      hosts:
        worker01:
          ansible_host: 172.31.6.70
          ansible_password: ubuntu
          ansible_ssh_common_args: -o StrictHostKeyChecking=no
          ansible_user: ubuntu
          env: production
        worker02:
          ansible_host: 172.31.13.73
          ansible_password: ubuntu
          ansible_ssh_common_args: -o StrictHostKeyChecking=no
          ansible_user: ubuntu
          env: production
    ungrouped: {}
```

• Transformer le fichier yaml en json et tester de nouveau les commandes

    ansible-inventory -i hosts.ini --list > hosts.json 

 `vi hosts.json`
```json
{
    "_meta": {
        "hostvars": {
            "localhost": {
                "ansible_connection": "local",
                "ansible_user": "ubuntu"
            },
            "worker01": {
                "ansible_host": "172.31.6.70",
                "ansible_password": "ubuntu",
                "ansible_ssh_common_args": "-o StrictHostKeyChecking=no",
                "ansible_user": "ubuntu",
                "env": "production"
            },
            "worker02": {
                "ansible_host": "172.31.13.73",
                "ansible_password": "ubuntu",
                "ansible_ssh_common_args": "-o StrictHostKeyChecking=no",
                "ansible_user": "ubuntu",
                "env": "production"
            }
        }
    },
    "all": {
        "children": [
            "ansible",
            "prod",
            "ungrouped"
        ]
    },
    "ansible": {
        "hosts": [
            "localhost"
        ]
    },
    "prod": {
        "hosts": [
            "worker01",
            "worker02"
        ]
```

(les conversions se font exclusivement vers yaml ou json pas ini)

`ls /home/ubuntu`
```
hosts  hosts.ini  hosts.json  hosts.yaml  inventaire
```

• Testez les commandes ad-hoc de ping et setup avec les trois fichiers d’inventaire

`ansible -i hosts.ini all -m ping`
```Ruby
localhost | SUCCESS => {
    "ansible_facts": {
        "discovered_interpreter_python": "/usr/bin/python3"
    },
    "changed": false,
    "ping": "pong"
}
worker01 | SUCCESS => {
    "ansible_facts": {
        "discovered_interpreter_python": "/usr/bin/python3"
    },
    "changed": false,
    "ping": "pong"
}
worker02 | SUCCESS => {
    "ansible_facts": {
        "discovered_interpreter_python": "/usr/bin/python3"
    },
    "changed": false,
    "ping": "pong"
}
```
`ansible -i hosts.yaml all -m ping`
```Ruby
localhost | SUCCESS => {
    "ansible_facts": {
        "discovered_interpreter_python": "/usr/bin/python3"
    },
    "changed": false,
    "ping": "pong"
}
worker01 | SUCCESS => {
    "ansible_facts": {
        "discovered_interpreter_python": "/usr/bin/python3"
    },
    "changed": false,
    "ping": "pong"
}
worker02 | SUCCESS => {
    "ansible_facts": {
        "discovered_interpreter_python": "/usr/bin/python3"
    },
    "changed": false,
    "ping": "pong"
}
```
`ansible -i hosts.json all -m ping`
```Ruby
[WARNING]: Skipping unexpected key (hostvars) in group (_meta), only "vars", "children" and "hosts" are valid
[WARNING]:  * Failed to parse /home/ubuntu/hosts.json with yaml plugin: Invalid "children" entry for "all" group, requires a dictionary, found "<class 'list'>" instead.
[WARNING]:  * Failed to parse /home/ubuntu/hosts.json with ini plugin: Invalid host pattern '_meta:' supplied, ending in ':' is not allowed, this character is reserved to provide a
port.
[WARNING]: Unable to parse /home/ubuntu/hosts.json as an inventory source
[WARNING]: No inventory was parsed, only implicit localhost is available
{ | UNREACHABLE! => {
    "changed": false,
    "msg": "Failed to connect to the host via ssh: ssh: Could not resolve hostname {: Name or service not known",
    "unreachable": true
}
```
# Le module Debug 

`ansible.builtin.debug`

-m debug: 

Ce module imprime des instructions pendant l'exécution et peut être utile pour déboguer des variables ou des expressions sans nécessairement arrêter le playbook.

msg = personnalise le message qui est affiché.

=> ansible -i hosts.ini all -m debug -a "msg='{{ env }}'"

# Précédence des variables
- Les “Group variables” s’appliquent pour tous les périphériques d’un groupe : all, le groupe parent, le groupe enfant.
- Les “Host variables” s’appliquent uniquement sur le périphériques et outrepassent les “Group variables”.

## Organisation de variables d’inventaire en fichiers
Au lieu de placer les variables des hôtes et des groupes dans le fichier d’inventaire, il est possible de les encoder dans des fichiers séparés prenant le nom de l’hôte ou du groupe dans les dossiers par défaut group_vars et host_vars en format YAML.

`/home/ubuntu`

- group_vars
    - prod.yaml
- host_vars
    - localhost.yaml
    - worker01.yaml
    - worker02.yaml


# TP-6: Inventaire et surcharge de variable

- Servez vous de precedent fichier d’inventaire au format YAML afin de testez la surcharge des variables
- Surcharger ensuite cette variable en utilisant le repertoire group_vars et testez afin d’avoir sa nouvelle valeur
- Surcharger avec un valeur différente en utilisant le repertoire host_vars puis testez
- Surcharger ensuite avec une valeur différente en utilisant le parameter –e afin de passer la valeur de la variable lors de l’execution de la commande ansible

![Getting Started](/assets/b.jpg)

## Information :

Au lieu de placer les variables des hôtes et des groupes dans le fichier d’inventaire `hosts.ini`. 

Il est possible de les encoder dans des fichiers séparés prenant le nom des hôtes dans le dossier par défaut host_vars en format YAML :

    - localhost.yaml
    - worker01.yaml
    - worker02.yaml

 et du groupe dans les dossiers par défaut group_vars en format YAML :

    - prod.yaml

## Question 1
1) Servez vous de precedent fichier d’inventaire au format YAML afin de testez la surcharge des variables
```txt
Utilisez le module debug avec l’attribut ”msg” pour affichier la valeur de la variable env ( -m debug -a "msg={{ env }}"
```

`ansible -i hosts.ini all -m debug -a "msg='{{ env }}'"`
```ruby
localhost | FAILED! => {
    "msg": "The task includes an option with an undefined variable. The error was: 'env' is undefined. 'env' is undefined"
}
worker01 | SUCCESS => {
    "msg": "production"
}
worker02 | SUCCESS => {
    "msg": "production"
}
```
## Explication:

Le fichier `hosts.ini` contient une variable d'environement pour les `worker01 & 02` dans le group `prod` avec pour valeur `production`. Mais `localhost` lui ne dispose d'aucune variable d'environement.

```ruby
[all:vars]
ansible_user=ubuntu

[ansible]
localhost ansible_connection=local

[prod]
worker01 ansible_host=172.31.6.70 ansible_password=ubuntu ansible_ssh_common_args='-o StrictHostKeyChecking=no'
worker02 ansible_host=172.31.13.73 ansible_password=ubuntu ansible_ssh_common_args='-o StrictHostKeyChecking=no'

[prod:vars]
env=production
```

## Question 2
2) Surcharger ensuite cette variable en utilisant le repertoire group_vars et testez afin d’avoir sa nouvelle valeur


- repertoir projet = /home/ubuntu

`mkdir group_vars`

`vi group_vars/prod.yaml`

```ruby
env:  prod
```

`ansible -i hosts.ini all -m debug -a "msg='{{ env }}'"`

```ruby
localhost | FAILED! => {
    "msg": "The task includes an option with an undefined variable. The error was: 'env' is undefined. 'env' is undefined"
}
worker01 | SUCCESS => {
    "msg": "prod"
}
worker02 | SUCCESS => {
    "msg": "prod"
}
```
- group_vars
    - prod.yaml

# Explication:

Le fichier `hosts.ini` contient une variable d'environement pour les `worker01 & 02` dans le group `prod` avec pour valeur `production`. Mais j'ai créé un répertoire `group_vars` qui contient un fichier du meme nom que mon groupe "prod" `prod.yaml`. Ainsi, la varible qu'elle contient s'appliquera à tous les workers issu du group "pro" meme si le fichier inventaire hosts.ini possede des varable spécifiques. `localhost` lui ne dispose d'aucune variable d'environement.


## Question 3
3) Surcharger avec un valeur différente en utilisant le repertoire host_vars puis testez

`/home/ubuntu`

`mkdir host_vars`
  
`vi host_vars/worker01.yaml`

```ruby
env:  worker01
```

- group_vars
    - prod.yaml
- host_vars
    - worker01.yaml

`ansible -i hosts.ini all -m debug -a "msg='{{ env }}'"`

```ruby
localhost | FAILED! => {
    "msg": "The task includes an option with an undefined variable. The error was: 'env' is undefined. 'env' is undefined"
}
worker01 | SUCCESS => {
    "msg": "worker01"
}
worker02 | SUCCESS => {
    "msg": "prod"
}
```

`vi host_vars/worker02.yaml`

```ruby
env:  worker02
```

`ansible -i hosts.ini all -m debug -a "msg='{{ env }}'"`
```ruby
localhost | FAILED! => {
    "msg": "The task includes an option with an undefined variable. The error was: 'env' is undefined. 'env' is undefined"
}
worker01 | SUCCESS => {
    "msg": "worker01"
}
worker02 | SUCCESS => {
    "msg": "worker02"
}
```
- group_vars
    - prod.yaml
- host_vars
    - worker01.yaml
    - worker02.yaml

`vi host_vars/localhost.yaml`

```ruby
env:  localhost
```
`ansible -i hosts.ini all -m debug -a "msg='{{ env }}'"`
```ruby
localhost | SUCCESS => {
    "msg": "localhost"
}
worker01 | SUCCESS => {
    "msg": "worker01"
}
worker02 | SUCCESS => {
    "msg": "worker02"
}
```
- group_vars
    - prod.yaml
- host_vars
    - localhost.yaml
    - worker01.yaml
    - worker02.yaml

`ansible -i hosts.ini all -m debug -a "msg='{{ env }}'" -e env=surcharge`
```ruby
localhost | SUCCESS => {
    "msg": "surcharge"
}
worker01 | SUCCESS => {
    "msg": "surcharge"
}
worker02 | SUCCESS => {
    "msg": "surcharge"
}
```

`/home/ubuntu`

- group_vars
    - prod.yaml
- host_vars
    - localhost.yaml
    - worker01.yaml
    - worker02.yaml


`vi hosts.ini`

```ruby
[all:vars]
ansible_user=ubuntu

[ansible]
localhost ansible_connection=local

[prod]
worker01 ansible_host=172.31.6.70 ansible_password=ubuntu ansible_ssh_common_args='-o StrictHostKeyChecking=no'
worker02 ansible_host=172.31.13.73 ansible_password=ubuntu ansible_ssh_common_args='-o StrictHostKeyChecking=no'

[prod:vars]
env=production
```

# playbooks

Un manifest peut contenir plusieurs playbooks.
les vars du fichier playbook on le dessus sur toutes les méthodes de surcharges, sauf se executer en CLI.

des listes d’action de type de tâches:

pre_tasks: avant

tasks: pendant

post_tasks: apres

```ruby
- name: "PLAY 1: tasks playbook"
  hosts: localhost
  connection: local
  pre_tasks:
  roles:
  tasks:
  post_tasks:
  handlers:
```

# Structure d’une tâche

Les tâches peuvent être directement appelées de manière séquentielle dans le livre de jeu selon la syntaxe formelle suivante (les modules présentés dans l’exemple n’existent pas) :
```ruby
- name: "Task 1 example"
  module_x:
    argument1: foo
    argument2: bar
- name: "Task 2 example"
  module_y:
    argument1: foo
    argument2: bar
```

Les tâches font appel à une seule action, c’est-à-dire à un seul module et ses arguments,

# Templating avec Jinja

modele qui s'adapte à l'envoronnement dans le quel il est deployé à l'aide de streucture conditionnel, il est dynamique.



```
ubuntu@AnsibleMaster:~$ ansible localhost -m ansible.builtin.setup -a 'filter=ansible_distribution'
localhost | SUCCESS => {
    "ansible_facts": {
        "ansible_distribution": "Ubuntu"
    },
    "changed": false
}
ubuntu@AnsibleMaster:~$ ansible -i /home/ubuntu/webapp/prod.yaml worker01 -m ansible.builtin.setup -a 'filter=ansible_distribution'
worker01 | SUCCESS => {
    "ansible_facts": {
        "ansible_distribution": "Ubuntu",
        "discovered_interpreter_python": "/usr/bin/python3"
    },
    "changed": false
}
ubuntu@AnsibleMaster:~$ ansible -i /home/ubuntu/webapp/prod.yaml worker01 -m ansible.builtin.setup -a "filter=ansible_distribution*"
worker01 | SUCCESS => {
    "ansible_facts": {
        "ansible_distribution": "Ubuntu",
        "ansible_distribution_file_parsed": true,
        "ansible_distribution_file_path": "/etc/os-release",
        "ansible_distribution_file_variety": "Debian",
        "ansible_distribution_major_version": "20",
        "ansible_distribution_release": "focal",
        "ansible_distribution_version": "20.04",
        "discovered_interpreter_python": "/usr/bin/python3"
    },
    "changed": false
}

```

# module copy
long
utiliser git

# Generic OS package manager

## ansible.builtin.package

Ce module gère les packages sur une cible sans spécifier de module de gestion de packages (comme ansible.builtin.yum, ansible.builtin.apt, …). Il est pratique à utiliser dans un environnement hétérogène de machines sans avoir à créer une tâche spécifique pour chaque gestionnaire de packages. appels de package derrière le module pour le gestionnaire de packages utilisé par le système d'exploitation découvert par le module ansible.builtin.setup. Si l'installation n'a pas encore été exécutée, le package l'exécutera. Ce module agit comme un proxy pour le module de gestionnaire de packages sous-jacent. Bien que tous les arguments soient passés au module sous-jacent, tous les modules ne prennent pas en charge les mêmes arguments. Cette documentation ne couvre que l'intersection minimale des arguments de module pris en charge par tous les modules d'empaquetage. Pour les cibles Windows, utilisez plutôt le module ansible.windows.win_package.


Examples
- name: Install ntpdate
  ansible.builtin.package:
    name: ntpdate
    state: present

# This uses a variable as this changes per distribution.
- name: Remove the apache package
  ansible.builtin.package:
    name: "{{ apache }}"
    state: absent

- name: Install the latest version of Apache and MariaDB
  ansible.builtin.package:
    name:
      - httpd
      - mariadb-server
    state: latest











# loop

# Tags Ansible
Tags Ansible
Si vous avez un grand livre de jeu, il peut s’avérer utile de ne pouvoir en exécuter qu’une partie spécifique plutôt que de tout lire dans le livre. Ansible prend en charge un attribut tags: pour cette raison.

Lorsque vous exécutez un livre de jeu, vous pouvez filtrer les tâches en fonction des “tags” de deux manières:

Sur la ligne de commande, avec les options --tags ou --skip-tags
Dans les paramètres de configuration Ansible, avec les options TAGS_RUN et TAGS_SKIP
Les “tags” peuvent être appliqués à de nombreuses structures dans Ansible :

tasks:
roles:
blocks:
import_roles:
import_tasks:
Mais son utilisation la plus simple est avec des tâches individuelles.

# Import et include

La principale différence est:

Toutes les import*déclarations sont pré-traitées au moment où les playbooks sont analysés.
Toutes les include*instructions sont traitées telles qu’elles se sont présentées lors de l’exécution du livre de jeu.

Ainsi importest statique, includeest dynamique.

Utiliser import lorsque vous traitez avec des "unités" logiques. Par exemple, séparez une longue liste de tâches dans des fichiers de sous-tâche:

main.yml:

- import_tasks: prepare_filesystem.yml
- import_tasks: install_prerequisites.yml
- import_tasks: install_application.yml
Mais vous utiliseriez includepour traiter différents flux de travail et prendre des décisions en fonction de faits rassemblés de manière dynamique:

les conditions d'installation:

- include_tasks: prerequisites_{{ ansible_os_family | lower }}.yml


Les importations remplacent essentiellement la tâche par les tâches du fichier. Il n'y a pas import_taskau moment de l'exécution. Ainsi, des attributs tels que tags, et when(et très probablement d’autres attributs) sont copiés dans chaque tâche importée.

includes sont bien exécutés. tagset whend’une tâche incluse s’appliquent uniquement à la tâche elle-même.

Les tâches importétiquetées à partir d'un fichier importé sont exécutées si la tâche est non étiquetée. Aucune tâche n'est exécutée à partir d'un fichier inclus si la includetâche n'est pas marquée.

Toutes les tâches d'un fichier importé sont exécutées si la importtâche est étiquetée. Seules les tâches balisées d'un fichier inclus sont exécutées si la includetâche est balisée.

Limitations de imports:

ne peut pas être utilisé avec with_*ou loopattributs
impossible d'importer un fichier dont le nom dépend d'une variable
Limitations de includes:

--list-tags ne montre pas les tags des fichiers inclus
--list-tasks ne montre pas les tâches des fichiers inclus
vous ne pouvez pas utiliser notifypour déclencher un nom de gestionnaire qui provient de l'intérieur d'une inclusion dynamique
vous ne pouvez pas utiliser --start-at-taskpour commencer l'exécution d'une tâche dans une inclusion dynamique

# Comment vérifier si docker est installé - ansible - centos

I'm trying to make a conditional "when" in my ansible playbook. If docker not installed, install docker. So i have a playbook, with a role with some tasks in it. And i would like to do something like

when: docker != not exist

or

when: docker == false

When i get setup, from one with docker installed i get this:

"ansible_docker0": {
            "active": true,
            "device": "docker0",
            "features": {
When no docker : 
SUCCESS => {
    "ansible_facts": {
        "discovered_interpreter_python": "/usr/bin/python"
    },
    "changed": false

# Clé privée

si ssh établie pas besoin d'utiliser les mots de passes

Il faut utilisé la commande ssh-copy-id (pensez à autoriser le mdp)

/user/home/ubuntu/.ssh = emplacement clé public

--private-key PRIVATE_KEY_FILE

https://docs.ansible.com/ansible/latest/user_guide/connection_details.html

copie clé privéee dans home/ubuntu de mon master

oussama-kp-ajc.pem

 chmod 400 oussama-kp-ajc.pem

Configurer des clés SSH sur Linux ou Unix selon les besoins de mon projet et les fournisseurs d'hébergement cloud. 

Comment configurer et indiquer à Ansible d'utiliser différentes clés ssh ? Comment configurer les informations d'identification SSH par fournisseur de services d'hébergement cloud ? 

Ansible utilise SSH qui permet également aux utilisateurs et à ansbile ; pour se connecter à des serveurs distants et effectuer des tâches de gestion. Cette page montre comment déjà configurer des clés SSH pour se connecter au serveur distant à l'aide de l'outil d'automatisation informatique Ansible.

ansible_ssh_pass 

Le mot de passe ssh à utiliser (ne stockez jamais cette variable en texte clair ; utilisez toujours un coffre-fort. 
Voir Variables et coffres-forts) 

ansible_ssh_private_key_file 

Fichier de clé privée utilisé par ssh. Utile si vous utilisez plusieurs clés et que vous ne souhaitez pas utiliser l'agent SSH.

```
all:
  vars:
     ansible_ssh_private_key_file: /home/ubuntu/frazer-kp-ajc1.pe
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
          ansible_host: 172.31.95.37
          hostname: AnsibleWorker01

        worker02:
          ansible_host: 172.31.95.0
          hostname: AnsibleWorker02
````

# Sécurisation des donnéé sensible:

# Ansible-vault:





# ansible configuration
https://docs.ansible.com/ansible/latest/reference_appendices/config.html
ordre de priorité

1) variable d'environement 
2) /home/ubuntu/projet/.ansible.cfg
3) home/.ansible.cfg
4) /etc/ansible/.ansible.cfg

# je défini le fichier d'inventaire
vi ansible.cfg

[defaults]
inventory= /home/ubuntu/webapp2/hosts.yaml

ansible all -m ping

# become a true

DEFAULT_BECOME
La description Bascule l'utilisation de l'élévation des privilèges, vous permettant de « devenir » un autre utilisateur après la connexion.

Section [privilege_escalation]

Key become

Environment
Variable
ANSIBLE_BECOME

vi ansible.cfg

[defaults]
inventory= /home/ubuntu/webapp2/hosts.yaml

[privilege_escalation]
become= true


DEFAULT_ASK_VAULT_PASS
La description 
Cela contrôle si un playbook Ansible doit demander un mot de passe de coffre-fort.


vi ansible.cfg

[defaults]
inventory= /home/ubuntu/webapp2/hosts.yaml
ask_vault_pass= true

[privilege_escalation]
become= true

DEFAULT_REMOTE_USER
La description 

Définit l'utilisateur de connexion pour les machines cibles Lorsqu'il est vide, il utilise la valeur par défaut du plug-in de connexion, normalement l'utilisateur en cours d'exécution d'Ansible.

Section [defaults]

Key remote_user

vi ansible.cfg

[defaults]
inventory= /home/ubuntu/webapp2/hosts.yaml
ask_vault_pass= true

[privilege_escalation]
become= true