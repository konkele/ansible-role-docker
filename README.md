# Ansible Role: Docker

The **`docker` role** provides a fully automated setup for Docker hosts, including installation, network management, secrets handling, user management, and Docker Compose stack deployment. It is designed to be **inventory-driven** and **idempotent**, making it safe for repeated execution in homelab or production environments.

This role follows a **layered and deterministic design**, enabling consistent deployment of container tooling such as Traefik and Portainer.

---

## Features

* **Docker Installation & Configuration**

  * Installs Docker, Docker CLI, containerd, and Compose plugin
  * Adds official Docker APT repository and GPG keys
  * Ensures prerequisite packages are installed

* **Docker Networks**

  * Automatically creates required managed networks
  * Ensures external networks exist without modification
  * Removes unused managed networks safely

* **Docker Secrets Management**

  * Validates required secrets are defined in inventory or host_vars
  * Writes secrets to `{{ docker_tooling_dir }}/secrets` with secure permissions
  * Removes disabled secrets automatically

* **Docker Services & Stacks**

  * Deploys enabled services via Docker Compose (v2)
  * Removes disabled services from the stack
  * Supports templated service definitions

* **User Management**

  * Adds users tagged with `docker` to the Docker group
  * Removes users no longer tagged for Docker

* **Tooling Directory Management**

  * Creates or cleans up the tooling directory based on enabled services
  * Configurable ownership and permissions

---

## Role Variables

Defined in `roles/docker/defaults/main.yml`. Key variables include:

### Docker Tooling Directory

| Variable               | Default               | Description                                                 |
| ---------------------- | --------------------- | ----------------------------------------------------------- |
| `docker_tooling_dir`   | `/opt/docker/tooling` | Base directory for Docker tooling configuration and secrets |
| `docker_tooling_user`  | `root`                | Owner of tooling directory                                  |
| `docker_tooling_group` | `docker`              | Group of tooling directory                                  |
| `docker_tooling_mode`  | `'0770'`              | Permissions of tooling directory                            |

### Docker Networks

| Variable          | Default            | Description                               |
| ----------------- | ------------------ | ----------------------------------------- |
| `docker_networks` | `proxy` (external) | Managed or external networks for services |

### Docker Services

| Service           | Description                    | Enabled by default |
| ----------------- | ------------------------------ | ------------------ |
| `traefik`         | Reverse proxy and ACME support | false              |
| `portainer`       | Container management UI        | false              |
| `portainer_agent` | Agent for Portainer clusters   | false              |

### Docker Secrets

Secrets must be defined in inventory or host_vars:

```yaml
docker_secrets:
  acme_dns_cloudflare_api_token:
    enabled: false
    var: acme_dns_cloudflare_api_token
```

Secrets are securely written to `{{ docker_tooling_dir }}/secrets` with `0600` permissions.

---

## Example Playbook

```yaml
- name: Docker tooling setup
  hosts: docker_hosts
  become: true
  roles:
    - role: ansible-role-docker
      vars:
        docker_services:
          traefik:
            enabled: true
          portainer:
            enabled: true
```

This playbook will:

* Install Docker and prerequisites
* Configure required networks
* Deploy enabled services
* Add tagged users to the Docker group
* Write secrets and clean up disabled services

---

## Directory Structure

```
roles/docker/
├── defaults/
│   └── main.yml
├── tasks/
│   ├── cleanup.yml
│   ├── install.yml
│   ├── main.yml
│   ├── network.yml
│   ├── prepare.yml
│   ├── secrets.yml
│   ├── stack.yml
│   └── users.yml
├── templates/
│   └── tooling_stack.yaml.j2
```

---

## Design Principles

* **Inventory expresses intent, not final state**
* **Deterministic and idempotent**: repeated runs produce consistent results
* **Enabled services drive directory and secret creation**
* **Managed networks are safely reconciled**
* **User tagging provides explicit, automated membership in the Docker group**
* **Secrets stored securely for sensitive data handling**
