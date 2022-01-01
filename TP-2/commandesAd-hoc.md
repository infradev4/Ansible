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