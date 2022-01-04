# TP-8: Déployez un serveur web

• Créez un dossier webapp qui va contenir tous les fichiers de notre projet

`ubuntu@AnsibleMaster:~$ pwd`

/home/ubuntu

`mkdir webapp`

• Créez un fichier d’inventaire appelé prod.yml (au format yaml) contenant un groupe prod avec comme membre nos workers et un groupe ansible avec comme membre localhost.

`vi /home/ubuntu/webapp/prod.yaml`

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

        worker02:
          ansible_host: 172.31.13.73

```

• Créez un dossier group_vars qui va contenir un fichier nommé prod qui contiendra les informations de connexion à utiliser par ansible


`/home/ubuntu/webapp`

`mkdir /home/ubuntu/webapp/group_vars`
  
`vi /home/ubuntu/webapp/group_vars/prod.yaml`

```ruby
env: prod
ansible_user: ubuntu
ansible_password: ubuntu
ansible_ssh_common_args: '-o StrictHostKeyChecking=no'
```

• Créez un playbook nommé nginx.yml permettant de déployez nginx et de créer un fichier index.html contenant votre prénom que vous copierez dans le repertoire par défaut du serveur nginx

`vi /home/ubuntu/webapp/nginx.yaml`
```ruby
---
# Playbook 1 de mon manifeste nginx.yaml
- name: "Install nginx"
  hosts: worker01 # définissent la portée en désignant l'hôte de l'inventaire prod.yaml
  become: true # Indique l’usage de l’élévation de privilège
  vars:
    env: playbookuuuuuu
#  vars_files:
#    - /home/ubuntu/nginx/env.yml

  pre_tasks:
    - name: "Test debug env var"
      debug: # affiche des messages sur la sortie standard.
        msg: "{{ env }}"

  tasks: # définition de la liste des taches
    - name: "install nginx" # nom de la tache 1
      package: # gère les packages sur une cible sans spécifier le module (yum apt)
        name: nginx # Nom du package
        state: present # Que ce soit pour installer (present) ou supprimer (absent) un package.


    - name: "start nginx"
      service: # module Contrôle les services sur les hôtes distants.
        name: nginx # Nom du service.
        enabled: yes # Si le service doit démarrer au démarrage.
        state: started

    - name: "delete index.html if exist"
      file: # module Créer ou supprimer des fichiers
        path: /var/www/html/index.html # Chemin d'accès au fichier géré.
        state: absent # les répertoires seront supprimés de manière récursive

    - name: create index file for nginx
      copy: # Le module copie un fichier de la machine locale vers un emplacement sur la machine distante.
        dest: /var/www/html/index.html # Si dest est un chemin inexistant et si dest se termine par "/" 
        content: "Bonjour Oussama" # Fonctionne uniquement lorsque dest est un fichier. Crée le fichier s'il n'existe pas.

# Playbook 2 de mon manifeste nginx.yaml
- name: "Afficher env"
  hosts: worker02
  become: true
  vars:
    env: playbookuuuuuu
#  vars_files:
#    - /home/ubuntu/nginx/env.yml
  tasks:
    - name: "Test debug env var"
      debug:
        msg: "{{ env }}"
```

`ansible-playbook -i /home/ubuntu/webapp/prod.yaml /home/ubuntu/webapp/nginx.yaml`

```ruby
PLAY [Install nginx] *******************************************************************************************************************************************************************

TASK [Gathering Facts] *****************************************************************************************************************************************************************
ok: [worker01]

TASK [Test debug env var] **************************************************************************************************************************************************************
ok: [worker01] => {
    "msg": "playbookuuuuuu"
}

TASK [install nginx] *******************************************************************************************************************************************************************
ok: [worker01]

TASK [start nginx] *********************************************************************************************************************************************************************
ok: [worker01]

TASK [delete index.html if exist] ******************************************************************************************************************************************************
changed: [worker01]

TASK [create index file for nginx] *****************************************************************************************************************************************************
changed: [worker01]

PLAY [Afficher env] ********************************************************************************************************************************************************************

TASK [Gathering Facts] *****************************************************************************************************************************************************************
ok: [worker02]

TASK [Test debug env var] **************************************************************************************************************************************************************************************************************
ok: [worker02] => {
    "msg": "playbookuuuuuu"
}

PLAY RECAP *****************************************************************************************************************************************************************************************************************************
worker01                   : ok=6    changed=2    unreachable=0    failed=0    skipped=0    rescued=0    ignored=0
worker02                   : ok=2    changed=0    unreachable=0    failed=0    skipped=0    rescued=0    ignored=0

```

• Vérifiez la syntaxe de votre playbook avec la commande ansible-lint (installez là si elle n’est pas disponible)

`sudo apt-get install ansible-lint -y`

`ansible-lint /home/ubuntu/webapp/nginx.yaml`
```ruby
[201] Trailing whitespace
webapp/nginx.yaml:36
```