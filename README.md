# Ansible

Action à l'aide de commandes impérative ou avec des play books.

Ansible utilise une syntaxe écrite en YAML. Aucune compétence en programmation particulière n'est nécessaire pour créer les playbooks d'Ansible.

Play books importés dans d'autres play books.

Configuration automatique des applications sur une ferme de serveurs

Pas besoin d'agent, connexion SSH et demande Python

Il peut être couplé avec Terraform

Il intervient sur des intras existants. Terraform crée des infras

Ansible vous fournit des centaines de modules prêts à l'emploi pour gérer vos tâches

# Glossaire

## Variables de connexion

Les variables de connexion sont normalement utilisées pour définir les détails sur la façon d'exécuter des actions sur une cible. La plupart correspondent à des plugins de connexion, mais tous ne leur sont pas spécifiques ; d'autres plugins comme shell, terminal et devenir sont normalement impliqués. Seuls les plus courants sont décrits car chaque plugin de connexion/devenir/shell/etc peut définir ses propres surcharges et variables spécifiques. Voir Contrôler le comportement d'Ansible : règles de priorité pour savoir comment les variables de connexion interagissent avec les paramètres de configuration, les options de ligne de commande et les mots-clés de playbook.

ansible_become_user
The user Ansible ‘becomes’ after using privilege escalation. This must be available to the ‘login user’.

ansible_connection
The connection plugin actually used for the task on the target host.

ansible_host
The ip/name of the target host to use instead of inventory_hostname.

ansible_python_interpreter
The path to the Python executable Ansible should use on the target host.

ansible_user
The user Ansible ‘logs in’ as.



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

## Le mode Ad Hoc ou Commandes Imperative
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




```
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
```

# Fonctions Rolling Update : (un niveau de playbook)

Fonctions permettant de définir la stratégie de déploiement que devra utiliser ansible lors de l’éxécution d’un playbook.
Par defaut chaque tache est efectue en simultané. 
Avec Rolling Update, les taches se font l'une apres l'autre.
grace avec le parametres "serial"


Le mot-clé serial indique à Ansible sur combien de serveurs opérer à la fois. Si ce n'est pas spécifié, Ansible parallélisera ces opérations jusqu'à la limite de « forks » par défaut spécifiée dans le fichier de configuration. Mais pour une mise à niveau progressive sans interruption, vous ne voudrez peut-être pas opérer sur autant d'hôtes à la fois. Si vous n'aviez qu'une poignée de serveurs Web, vous souhaiterez peut-être définir le numéro de série sur 1, pour un hôte à la fois. Si vous en avez 100, vous pourriez peut-être définir la série sur 10, pour dix à la fois.

Rolling Update – max_fail_percentage
% d'erreur authorisé sur les hots (tous)

si il y a plus de 50% d'erreur sur mon playbook de 4 hots, il y aura un arrete du déploiuement si nous avons une erreur sur 2 hosts.
any_error_fatal: true 
pour interrompre le depoloiement dans le cas d'une erreur dans une tache.



# Interrupt Execution: 

Fonction permettant d’arreter l’exécution du playbook sur tous les hôtes de l’inventaire au cas où la moindre erreur est enregistrée lors de l’éxécution du playbook.

# Delegate: 

Fonction permettant d’exécuter une commande ou action sur un hôte délégué à partir des facts (gather_facts) de l’hote sur lequel la tache devrait normalement s’exécuter
Pour recuperer les IP des workoers sur mon Master

# delegate_to

Si vous souhaitez effectuer une tâche sur un hôte en référence à d'autres hôtes, utilisez le mot-clé délégué_to sur une tâche. C'est idéal pour gérer les nœuds dans un pool à charge équilibrée ou pour contrôler les fenêtres de panne. Vous pouvez utiliser la délégation avec le mot-clé serial pour contrôler le nombre d'hôtes s'exécutant en même temps :


