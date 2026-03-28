# forgejo-setup
Installs forgejo as Podman container with PostgreSQL as DB.

Forgejo docs: https://forgejo.org/docs/latest/admin/installation/docker/#using-rootless-image

## Architecture
- Podman network `forgejo`
- Podman pod `forgejo`
- Container `forgejo-app`
- Container `forgejo-db`

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

## Dependencies
```bash
ansible-galaxy collection install containers.podman
```

## Running playbook
Run playbook
```bash
ansible-playbook -i inventory.yaml forgejo-setup/playbook.yaml
```