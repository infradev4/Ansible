# TP-10: JinJa désinstaller nginx
• Ecrivez un playbook ansible qui permet maintenant de désinstaller nginx sur le
worker02 car on a jugé en production que cela n’est plus nécessaire

1) Ecrivez le script (desinstall_nginx.sh) et templatisez le avec une structure
conditionnelle testant la distribution Linux (Ubuntu ou Centos) avant l’exécution des commandes

`vi /home/ubuntu/webapp/templates/desinstall_nginx.sh.j2`

```ruby
#!/bin/bash
{% if ansible_distribution == "Ubuntu" %}
   sudo apt-get purge --autoremove {{ app }} -y
{% elif ansible_distribution == "Centos" %}
   sudo yum purge --autoremove {{ app }} -y
{% else %}
   echo 'Distribution pas prise en charge'
{% endif %}
```

2) Creez un nouveau playbook dans votre précedent manifest qui ne s’exécutera que sur l’hote Worker02 et qui chargera et exécutera le template ou le script emplatisé afin de désisnatller nginx
3) Pensez à variabiliser le paquet à installer, l’utilisateur doit pouvoir hoisir le paquet à installer au moment du lancement de la commande ansible

`vi /home/ubuntu/webapp/nginxjinjaInstallerEtDesinstaller.yaml`

```ruby
---
- name: "Install nginx"
  hosts: prod
  become: true
  vars:
    app: nginx
  
  tasks:
    - name: "Copy Script files"
      template:
        src: /home/ubuntu/webapp/templates/install_nginx.sh.j2
        dest: /home/ubuntu/install_nginx.sh

    - name: "Install nginx"
      command: "sh /home/ubuntu/install_nginx.sh"

    - name: "start nginx"
      service:
        name: nginx
        enabled: yes
        state: started

    - name: create index file for nginx
      copy:
        dest: /var/www/html/index.html
        content: "Bonjour Oussama"

- name: "Desinstall nginx"
  hosts: worker02
  become: true
  vars:
    app: nginx
  
  tasks:
    - name: "Copy script file"
      template:
        src: /home/ubuntu/webapp/templates/desinstall_nginx.sh.j2
        dest: /home/ubuntu/desinstall_nginx.sh

    - name: desinstall nginx
      command: "sh /home/ubuntu/desinstall_nginx.sh"
```

4) Testez votre playbook et vérifiez que nginx a été correctement désinstallé du worker02

`ansible-playbook -i /home/ubuntu/webapp/prod.yaml /home/ubuntu/webapp/nginxjinjaInstallerEtDesinstaller.yaml`

```ruby

PLAY [Install nginx] ***********************************************************************************************************************************

TASK [Gathering Facts] *********************************************************************************************************************************
ok: [worker02]
ok: [worker01]

TASK [Copy Script files] *******************************************************************************************************************************
ok: [worker02]
ok: [worker01]

TASK [Install nginx] ***********************************************************************************************************************************
changed: [worker02]
changed: [worker01]

TASK [start nginx] *************************************************************************************************************************************
ok: [worker01]
ok: [worker02]

TASK [create index file for nginx] *********************************************************************************************************************
ok: [worker01]
ok: [worker02]

PLAY [Desinstall nginx] ********************************************************************************************************************************

TASK [Gathering Facts] *********************************************************************************************************************************
ok: [worker02]

TASK [Copy script file] ********************************************************************************************************************************
changed: [worker02]

TASK [desinstall nginx] ********************************************************************************************************************************
changed: [worker02]

PLAY RECAP *********************************************************************************************************************************************
worker01                   : ok=5    changed=1    unreachable=0    failed=0    skipped=0    rescued=0    ignored=0
worker02                   : ok=8    changed=3    unreachable=0    failed=0    skipped=0    rescued=0    ignored=0
```