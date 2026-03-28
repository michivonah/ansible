# install-podman
Installs Podman on a linux based system and sets up a dedicated user for running rootless containers.

Podman: https://podman.io/

## Inventory variables
```yaml
your_servers:
  vars:
    podman_user: podman-user
    
  hosts:
    srv-1:
      ansible_host: <HOSTNAME>
      ansible_user: <USER>
    srv-2:
      ansible_host: <HOSTNAME>
      ansible_user: <USER>
    srv-3:
      ansible_host: <HOSTNAME>
      ansible_user: <USER>
```

## Running playbook
Run playbook
```bash
ansible-playbook -i inventory.yaml install-podman/playbook.yaml
```