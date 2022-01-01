ubuntu@AnsibleMaster:~/webappApacheVault$ ls
apache.yaml  deploy_apache.yaml  docker.yaml  group_vars  password.yaml  prod.yaml  templates


ubuntu@AnsibleMaster:~/webappApacheVault$ grep -r ansible_vault_sudo_pass

password.yaml:ansible_vault_sudo_pass:  ubuntu
group_vars/prod.yaml:ansible_password: "{{ ansible_vault_sudo_pass }}"

ubuntu@AnsibleMaster:~/webappApacheVault$ cat password.yaml
ansible_vault_sudo_pass:  ubuntu

ubuntu@AnsibleMaster:~/webappApacheVault$ cat group_vars/prod.yaml
env: prod
ansible_user: ubuntu
ansible_password: "{{ ansible_vault_sudo_pass }}"
ansible_ssh_common_args: '-o StrictHostKeyChecking=no'



ubuntu@AnsibleMaster:~/webappApacheVault$ vi ansible.cfg

# password.yaml

ansible_vault_sudo_pass: ubuntu

# encrypt
ansible-vault encrypt ./password.yaml

New Vault password:
Confirm New Vault password:
Encryption successful

# /group/vars/prod.yml

ansible_user: ubuntu
ansible_sudo_pass: "{{ ansible_vault_sudo_pass }}"
ansible_ssh_common_args: -o StrictHostKeyChecking=no

# vi ansible.cfg

[defaults]
inventory=/home/ubuntu/webappApacheVault/prod.yaml
ask_vault_pass = true

# nginx.yaml
- name: "Install nginx"
  hosts: worker01
  become: true
  vars:
    env: playbookuuuuuu
  vars_files:
    - /home/ubuntu/webappApacheVault/password.yaml
