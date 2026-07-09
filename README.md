# Ansible Practice

Personal sandbox for learning and practicing Ansible — ad-hoc commands, playbooks, roles, vault-encrypted secrets, and a Dockerized lab environment to run it all against.

## Structure

```
.
├── node_setup/    # Docker-based lab nodes (managed hosts) to practice Ansible against
└── practice/      # Ansible playbooks, roles, inventory, and vault examples
```

### `node_setup/`

Spins up SSH-reachable Docker containers as managed nodes, so playbooks can be run without real servers or VMs. See [node_setup/README.md](node_setup/README.md) for setup steps.

### `practice/`

Core Ansible examples:

- `inventory.ini` — inventory of the lab nodes
- `demo.-playbook.yml` — minimal "hello world" playbook
- `docker-role-playbook.yml` + `roles/docker/` — example of an Ansible role
- `nginx/` — installs and starts Nginx via a playbook
- `vault/` — Ansible Vault example for encrypting/decrypting secrets

See [practice/README.md](practice/README.md) for the full set of commands (ad-hoc commands, facts gathering, playbook runs, vault usage, roles).

## Getting Started

1. Set up the lab nodes: follow [node_setup/README.md](node_setup/README.md).
2. Point `practice/inventory.ini` at your nodes.
3. Run playbooks and commands as described in [practice/README.md](practice/README.md).
