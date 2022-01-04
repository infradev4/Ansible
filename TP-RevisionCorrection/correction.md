# Correction : 
## Révision des notions abordées
1. Supprimez tous vos instances et recréez les de Zéro ( 01 ansible master et 02 ansible Worker)
2. Réalisez les différentes configurations afin d’autoriser l’accès ssh par mot de passe
3. Réalisez votre fichier d’inventaire afin d’intégrer ces différents hôtes et testez l’authentification garce au
module ping
4. Sécurisez vos informations sensibles à travers un fichier secrets.yml, cryptez le à l’aide de vault et créez un fichier de configuration permettant l’élévation de privilège et la demande du mot de passe vault automatiquement
5. Réalisez un playbook permettant de modifier le nom des différents hôtes à l’aide d’une variable d’environnement « hostname » qui pourra être surchargée lors du lancement du Play
6. Réalisez un autre playbook permettant d’installer docker et les divers prérequis permettant d’utiliser le module ansible docker_container
7. Créez un nouveau playbook /manifest qui utilisera (import) les 02 précédents afin de changer le nom des l’hotes, d’installer docker et ses prérequis et pour terminer de lancer un container nginx afin d’heberger le site web se trouvant sur le repo suivant : https://github.com/diranetafen/static-website-example.git

`mkdir -p revision/{templates}`

vi hosts.yaml

```ruby
all:
  children:
    ansible:
      hosts:
        localhost:
          ansible_connection: local
          ansible_user: ubuntu
          ansible_password: '{{ ansible_vault_password }}'
          hostname: AnsibleMaster
    prod:
      vars:
        env: production

      hosts:
        worker01:
          ansible_host: 172.31.14.210
          ansible_user: ubuntu
          ansible_password: '{{ ansible_vault_password }}'
          #accepter automatiquement les clés d'hôte SSH
          ansible_ssh_common_args: '-o StrictHostKeyChecking=no'
          hostname: AnsibleWorker01

        worker02:
          ansible_host: 172.31.0.185
          ansible_user: ubuntu
          ansible_password: '{{ ansible_vault_password }}'
          #accepter automatiquement les clés d'hôte SSH
          ansible_ssh_common_args: '-o StrictHostKeyChecking=no'
          #varible use on the file "hostname.yaml" for edit the name of the hosts
          hostname: AnsibleWorker02
```

vi docker.yaml
```ruby
---
- name: "Install docker"
  hosts: prod
  #I indicate the file which contains the encrypted password
  vars_files:  secret.yaml

  tasks:
    - name: Download Install docker script
      get_url:
        url: "https://get.docker.com"
        dest: /home/ubuntu/get-docker.sh
      #installation seulement si le client possede la varibale "ansible_docker0" en non définies
      when: ansible_docker0 is undefined  

    - name: run script to install docker
      command: "sh /home/ubuntu/get-docker.sh"
      #Lancement si le client possede la varibale "ansible_docker0" en non définies
      when: ansible_docker0 is undefined

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

vi webappNginx.yaml
```ruby
- name: "Deploy a webapp nginx"
  hosts: prod
  #I indicate the file which contains the encrypted password
  vars_files:  secret.yaml

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
        src: templates/index.html.j2
        dest: /home/ubuntu/static-website/index.html

    - name: "Create nginx container"
      docker_container:
        name: nginx
        image: nginx
        ports:
          - "8080:80"
        volumes:
          - "/home/ubuntu/static-website:/usr/share/nginx/html/"
```

vi hostname.yaml
```ruby
-  name: "edit the name of our host"
   hosts: all
   vars_files:  secret.yaml
   tasks:
     -  name: "edit the name of the hosts"
        #j'ai definie la varible "hostname" dans le fichier "hosts" ex: hostname: AnsibleWorker01
        #wreat with "ini" methode hear
        hostname: name="{{ hostname }}"
```

vi deployWebappNginx.yaml
```ruby
---
  #le chemin du fichier "secret.yaml" doit etre indiqué dans les playbooks, pas içi.
- name: "change the name of hosts"
  import_playbook: hostname.yaml
  #vars_files ne peut pas etre utilisé dans include

- name: "Install docker with prerequises"
  import_playbook: docker.yaml


- name: "deploy webapp with nginx"
  import_playbook: webappNginx.yaml 
```


vi secret.yaml
```ruby
ansible_vault_password:  ubuntu
```

vi ansible.cfg
```ruby
#exécuter des tâches avec des privilèges root
[privilege_escalation]
become = true

[defaults]
#demande le mot de pass vault a chaque lancement
ask_vault_pass = true 
```

ubuntu@ip-172-31-0-239:~/tpRevision/correction$ ansible-playbook -i hosts.yaml deployWebappNginx.yaml
Vault password:

PLAY [edit the name of our host] ***********************************************************************************************************************

TASK [Gathering Facts] *********************************************************************************************************************************
ok: [localhost]
ok: [worker02]
ok: [worker01]

TASK [edit the name of the hosts] **********************************************************************************************************************
ok: [localhost]
ok: [worker01]
ok: [worker02]

PLAY [Install docker] **********************************************************************************************************************************

TASK [Gathering Facts] *********************************************************************************************************************************
ok: [worker01]
ok: [worker02]

TASK [Download Install docker script] ******************************************************************************************************************
skipping: [worker01]
skipping: [worker02]

TASK [run script to install docker] ********************************************************************************************************************
skipping: [worker01]
skipping: [worker02]

TASK [give the privilège to ubuntu] ********************************************************************************************************************
ok: [worker02]
ok: [worker01]

TASK [install python pip] ******************************************************************************************************************************
ok: [worker01]
ok: [worker02]

TASK [install docker-py module] ************************************************************************************************************************
ok: [worker02]
ok: [worker01]

PLAY [Deploy a webapp nginx] ***************************************************************************************************************************

TASK [Gathering Facts] *********************************************************************************************************************************
ok: [worker01]
ok: [worker02]

TASK [Create directory] ********************************************************************************************************************************
ok: [worker02]
ok: [worker01]

TASK [Git Clone] ***************************************************************************************************************************************
ok: [worker02]
ok: [worker01]

TASK [Copy Index File From Template] *******************************************************************************************************************
changed: [worker02]
changed: [worker01]

TASK [Create httpd container] **************************************************************************************************************************
changed: [worker01]
changed: [worker02]

PLAY RECAP *********************************************************************************************************************************************
localhost                  : ok=2    changed=0    unreachable=0    failed=0    skipped=0    rescued=0    ignored=0
worker01                   : ok=11   changed=2    unreachable=0    failed=0    skipped=2    rescued=0    ignored=0
worker02                   : ok=11   changed=2    unreachable=0    failed=0    skipped=2    rescued=0    ignored=0


