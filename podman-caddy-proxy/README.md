# podman-caddy-proxy
Runs caddy proxy as Podman container.

Caddy docs: https://caddyserver.com/docs/getting-started

## Inventory example
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
ansible-playbook -i inventory.yaml podman-caddy-proxy/playbook.yaml
```