# TP-4: Inventaire au format yaml
- Modifiez le fichier hosts que vous avez écris au format INI afin qu’il soit en au format yaml

* Avant:
```Ruby
worker01 ansible_host=172.31.6.70 ansible_user=ubuntu ansible_password=ubuntu ansible_ssh_common_args='-o StrictHostKeyChecking=no'
worker02 ansible_host=172.31.13.73 ansible_user=ubuntu ansible_password=ubuntu ansible_ssh_common_args='-o StrictHostKeyChecking=no'
```

`vi hosts.yaml`

```Ruby
all:
  hosts:
    worker01:
      ansible_host:  172.31.6.70
      ansible_user:  ubuntu
      ansible_password:  ubuntu
      ansible_ssh_common_args:  '-o StrictHostKeyChecking=no'
    worker02:
      ansible_host:  172.31.13.73
      ansible_user:  ubuntu
      ansible_password:  ubuntu
      ansible_ssh_common_args:  '-o StrictHostKeyChecking=no'
```
- Testez à nouveau vos commandes ad-hoc avec le nouveau fichier d’inventaire au format yaml

`ansible -i hosts.yaml worker01 -m ping`
```Ruby
worker01 | SUCCESS => {
    "ansible_facts": {
        "discovered_interpreter_python": "/usr/bin/python3"
    },
    "changed": false,
    "ping": "pong"
}
```