# Ansible GitLab Lab

Automated GitLab CE installation and GitLab Runner registration using Ansible.

## Infrastructure

| Host | IP | Role |
|------|----|------|
| gitlab | 10.30.10.100 | GitLab CE server |
| gitrunner1 | 10.30.10.101 | GitLab Runner |
| gitrunner2 | 10.30.10.102 | GitLab Runner |

## Prerequisites

- Ansible installed on your control machine
- `sshpass` installed (`sudo apt install sshpass`)
- VMs reachable at the IPs above with a known password for the `hyfer` user
- SSH keypair at `~/creds/RedKeys.pem` and `~/creds/RedKeys.pub`
- `gh` CLI (optional, for repo management)

## Quickstart

### 1. Bootstrap SSH keys

```bash
ansible-playbook playbooks/bootstrap_ssh.yml --ask-pass
```

### 2. Bootstrap sudo

```bash
ansible-playbook playbooks/bootstrap_sudo.yml --ask-pass
```

### 3. Configure /etc/hosts on all VMs

```bash
ansible-playbook playbooks/configure_hosts.yml
```

### 4. Generate local CA and GitLab TLS certificate

```bash
ansible-playbook playbooks/generate_ca.yml
```

Import `files/ssl/lab-ca.crt` into your OS/browser trust store so the cert is trusted.

### 5. Install GitLab CE

```bash
ansible-playbook playbooks/install_gitlab.yml
```

### 6. Install and register GitLab Runners

Create a runner token in GitLab UI: **Admin Area → CI/CD → Runners → New instance runner**

```bash
ansible-playbook playbooks/install_runners.yml
```

You will be prompted to enter a `glrt-...` token for each runner host.

## Notes

- Runners use the **shell** executor (no Docker required)
- TLS certificates are self-signed using a local CA; private keys are gitignored
- Runner tags and configuration are set in the GitLab UI when creating the token
