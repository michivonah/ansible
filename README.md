# ansible

## General
- Ansible is agentless; this means no agent neeeds to be installed on the target hosts; ssh access is just enough
- Ansible will be run on a control node, which executes the remote tasks.
- Target hosts are also called managed nodes
- Plugins can be used to expand the capabilities of ansible.
- Ansible content can be distributed and shared through Ansible Galaxy. Its like a marketplace.

[Basic concepts](https://docs.ansible.com/projects/ansible/latest/getting_started/basic_concepts.html)

## Inventory
- A inventory is a list of managed nodes/hosts
- All hosts should be added to the inventory.ini file (hostname or ip address).
- The hosts can be grouped; the group name is inside brackets `[]`
- The inventory can be ether a `.ini` or a `.yaml` file
- Its also possible to declare variables inside of the inventory file
- Its possible to define ranges with `[01:50]`. It's also possible to skip some numbers with a stride `[01:50:2]` so every odd number gets used.
- Alphabetical ranges are also possible
- It's possible to configure ansible to escalate privileges after sshing into a host (so it has root privileges)

Verify inventory
```bash
ansible-inventory -i inventory.ini --list 
```

[Source](https://docs.ansible.com/projects/ansible/latest/inventory_guide/intro_inventory.html)


## Run default automation
```bash
ansible test -m ping -i inventory.yaml
```

## Important parameters
| Parameter | Meaning |
| --- | --- |
| `-u` | Defines the user to use for ssh into the servers |
| `-i` | Specifies the inventory file to use |
| `--check` | A sort of dry run; hosts configuration don't gets altered |

## Playbooks
- A playbook is an automation blueprint
- It consists of plays, tasks & modules
- They have a hierarchy:

    - Playbook (List of plays)
        - Play (Ordered list of tasks)
            - Task (Reference to module for executing a task)
                - Module (The code that gets run)

- A playbook is defined in a `playbook.yaml`-file
- A playbook can be run with the `ansible-playbook` command

```bash
ansible-playbook -i inventory.yaml playbook.yaml
```

- A play is a ordered list of tasks with the definition on how to run them (variables, etc.)
- Each play consists of at minium the nodes to target and the tasks to execute
- So this is a play
```yaml
- name: Ping specified hosts
  hosts: test
  tasks:
    - name: Ping
      ansible.builtin.ping:
```
- A task is an action inside of a play, which will be executed by a module
- A module is simply the binary/code that gets run by a task
- Most ansible modules check before execution if the desired state is already met, if this is the case, it won't do anything.

- When you wan't to try a playbook without apply the changes to the system, you can't pass the `--check` flag.
- So ansible will only do a "dry run" of the playbook without altering the system configuration.
- This is great for testing new playbooks before running in production.

- With `ansible-lint` it's possible to check a playbook and get feedback about it.
- Just run `ansible-lint` followed by the playbooks name
```bash
ansible-lint playbook.yaml
```

[Playbooks intro](https://docs.ansible.com/projects/ansible/latest/playbook_guide/playbooks_intro.html)

## Ad-hoc commands
- Ad-hoc commands are an alternative way for running ansible commands
- They are ideal for simple "one command" tasks which were rarely done (like rebooting some servers)
- By default ansible uses the `ansible.builtin.command` module for ad-hoc commands
- For example if you wan't to reboot a group of hosts, run:
```bash
ansible test -i inventory.yaml -a "/sbin/reboot"
```

- With `-m` you can tell ansible to use another module
```bash
ansible test -m ansible.builtin.copy -a "src=/etc/hosts dest=/tmp/hosts"
```

- So for quick command execution ad-hoc commands are ideal.
- But for more complex stuff you should prefer the use of regular yaml playbooks
