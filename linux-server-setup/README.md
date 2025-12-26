# linux-server-setup

Required vars in inventory: `admin_user`, `public_ssh_key_path`

Install dependencies
```bash
ansible-galaxy collection install ansible.posix
```

Run playbook
```bash
ansible-playbook -i inventory.yaml -K linux-server-setup/playbook.yaml
```

It's also possible to run the playbook without `-K` if your execution user doesn't require as sudo password.