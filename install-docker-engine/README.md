# install docker engine + setup docker swarm mode
Installs the docker engine on a RHEL based linux server and sets up docker swarm mode.

## Inventory variables
```yaml
swarm_cluster:
  vars:
    swarm_init_host: srv-1
    
  hosts:
    srv-1:
      ansible_host: <HOSTNAME>
      ansible_user: <USER>
      swarm_manager: <true|false>
    srv-2:
      ansible_host: <HOSTNAME>
      ansible_user: <USER>
      swarm_manager: <true|false>
    srv-3:
      ansible_host: <HOSTNAME>
      ansible_user: <USER>
      swarm_manager: <true|false>
```

## Dependencies

Install dependencies
```bash
ansible-galaxy collection install community.docker
```

Run playbook
```bash
ansible-playbook -i inventory.yaml install-docker-engine/playbook.yaml
```

## Port requiremens for docker swarm mode
- Port 2377 TCP: communication with and between manager nodes
- Port 7946 TCP/UDP: overlay network node discovery
- Port 4789 UDP: overlay network traffic