# delegate_facts
Déléguer des tâches Ansible, c'est comme déléguer des tâches dans le monde réel - vos courses vous appartiennent, même si quelqu'un d'autre les livre à votre domicile. De même, tous les faits collectés par une tâche déléguée sont affectés par défaut à l'inventaire_hostname (l'hôte actuel), et non à l'hôte qui a produit les faits (le délégué à l'hôte). Pour attribuer des faits rassemblés à l'hôte délégué au lieu de l'hôte actuel, définissez délégué_facts sur true :

# Run Once: 
(fonctionne avec serial)
Fonction permettant d’exécuter une tache juste une seule fois peu importe le nombre d’hotes définis dans le fichier d’inventaire. Toutefois, il est important de noter que le Run Once s’applique pour chaque groupe d’hotes d’exécution simultané défini au niveau de « serial »

# Ignore_errors: 
[si il y a une erreur sur un worker, il n'y aura plus d'execution de tache, sauf avec Ignore_errors au niveau des taches.]

Fonction permettant d’ignorer les erreurs au niveau des taches et ainsi de ne pas arrêter l’exécution d’un playbook sur un hôte en cas d’échec ou d’erreurs au niveau de ladite tache

Ignore_unreacheable: 
Fonction permettant d’ignorer l’erreur sur une tache si celle-ci est due au fait que l’hote soit injoignable

# Register: 

Fonction permettant sauvegarder les variables de retour d’un modules dans une variable de notre choix. NB: Prendre connaissance des variables disponibles (return values) pour chacun des modules à utiliser


Enregistrement de variables

Vous pouvez créer des variables à partir de la sortie d'une tâche Ansible avec le registre de mot-clé de tâche. Vous pouvez utiliser des variables enregistrées dans toutes les tâches ultérieures de votre jeu. Par example: 
- hosts: web_servers

  tasks:

     - name: Run a shell command and register its output as a variable
       ansible.builtin.shell: /usr/bin/foo
       register: foo_result
       ignore_errors: true

     - name: Run a shell command using output of the previous task
       ansible.builtin.shell: /usr/bin/bar
       when: foo_result.rc == 5



Les variables enregistrées peuvent être des variables simples, des variables de liste, des variables de dictionnaire ou des structures de données imbriquées complexes. La documentation de chaque module comprend une section RETURN décrivant les valeurs de retour pour ce module. Pour voir les valeurs d'une tâche particulière, exécutez votre playbook avec -v. 

Les variables enregistrées sont stockées en mémoire. Vous ne pouvez pas mettre en cache les variables enregistrées pour une utilisation dans des jeux futurs. Inscrit les variables ne sont valides sur l'hôte que pour le reste de l'exécution du playbook en cours. 

Les variables enregistrées sont des variables au niveau de l'hôte. Lorsque vous enregistrez une variable dans une tâche avec une boucle, la variable enregistrée contient une valeur pour chaque élément de la boucle. La structure de données placée dans la variable pendant la boucle contiendra un attribut de résultats, c'est-à-dire une liste de toutes les réponses du module. Pour un exemple plus détaillé de la façon dont cela fonctionne, consultez la section Boucles sur l'utilisation de register avec une boucle.

# Handlers
Parfois, vous souhaitez qu'une tâche ne s'exécute que lorsqu'une modification est apportée sur une machine. Par exemple, vous souhaiterez peut-être redémarrer un service si une tâche met à jour la configuration de ce service, mais pas si la configuration est inchangée. Ansible utilise des gestionnaires pour traiter ce cas d'utilisation. Les gestionnaires sont des tâches qui ne s'exécutent que lorsqu'elles sont notifiées. Chaque gestionnaire doit avoir un nom globalement unique.


# Rôles: 
## Rendre les playbook réutilisables

Les rôles vous permettent de charger automatiquement les variables, fichiers, tâches, gestionnaires et autres artefacts Ansible associés en fonction d'une structure de fichiers connue. Après avoir regroupé votre contenu dans des rôles, vous pouvez facilement les réutiliser et les partager avec d'autres utilisateurs.

# Structure du répertoire des rôles

Un rôle Ansible a une structure de répertoires définie avec huit répertoires standard principaux. Vous devez inclure au moins un de ces répertoires dans chaque rôle. Vous pouvez omettre tous les répertoires que le rôle n'utilise pas. 

## Par example:
```ruby
# playbooks
site.yml
webservers.yml
fooservers.yml
roles/
    common/
        tasks/
        handlers/
        library/
        files/
        templates/
        vars/
        defaults/
        meta/
    webservers/
        tasks/
        defaults/
        meta/
```
Par défaut, Ansible recherchera dans chaque répertoire d'un rôle un fichier main.yml pour le contenu pertinent

* task/main.yml - la liste principale des tâches exécutées par le rôle.

* handlers/main.yml - gestionnaires, qui peuvent être utilisés à l'intérieur ou à l'extérieur de ce rôle. 

* library/my_module.py - modules, qui peuvent être utilisés dans ce rôle (voir Intégrer des modules et des plugins dans des rôles pour plus d'informations). 

* defaults/main.yml - variables par défaut pour le rôle (voir Utilisation des variables pour plus d'informations). Ces variables ont la priorité la plus basse de toutes les variables disponibles et peuvent être facilement remplacées par toute autre variable, y compris les variables d'inventaire.

* vars/main.yml - autres variables pour le rôle (voir Utilisation de variables pour plus d'informations). 

* files/main.yml - fichiers que le rôle déploie. 

* templates/main.yml - modèles que le rôle déploie. 

* meta/main.yml - métadonnées pour le rôle, y compris les dépendances de rôle.

# Stocker et rechercher des rôles

Par défaut, Ansible recherche des rôles dans les emplacements suivants : 

dans les collections, si vous les utilisez dans un répertoire appelé roles/, relatif au fichier playbook dans le role_path configuré. 

Le chemin de recherche par défaut est ~/.ansible/roles:/usr/share/ansible/roles:/etc/ansible/roles. 

dans le répertoire où se trouve le fichier playbook 

Si vous stockez vos rôles dans un emplacement différent, définissez l'option de configuration roles_path afin qu'Ansible puisse trouver vos rôles. L'archivage des rôles partagés dans un seul emplacement facilite leur utilisation dans plusieurs playbooks. 

Voir Configuration d'Ansible pour plus de détails sur la gestion des paramètres dans ansible.cfg. 

Vous pouvez également appeler un rôle avec un chemin complet :
```ruby
---
- hosts: production
  roles:
    - role: '/path/to/my/roles/common'
```


## comsommer la ressources
ansible-galaxy ......

ex: odoo

git submodule add https://github.com/sadofrazer/odoo_role.git roles/odoo

oddo.yaml
  name:
  hosts: production
  become: true
  roles: 
    - odoo

hosts.yaml

(git clone submodule ou git clone)
roles
  odoo
    readme.md
    defaults
    galaxy.ymal
    meta
    tasks
    templates
    tests

playbook-ansible -i hosts.yaml odoo.yaml

ou

## methode galaxy
( pas de repertoire à créé)

vi oddo.yaml
  name:
  hosts: production
  become: true
  roles: 
    - odoo

vi hosts.yaml

vi requirements.yaml
# e nom de la ressource à consommer
- src:  sadofrazer.odoo_role




ansible-galaxy  install -r requirements.yaml

## si je cherche un seul role
ansible-galaxy install sadofrazer.odoo_role

ubuntu@AnsibleMaster:~$ ansible-galaxy role init docker_role
- Role docker_role was created successfully


meta et galaxy pour pousser sur galaxy-ansyble

# Ansible Tower
GESTION ET PROGRMTION DES PLABOOKS AVEC DES WEBHOOKS


* Projets

Les projets sont des collections logiques de playbooks dans Ansible Tower. Ces playbooks résident soit sur l’instance d’Ansible Tower, soit dans un système de contrôle de version de code source pris en charge par Tower.

* Inventaires

Un inventaire est une collection d’hôtes sur lesquels des travaux peuvent être lancés, comme un fichier d’inventaire Ansible. Les inventaires sont divisés en groupes et ces groupes contiennent les hôtes réels. Les groupes peuvent être remplis manuellement, en entrant des noms d’hôtes dans Tower, à partir de l’un des fournisseurs de cloud pris en charge par Ansible Tower ou via des scripts d’inventaire dynamique.

* Identifiants

Les informations d’identification sont utilisées par Tower pour l’authentification lors du lancement de Jobs sur des machines, la synchronisation avec les sources d’inventaire et l’importation de contenu de projet à partir d’un système de contrôle de version. La configuration des informations d’identification se trouve dans les paramètres.

Les informations d’identification de Tower sont importées et stockées chiffrées dans le système et ne peuvent être récupérées en texte brut sur la ligne de commande par aucun utilisateur. Vous pouvez accorder aux utilisateurs et aux équipes la possibilité d’utiliser ces informations d’identification, sans réellement exposer les informations d’identification à l’utilisateur.

* Modèles

Un modèle de travail est une définition et un ensemble de paramètres permettant d’exécuter un travail Ansible. Les modèles de travaux sont utiles pour exécuter plusieurs fois le même travail. Les modèles de tâches encouragent également la réutilisation du contenu du playbook Ansible et la collaboration entre les équipes. Pour exécuter un travail, Tower requiert que vous créiez d’abord un modèle de travail.

* taches

Une taches est essentiellement une instance d’un playbook Ansible lancé par Tower sur un inventaire d’hôtes.

* Jobs = playbooks

