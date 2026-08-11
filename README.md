# Ansible Infrastructure Automation
# ANSIBLE-INFRA-AUTOMATION

Enterprise Ansible Automation Framework for:

- Control-M Agent
- Splunk Universal Forwarder
- OpenTelemetry Collector
- IBM Connect:Direct

Supports:

- DEV
- STG
- E2E
- PROD

Deployment Model:

GitOps

Feature Branch
    |
    v
Pull Request
    |
    v
Merge to Environment Branch
    |
    v
GitHub Actions
    |
    v
Ansible Deployment

--------------------------------------------------
REPOSITORY STRUCTURE
--------------------------------------------------

ansible-infra-automation/

├── .github/
│   └── workflows/
│       ├── deploy.yml
│       └── pr-validation.yml
│
├── inventories/
│   ├── dev/
│   ├── stg/
│   └── prod/
│
├── playbooks/
│   └── site.yml
│
├── roles/
│   ├── controlm/
│   ├── splunkuf/
│   ├── otel/
│   └── connectdirect/
│
├── ansible.cfg
├── requirements.yml
└── README.md

--------------------------------------------------
ENVIRONMENT STRUCTURE
--------------------------------------------------

inventories/dev/

hosts.yml

group_vars/all.yml

Same structure for:

stg
e2e
prod

--------------------------------------------------
GROUP VARS
--------------------------------------------------

group_vars contains environment-level configuration.

Example:

inventories/dev/group_vars/all.yml

environment: dev

gcp_project_id: anz-dev-project

controlm_server: controlm-dev.company.com

splunk_deployment_server: splunk-dev.company.com

otel_endpoint: otel-dev.company.com

connectdirect_server: cd-dev.company.com

These values are shared by all DEV servers.

Do NOT put:

- Hostnames
- IPs
- Passwords
- SSH Keys

inside group_vars.

--------------------------------------------------
HOSTS INVENTORY
--------------------------------------------------

hosts.yml is the only file users typically modify.

Example:

all:
  hosts:

    devapp01:

      ansible_host: 10.10.1.20

      software:
        - controlm
        - splunk
        - otel

--------------------------------------------------
ONBOARDING NEW SERVER
--------------------------------------------------

Step 1

Open:

inventories/dev/hosts.yml

Step 2

Add server:

devapp02:

  ansible_host: 10.10.1.21

  software:
    - controlm
    - splunk

Step 3

Create Feature Branch

feature/onboard-devapp02

Step 4

Commit Changes

Step 5

Create Pull Request

Step 6

Merge to DEV branch

Step 7

Pipeline Automatically Executes

No manual deployment required.

--------------------------------------------------
SOFTWARE FLAGS
--------------------------------------------------

Available software:

controlm
splunk
otel
connectdirect

Example:

software:
  - controlm
  - splunk

Only these components will be installed.

--------------------------------------------------
SECRETS MANAGEMENT
--------------------------------------------------

Secrets are stored in:

GCP Secret Manager

Examples:

dev-controlm-password

dev-splunk-token

prod-controlm-password

Passwords must NEVER be stored in:

- GitHub Repository
- Inventory Files
- Group Vars

--------------------------------------------------
DEPLOYMENT FLOW
--------------------------------------------------

Feature Branch

↓

Pull Request

↓

PR Validation Pipeline

↓

Merge

↓

GitHub Deploy Pipeline

↓

Ansible Playbook

↓

Role Execution

↓

Validation

↓

Success

--------------------------------------------------
PR VALIDATION PIPELINE
--------------------------------------------------

Executed on:

Pull Request

Checks:

- YAML Validation
- Ansible Syntax Check
- Ansible Lint
- Role Validation

No software deployment occurs.

--------------------------------------------------
DEPLOYMENT PIPELINE
--------------------------------------------------

Executed on:

Merge to:

dev
stg
main

Environment mapping:

dev -> DEV

stg -> STG

main -> PROD

--------------------------------------------------
ADDING NEW SOFTWARE
--------------------------------------------------

Example:

CrowdStrike

Step 1

Create:

roles/crowdstrike

Step 2

Update:

site.yml

Step 3

Use:

software:
  - crowdstrike

No workflow changes required.

--------------------------------------------------
ROLES
--------------------------------------------------

Each role must contain:

defaults/
tasks/
handlers/
templates/
files/

Example:

roles/controlm

tasks/

install.yml

configure.yml

register.yml

validate.yml

main.yml

--------------------------------------------------
TROUBLESHOOTING
--------------------------------------------------

Check:

GitHub Actions Logs

Check:

Ansible Play Recap

Check:

Application Logs

Control-M:

/opt/controlm/logs

Splunk:

/opt/splunkforwarder/var/log

OTEL:

/var/log/otel

--------------------------------------------------
BEST PRACTICES
--------------------------------------------------

Do:

✅ Use Feature Branches

✅ Raise PR

✅ Store Secrets in GCP Secret Manager

✅ Make roles idempotent

✅ Use software list for onboarding

✅ Keep environment configuration in group_vars

Don't:

 Store passwords in Git

 Modify workflows for onboarding

❌ Hardcode server names

❌ Duplicate code across environments
