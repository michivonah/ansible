# podman-duplicati-backup
Runs duplicati backup as podman container.

Docs: https://docs.duplicati.com/
Docker Hub: https://hub.docker.com/r/duplicati/duplicati

## Required variables
- `podman_user`

## Dependencies
```bash
ansible-galaxy collection install containers.podman
```

## Running playbook
Run playbook
```bash
ansible-playbook -i inventory.yaml podman-duplicati-backup/playbook.yaml
```