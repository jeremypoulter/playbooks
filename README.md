# playbooks

A collection of playbooks to manage my home/office.

```bash
ansible-galaxy role install -r requirements.yml
ansible-galaxy collection install -r requirements.yml
```

## Docker host

`playbooks/docker.yml` configures the `docker` inventory host with the common
server setup, Docker Engine, and the QEMU guest agent for Proxmox integration.
It then starts Portainer and Nginx Proxy Manager using Docker Compose. Their
data is persisted under `/opt/docker/data`.

```bash
ansible-playbook playbooks/docker.yml --ask-become-pass
```

The become password is needed on first setup unless the SSH user already has
passwordless sudo configured. The common role configures passwordless sudo for
subsequent runs. If privilege escalation still times out, verify that the SSH
user can authenticate with sudo on the guest (`ssh -t docker.lan 'sudo -v'`).

For a fresh Ubuntu guest that needs Python bootstrapped, set
`bootstrap_ubuntu: true` for the host (for example in
`inventories/home/host_vars/docker`) before running the playbook. The host
must have ports 80, 81, 443, and 9443 available. To activate guest-agent
integration, enable QEMU Guest Agent in the VM's Proxmox options and restart
the VM; if the virtio channel is missing, the playbook reports it and continues
with the Docker services. Portainer is available at
`https://docker.lan:9443`; the Nginx Proxy Manager admin UI is at
`http://docker.lan:81`.
