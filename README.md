# Infrastructure Automation with Ansible
## Core Concepts
### Infrastructure as Code (IaC)

**Infrastructure as Code (IaC)** is the practice of defining, managing, and provisioning infrastructure — such as servers, networks, and services — using machine-readable configuration files rather than manual processes or ad-hoc scripts.  
The idea is to treat infrastructure like application code: version-controlled, reviewable, testable, and reproducible.

Instead of “clicking through” graphical interfaces or running commands manually, the infrastructure’s desired state is expressed in code and applied automatically.  
This ensures that environments can be created, destroyed, and recreated consistently across development, staging, and production.

---

### IaC vs Configuration Management

Although the terms often overlap, it’s useful to distinguish between:

- **Infrastructure as Code (IaC):** focuses on *provisioning* and *orchestrating* physical or virtual resources — servers, networks, load balancers, storage volumes, etc.  
  *Examples:* Terraform, CloudFormation, Ansible (in some cases).

- **Configuration as Code (CaC):** focuses on *configuring* the software and services that run on top of that infrastructure — installing packages, managing files, starting services, enforcing policies.  
  *Examples:* Ansible, Puppet, Chef.

Both models complement each other: IaC builds the environment, while CaC ensures it runs as intended.  
In practice, teams often use “IaC” to refer to the combination of both provisioning and configuration management — and this repository follows that convention.

---

### Key Principles of IaC

- **Idempotence**  
  Running the same automation repeatedly should produce the same result.  
  Tasks must be safe to reapply without side effects.

- **Declarative over Imperative**  
  Focus on *what* the final state should be (“nginx is installed and running”) rather than *how* to achieve it step by step.

- **Composability and Reuse**  
  Build small, focused modules or roles that can be combined to define more complex systems.

- **Observability and Safety**  
  Include validation, logging, and safe defaults so that automation remains transparent and predictable.

By applying these principles, Infrastructure as Code turns manual setup into a controlled, repeatable process — making infrastructure changes traceable, reversible, and auditable.

---

### Automation Lifecycle

Modern infrastructure management benefits from a clear separation of lifecycle stages.  
By distinguishing **Day 0**, **Day 1**, and **Day 2** operations, we can design automation that is safer, more repeatable, and easier to extend over time.  
Each phase builds on the previous one: Day 0 makes a system reachable, Day 1 makes it manageable, and Day 2 ensures it stays consistent and secure.

![](docs/images/automation-lifecycle.png)

#### Day 0 – Provisioning
Day 0 focuses on preparing a machine or environment so it becomes accessible for automation.  
At this stage, configuration management tools like Ansible are not yet involved — the goal is purely to get a working operating system with remote access.

Typical tasks include:
- Installing or flashing the base operating system.
- Configuring hostname, networking, and remote access (e.g., SSH).
- Adding control-plane public keys or setting temporary credentials.

In cloud or virtualized environments, this step is often handled by prebaked OS images or infrastructure provisioning tools like **Terraform**.  
For physical servers or smaller homelab systems, these steps are usually performed manually or via imaging scripts.

#### Day 1 – Bootstrap
Once the system is reachable, Day 1 automation transforms it into a **managed host**.  
The focus here is on establishing a stable and uniform management baseline so configuration tools can safely take over.

Typical tasks include:
- Creating a dedicated automation or admin user and deploying SSH keys or API credentials.
- Installing Python and essential runtime dependencies.
- Granting controlled privilege escalation (sudo) where required.
- Applying minimal system settings such as timezone, locale, and base packages.

This phase typically runs once per host — it’s the “import” step that brings new machines into the managed fleet.  
Bootstrap playbooks should remain small, focused, and easy to re-run safely.

#### Day 2 – Operations and Maintenance
Day 2 automation is where most of the ongoing infrastructure work happens.  
At this stage, hosts are fully managed and configuration management enforces desired state continuously.  
The goal is to configure, maintain, and evolve systems reliably over time.

Typical tasks include:
- Applying system configuration and security hardening.
- Managing networking, services, and application settings.
- Installing and updating software (containers, orchestration agents, monitoring tools).
- Running regular updates, backups, and integrity checks.
- Detecting and remediating configuration drift.

Day 2 operations can be further divided into **recurring automation** (e.g., scheduled updates or backups) and **ad-hoc automation** (e.g., deploying new services or changing configurations).  
Most Infrastructure-as-Code effort lies here, and it’s best practice to keep logic modular, declarative, and idempotent.

---

## Ansible Concepts

Ansible is a lightweight, agentless automation tool that describes infrastructure and system configuration as code.  
Core components of an Ansible IaC project include:

- **Playbooks** — YAML files that describe the desired state of systems. Each playbook defines *what* to apply and *where*.  
- **Roles** — reusable collections of tasks, templates, files, and variables. Roles make automation modular and easy to maintain.  
- **Inventory** — defines the hosts and groups of hosts managed by Ansible, along with their connection details and variables.

For detailed explanations and advanced features, refer to the official [Ansible documentation](https://docs.ansible.com/ansible/latest/getting_started/index.html).

---
To create a new role, use the Ansible Galaxy command to scaffold the structure:

```bash
ansible-galaxy init roles/<role_name>
```
You can think of Ansible Galaxy as a Package Manager for Ansible Roles. Once created you can also share and download roles from the Ansible Galaxy community repository.

You can then include the role in a playbook:
```yaml
- hosts: managed
  become: true
  roles:
    - role: myrole
```

Those are the most important commands that you will need to get started:

Run a playbook against your inventory:
```bash
ansible-playbook -i inventories/prod/hosts.ini playbooks/site.yml
```

Check connectivity to all hosts:
```bash
ansible all -m ping -i inventories/prod/hosts.ini
```

Run a playbook limited to a single host:
```bash
ansible-playbook playbooks/site.yml --limit srvprod01
```

Run only specific tags:
```bash
ansible-playbook playbooks/site.yml --tags hardening,network
```
