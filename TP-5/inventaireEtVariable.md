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