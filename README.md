# Infrastructure Automation with Ansible
## Automation Lifecycle
In infrastructure automation, it’s useful to distinguish between Day 0, Day 1, and Day 2 operations.
Each stage represents a different point in the system’s lifecycle — from first boot, to configuration, to ongoing maintenance.
This separation helps keep IaC code focused, idempotent, and easy to reuse.

### Day 0 – Provisioning
The goal of Day 0 operations is simply to make a machine reachable and ready for management.
At this stage, no configuration management tools are running yet.
Everything is about getting basic network and access in place.
	•	Flash or install the base operating system.
	•	Configure hostname, network, and enable SSH.
	•	Add your control node’s public key or password.
	•	Usually done manually, via an imaging tool, or with cloud-init.

Day 0 ends once the automation agent (in this case Ansible) can establish an SSH connection to the host.
In a virtualized enviroments htis role is usually taken over by dedicated provisioning tools like Terraform. In smaller enviroments like home labs this step can't be automated since it requires manual hardware setup and installation

### Day 1 – Bootstrap
Day 1 transforms a fresh system into a properly managed host.
This is the first Thruly automated step, establishing a consistent administrative baseline.
	•	Create the ansible admin user and install SSH keys.
	•	Install Python (required for Ansible modules).
	•	Enable password-less sudo for automation.
	•	Apply minimal setup (timezone, locale, package tools).

**This phase runs only once per host.**

### Day 2 – Operations
Once a host is under management, Day 2 automations enforce and maintain its desired state.
These tasks can be safely re-applied at any time.
	•	Apply system configuration and security hardening.
	•	Configure networking and essential services.
	•	Install and manage software such as Docker or k3s.
	•	Schedule regular updates, backups, and monitoring.

Day 2 automation represents the normal operational lifecycle of your systems.
It’s where most of the automation will be done.
