# Control-M Ansible Role

## Overview

This role automates the deployment workflow of a Control-M Agent using Ansible. It follows a modular role-based structure consisting of separate task files for installation, configuration, registration, and validation.

The current implementation uses a **dummy deployment** to simulate the Control-M Agent installation because the official Control-M installation package is not available. The role has been designed so that the dummy implementation can be replaced with the actual installer in the future without changing the overall project structure.

---

## Requirements

The following prerequisites must be met before executing this role:

- Ansible installed on the control node
- Python installed on the target machine
- SSH connectivity between the Ansible controller and the target VM
- Linux-based target server (Ubuntu)
- Sudo privileges on the target machine

---

## Role Variables

The following variables are defined in `defaults/main.yml`.

|        Variable         |            Description           |   Default Value  |
|-------------------------|----------------------------------|------------------|
| `controlm_install_dir`  | Control-M installation directory | `/opt/controlm`  |
| `controlm_service_name` | Control-M service name           | `controlm-agent` |
| `controlm_server`       | Control-M Server name            | `dummy-server`   |
| `controlm_agent_name`   | Agent name                       | `agent01`        |
| `controlm_port`         | Communication port               | `7005`           |
| `controlm_version`      | Control-M version                | `9.0`            |

---

## Role Structure

```
roles/
└── controlm/
    ├── defaults/
    ├── handlers/
    ├── tasks/
    │   ├── install.yml
    │   ├── configure.yml
    │   ├── register.yml
    │   ├── validate.yml
    │   └── main.yml
    ├── files/
    ├── templates/
    ├── vars/
    ├── meta/
    └── tests/
```

---

## Dependencies

This role has no external Ansible role dependencies.

---

## Example Playbook

```yaml
---
- name: Configure Control-M Agent
  hosts: gcp
  become: yes

  roles:
    - controlm
```

---

## Validation

After successful execution, the following files are created on the target machine:

```
/opt/controlm/
│
├── installed.txt
├── registered.txt
└── config/
      └── controlm.conf
```

These files are used to simulate the installation, configuration, and registration of the Control-M Agent.

---

## Future Enhancements

When the official Control-M installation package becomes available, the following components can be replaced without changing the role structure:

- Dummy installation tasks
- Dummy configuration
- Dummy registration
- Handler implementation

The existing role structure can be reused for the actual Control-M deployment.

