# Installation:

## AWS

t2.medium	

us-east-1a	

3.224.127.23

## Script de lancement
```ruby
#!/bin/bash
#Install docker
sudo apt-get update -y
sudo apt-get install git wget curl -y
curl -fsSL https://get.docker.com -o get-docker.sh
sh get-docker.sh
sudo usermod -aG docker ubuntu
#Install Docker compose
sudo curl -L "https://github.com/docker/compose/releases/download/1.29.2/docker-compose-$(uname -s)-$(uname -m)" -o /usr/local/bin/docker-compose
sudo chmod +x /usr/local/bin/docker-compose
git clone https://github.com/sadofrazer/tower.git
cd tower/
tar -xzvf awx.tar.gz -C ~/
cd ~/.awx/awxcompose/
docker-compose up -d
```


http://3.224.127.23/#/home

login: admin

mdp: password

roles/requirements.yml

- src: https://github.com/infradev4/roleWordpress.git
- src: infradev4.docker_role
- src: infradev4.docker_role
- src: https://github.com/infradev4/roleDocker.git


https://github.com/infradev4/deploy_wordpress

````
-  name:
   hosts: prod
   become: true
   roles: 
     - roleDocker
     - roleWordpress
````
- roleWordpress
- src: https://github.com/infradev4/roleWordpress.git
- roleDocker
- src: https://github.com/infradev4/roleDocker.git

[sadofrazer tower]
3.84.74.21


git clone pour recuper le script à mettre en user data pour machine aws Ubuntu en t2 medium 

→ navigateur web <ip_publique>:80 

→ login: admin et password: password (par défaut)

Le fichier roles/requirements.yml permet de spécifier les sources à tower afin de télécharger les rôles nécessaires
![Screenshot](assets\a.jpg)
![Screenshot](assets\b.jpg)


# Création projet 
![Screenshot](assets\c.jpg)
```ruby
→ nom: deploy_wordpress_project 
→ details source 
→ URL DU SCM: 
(url du repo git deploy_wordpress) 

https://github.com/infradev4/deploy_wordpress.git

* nom branche: main 
(master ou main) 

→ options de mise a jour scm : 
 * mettre à jour révision au lancement 

(pour toujours récupérer la dernière version sur 
github lors du lancement du projet)

→ enregistrer 
```
![Screenshot](assets\e.jpg)

# Création inventaire 
```ruby
→ inventaire 
→ + 
→ inventaire (classique) 
→ nom: inventory_wordpress 
→ enregistrer 
```

![Screenshot](assets\f.jpg)
```ruby
→ onglet "sources" 
→ + 
→ nom: inventory_src_wordpress 
→ source: provenance d’un projet 
(repo public donc pas besoin de token)

→ selection projet 
→ selection fichier inventaire 
→ hosts.yml 
(celui qui est à la racine du repo) 

→ options de mise à jour scm : mettre à jour au lancement 
→ enregistrer
```
![Screenshot](assets\g.jpg)
```ruby
→ cliquer sur synchronisation pour récupérer l'inventaire 
```
![Screenshot](assets\h.jpg)
```ruby
→ onglet hôtes pour visualiser les hôtes 
→ cliquer sur hôtes pour voir leurs variables 
→ enregistrer 
→ retour inventaire avec logos verts si ok
```
![Screenshot](assets\i.jpg)

# Création credentials 
![Screenshot](assets\j.jpg)
```txt
→ INFORMATIONS D’IDENTIFICATION 
→ + 
→ nom: wordpress_vault 
→ type: coffre-fort
(vault) 
→ organisation: default 
→ MOT DE PASSE DE L'ARCHIVAGE SÉCURISÉ #(ubuntu)
→ enregistrer 
→ + 
```
![Screenshot](assets\k.jpg)
```txt
→ nom: hosts_prod_id 
→ type: machine 
→ organisation: default 
→ NOM D'UTILISATEUR: ubuntu 
→ MOT DE PASSE :ubuntu 
(ou copie de clé privée en fonction du playbook) 
→ MÉTHODE D'ESCALADE PRIVILÉGIÉE: sudo 
→ MOT DE PASSE POUR L’ÉLÉVATION DES PRIVILÈGES: ubuntu 
→ enregistré
```
![Screenshot](assets\l.jpg)

# Création job template 
![Screenshot](assets\m.jpg)
```txt
→ MODÈLES 
→ + 
→ MODÈLES de job 
→ nom: deploy_wordpress_job 
→ TYPE DE JOB: executer 
→ inventaire:inventory_wordpress
→ projet:deploy_wordpress_project 
→ playbook:wordpress.yml 
→ info d’identification: host_prod_id et wordpress_vault 
→ enregistrer 
→ enregistrer
```
![Screenshot](assets\n.jpg)
```txt
Modèle → icone fusée du deploy_wordpress_job pour lancer le job
```
![Screenshot](assets\o.jpg)

# Webhook
![Screenshot](assets\p.jpg)
![Screenshot](assets\q.jpg)
![Screenshot](assets\r.jpg)
![Screenshot](assets\s.jpg)





