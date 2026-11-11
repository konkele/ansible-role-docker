# Ansible Role: Docker

The **docker** role installs and configures Docker Engine on Debian and Ubuntu systems.

It configures Docker's official package repository, installs the Docker Engine and related tooling, and ensures the Docker service is enabled and running.

---

## Features

- Configure Docker's official package repository
- Install Docker Engine
- Install Docker CLI
- Install containerd
- Install Docker Buildx
- Install Docker Compose plugin
- Install Python Docker bindings
- Enable and start Docker service
- Support Debian and Ubuntu

---

## Requirements

- Debian or Ubuntu
- Ansible 2.16 or later
- Internet access to Docker package repositories

---

## Variables

### Prerequisites

```yaml
docker_prerequisites:
  - ca-certificates
  - python3-debian
  - python3-pip
  - python3-jsondiff
```

### Docker Packages

```yaml
docker_packages:
  - docker-ce
  - docker-ce-cli
  - containerd.io
  - docker-buildx-plugin
  - docker-compose-plugin
  - python3-docker
```

### Service Configuration

```yaml
docker_service_enabled: true

docker_service_state: started
```

---

## Tags

| Tag | Description |
|-------|-------------|
| `docker_repository` | Configure Docker package repositories |
| `docker_install` | Install Docker packages |
| `docker_service` | Manage Docker service |

---

## Example Playbook

```yaml
---
- name: Configure Docker hosts
  hosts: docker
  become: true

  roles:
    - role: konkele.docker
```

---