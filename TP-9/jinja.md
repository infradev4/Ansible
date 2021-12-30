# TP-9: JinJa
• Ecrivez un playbook ansible qui permet d’installer nginx sur des machines distantes en fonction de la distribution de Linux à l’aide d’un script

1) Ecrivez le script (install_nginx.sh) et templatisez le avec une structure conditionnelle testant la distribution Linux (Ubuntu ou Centos) avant l’exécution des commandes

`mkdir /home/ubuntu/webapp/templates`

`vi /home/ubuntu/webapp/templates/install_nginx.sh.j2`

```ruby
#!/bin/bash
{% if ansible_distribution == "Ubuntu" %}
   sudo apt-get install {{ app }} -y
{% elif ansible_distribution == "Centos" %}
   sudo yum install {{ app }} -y
{% else %}
   echo 'Distribution pas prise en charge'
{% endif %}
```

2) Ecrivez le playbook qui chargera et exécutera le template ou le script templatisé sur les hotes distants


`vi /home/ubuntu/webapp/nginxjinja.yaml`

```ruby
---
- name: "Install nginx"
  hosts: prod
  become: true
  vars:
    app: nginx # sudo apt-get install {{ app }} -y
 
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
```    
3) Pensez à variabiliser le paquet à installer, l’utilisateur doit pouvoir choisir le paquet à installer au moment du lancement de la commande ansible

4) Testez votre playbook et consommez vos serveurs web

`ansible-playbook -i /home/ubuntu/webapp/prod.yaml /home/ubuntu/webapp/nginxjinja.yaml`

```ruby
PLAY [Install nginx] ***********************************************************************************************************************************

TASK [Gathering Facts] *********************************************************************************************************************************
ok: [worker01]
ok: [worker02]

TASK [Copy Script files] *******************************************************************************************************************************
ok: [worker01]
ok: [worker02]

TASK [Install nginx] ***********************************************************************************************************************************
changed: [worker01]
changed: [worker02]

TASK [start nginx] *************************************************************************************************************************************
ok: [worker01]
ok: [worker02]

TASK [create index file for nginx] *********************************************************************************************************************
ok: [worker01]
changed: [worker02]

PLAY RECAP *********************************************************************************************************************************************
worker01                   : ok=5    changed=1    unreachable=0    failed=0    skipped=0    rescued=0    ignored=0
worker02                   : ok=5    changed=2    unreachable=0    failed=0    skipped=0    rescued=0    ignored=0
```
# Resultat sur mon worker01
```ruby
ubuntu@AnsibleWorker01:~$ ls
frazer.txt  install_nginx.sh
ubuntu@AnsibleWorker01:~$ cat install_nginx.sh
#!/bin/bash
   sudo apt-get install nginx -y
```
# Resultat sur mon worker02
```ruby
ubuntu@AnsibleWorker02:~$ ls
frazer.txt  install_nginx.sh
ubuntu@AnsibleWorker02:~$ cat install_nginx.sh
#!/bin/bash
   sudo apt-get install nginx -y
```
