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