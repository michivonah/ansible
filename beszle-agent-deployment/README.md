# beszle-agent-deployment
Installs the beszle-agent as docker container with docker compose on an ssh host.

## Inventory variables
```yaml
all:
  vars:
    beszel:
      hub_url: https://beszel.example.com
      key: 'ssh key'

docker_hosts:
  hosts:
    your_host:
      ansible_host: host
      ansible_user: user
      ansible_become: true
      beszel_token: <TOKEN>
```

## Dependencies

Install dependencies
```bash
ansible-galaxy collection install community.docker
```

Run playbook
```bash
ansible-playbook -i inventory.yaml beszle-agent-deployment/playbook.yaml
```