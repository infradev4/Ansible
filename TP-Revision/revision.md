# TP : Révision des notions abordées
1. Supprimez tous vos instances et recréez les de Zéro ( 01 ansible master et 02 ansible Worker)
2. Réalisez les différentes configurations afin d’autoriser l’accès sh par mot de passe
3. Réalisez votre fichier d’inventaire afin d’intégrer ces différents hôtes et testez l’authentification garce au
module ping
4. Sécurisez vos informations sensibles à travers un fichier secrets.yml, cryptez le à l’aide de vault et créez un
fichier de configuration permettant l’élévation de privilège et la demande du mot de passe vault
automatiquement
5. Réalisez un playbook permettant de modifier le nom des différents hôtes à l’aide d’une variable
d’environnement « hostname » qui pourra être surchargée lors du lancement du Play
6. Réalisez un autre playbook permettant d’installer docker et les divers prérequis permettant d’utiliser le
module ansible docker_container
7. Créez un nouveau playbook /manifest qui utilisera (import) les 02 précédents afin de changer le nom des
l’hotes, d’installer docker et ses prérequis et pour terminer de lancer un container nginx afin d’heberger
le site web se trouvant sur le repo suivant : https://github.com/diranetafen/static-website-example.git

ansible-playbook -i prod.yaml deploy_apache.yaml

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


- name: "Install container apache with static-website"
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

    - name: "Create httpd container"
      docker_container:
        name: webapp
        image: httpd
        ports:
          - "8081:80"
        volumes:
          - "/home/ubuntu/static-website:/usr/local/apache2/htdocs/"


sudo hostnamectl set-hostname AnsibleWorker01