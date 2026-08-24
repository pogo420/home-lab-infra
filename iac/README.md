# Ansible setup

* Virtual env and install ansible `pip install -r requirements.txt`
* Running playbooks
  ```shell
  # Comma after IP is important
  ansible-playbook -i <IP>, playbooks/bootstrap.yml -u <user_name> -e "ansible_port=<ssh_port>"
  ansible-playbook -i <IP>, playbooks/security.yml -u <user_name> -e "ansible_port=<ssh_port>"
  ansible-playbook -i <IP>, playbooks/firewall.yml -u <user_name> -e "ansible_port=<ssh_port>"
  ansible-playbook -i <IP>, playbooks/kube_cluster.yml -u <user_name> -e "ansible_port=<ssh_port>"
  ```
