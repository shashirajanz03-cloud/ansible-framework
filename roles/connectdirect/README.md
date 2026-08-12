# Connect:Direct Ansible Role

This role installs, configures, starts, and validates IBM Connect:Direct on Linux servers using the shared GitOps Ansible repository model.

## Where this role should be placed

Copy this folder into:

```text
ansible-infra-automation/
└── roles/
    └── connectdirect/
```

## How onboarding works

The onboarding team should normally update only one file:

```text
inventories/<env>/hosts.yml
```

Example:

```yaml
all:
  hosts:
    devapp01:
      ansible_host: 10.10.1.20
      software:
        - controlm
        - splunk
        - connectdirect
```

After the feature branch is merged to the environment branch, the GitHub Actions deployment workflow runs `playbooks/site.yml`. The `connectdirect` role runs only when `connectdirect` is present in the host `software` list.

## Required environment variables

These values should be maintained in:

```text
inventories/<env>/group_vars/all.yml
```

Example:

```yaml
environment: dev
gcp_project_id: anz-dev-project
secret_prefix: dev

artifact_repo_url: https://artifact.example.com

connectdirect_package_name: cdunix-installer.tar.gz
connectdirect_installer_source: "{{ artifact_repo_url }}/connectdirect/{{ connectdirect_package_name }}"

connectdirect_local_node: CD_DEV_NODE
connectdirect_listen_port: 1364
connectdirect_install_dir: /opt/cdunix
connectdirect_runtime_user: cduser
connectdirect_runtime_group: cdgroup

connectdirect_partner_nodes:
  - name: PARTNER01
    host: partner01.example.com
    port: 1364
    protocol: tcpip
```

## Required GCP Secret Manager secrets

Store sensitive values in GCP Secret Manager. Do not store passwords, certificates, or keys in Git.

Recommended secret naming pattern:

```text
<env>-connectdirect-keystore-password
<env>-connectdirect-cert-password
<env>-connectdirect-admin-password
```

Example for DEV:

```text
dev-connectdirect-keystore-password
dev-connectdirect-cert-password
dev-connectdirect-admin-password
```

## Role execution order

```text
main.yml
 ├── prereq.yml
 ├── secrets.yml
 ├── install.yml
 ├── configure.yml
 ├── secureplus.yml
 ├── service.yml
 └── validate.yml
```

## Validation checklist

The role validates:

- Runtime user and group exist
- Installation directory exists
- Installer marker file exists
- Configuration files are deployed
- Connect:Direct service is enabled and running
- Listening port is reachable locally
- Optional partner node connectivity can be added later

## Important notes

- This role is intentionally parameterized.
- No environment-specific server name is hardcoded inside the role.
- No password is stored inside the repo.
- Installer download, node name, ports, and partner definitions are controlled by inventory and group variables.
- Real installation commands may need to be adjusted according to the Connect:Direct version and enterprise-approved silent install method.
