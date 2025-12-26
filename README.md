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
- It's possible to configure ansible to [escalate privileges](https://docs.ansible.com/projects/ansible/latest/playbook_guide/playbooks_privilege_escalation.html) after sshing into a host (so it has root privileges)

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

- When you wan't to try a playbook without apply the changes to the system, you can pass the `--check` flag.
- So ansible will only do a "dry run" of the playbook without altering the system configuration.
- This is great for testing new playbooks before running in production.

- With `ansible-lint` it's possible to check a playbook and get feedback about it.
- Just run `ansible-lint` followed by the playbooks name
```bash
ansible-lint playbook.yaml
```

[Playbooks intro](https://docs.ansible.com/projects/ansible/latest/playbook_guide/playbooks_intro.html)

- With `become` you can esacalate privileges [More details](https://docs.ansible.com/projects/ansible/latest/playbook_guide/playbooks_privilege_escalation.html)

### Templating
- Its possible to use jinja2 templates to create dynamic expressions with data like ip address, hostname or environmental information
- Templates get calculated on the control node and then will be sent, to the hosts. The hosts don't notice anything from the templating process.

[Source](https://docs.ansible.com/projects/ansible/latest/playbook_guide/playbooks_templating.html)

- Jinja2 supports inline conditionals
```yaml
"{{ 'wheel' if ansible_os_family == 'RedHat' else 'sudo' }}"
```

[python - Jinja2 shorthand conditional - Stack Overflow](https://stackoverflow.com/questions/14214942/jinja2-shorthand-conditional)

### Filters
- Filters are used to transform something into something for example json data to yaml data or generating a hash out of a string.
- Those filter transformations are aswell (same as with templating) executed on the control node.
- With filters its also possible to defined variables as mandatory or providing default values

Example for a default value
```yaml
{{ some_variable | default(5) }}
```

- Its also possible to set a default value if a boolean is false/true
- By default ansible requires values for custom set variables. To decalre a variable as optional `omit` needs to be set as default value
```yaml
{{ item.mode | default(omit) }}
```

To define a value as mandator, use `mandatory`
```yaml
{{ variable | mandatory }}
```

Its also possible to define a hint/custom error message, if a required value in undefined
```yaml
galaxy_api_key: "{{ undef(hint='You must specify your Galaxy API key') }}"
```

- A overview of the possibilites is provided on the corresponding ansible docs page.


[Source](https://docs.ansible.com/projects/ansible/latest/playbook_guide/playbooks_filters.html)

### Tests
- It's possible to define tests/conditions for each tasks, which need to be fulfilled, so the task gets run
- This can be also used for checking if some var is truthy

Example:
```yaml
- debug:
    msg: "Truthy"
  when: value is truthy
  vars:
    value: "some string"
```

- Maybe you want to run a task only on certain linux distributions/os families. Then you can use something like the following:
```yaml
when: ansible_os_family == 'Debian'
```

[Docs](https://docs.ansible.com/projects/ansible/latest/playbook_guide/playbooks_tests.html)
[Conditionals](https://docs.ansible.com/projects/ansible/latest/playbook_guide/playbooks_conditionals.html)

### Fact gathering / Ansible Facts
- When a playbook is run, ansible firstly gathers some information about the hosts, its running on
- Those facts than can be accessed as variables with `ansible_` prefix
- This enables the possibility to only run some tasks on certain operating systems

[Docs](https://docs.ansible.com/projects/ansible/latest/collections/ansible/builtin/gather_facts_module.html)
[Guide to Ansible Facts and Fact Gathering](https://spacelift.io/blog/ansible-facts)
[Best way to run a playbook with different OS’s? : r/ansible](https://www.reddit.com/r/ansible/comments/17fcbde/best_way_to_run_a_playbook_with_different_oss/)

### Debug module
- For debug and developement purposes the debug module can be handy
- It prints a speicifed message as console output
- Its also useful to combine this with conditions via the `when:` directive

```yaml
    - name: Print os family
      debug:
        msg: "{{ ansible_os_family }}"
```

[Docs](https://docs.ansible.com/projects/ansible/latest/collections/ansible/builtin/debug_module.html)

### Variables
- As used for tests & filters

[Docs](https://docs.ansible.com/projects/ansible/latest/playbook_guide/playbooks_variables.html)

### Handy modules
- [ansible.builtin.user](https://docs.ansible.com/projects/ansible/latest/collections/ansible/builtin/user_module.html)
- [ansible.builtin.package](https://docs.ansible.com/projects/ansible/latest/collections/ansible/builtin/package_module.html)
- [ansible.posix.authorized_key](https://docs.ansible.com/projects/ansible/latest/collections/ansible/posix/authorized_key_module.html)
- [ansible.builtin.lineinfile](https://docs.ansible.com/projects/ansible/latest/collections/ansible/builtin/lineinfile_module.html)
- [ansible.builtin.template](https://docs.ansible.com/projects/ansible/latest/collections/ansible/builtin/template_module.html)

- [List of all builtin modules](https://docs.ansible.com/projects/ansible/latest/collections/ansible/builtin/index.html)

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

## Ansible Galaxy
Show installed collections:
```bash
ansible-galaxy collection list
```
