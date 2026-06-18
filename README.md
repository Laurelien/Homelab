# Homelab

Deployment project and local infrastructure management (Homelab) on a dedicated Debian server with containerized services. The main goal is to centralize my data and be less dependent on cloud services (iCloud, Google Drive, etc.)

## Architecture

<img width="602" height="611" alt="homelab drawio" src="https://github.com/user-attachments/assets/7d6fab85-a8e8-4c9f-a474-09cac04844aa" />

The infrastructure operates strictly within an isolated LAN zone, minimizing the external attack surface prior to VPN implementation.

## Hardware

| **Component** | **Spec**                 | Role and notes                                 |
| ------------- | ------------------------ | ---------------------------------------------- |
| Machine       | Lenovo M720q tiny        | Low electricity consumption                    |
| CPU           | Intel i5 9500t (6 cores) | Ideal for containerization                     |
| RAM           | 16GB DDR4 (2 x 8GB)      | Dual-channel configuration                     |
| Storage       | 250GB SSD + 8TB HDD      | SSD (OS + Docker) / HDD (Samba + Mass storage) |
| OS            | Debian 13                | Secure and stable Linux OS                     |

## Services

| **Service** | **Where**         | **Role**                                       |
| ----------- | ----------------- | ---------------------------------------------- |
| Pi-hole     | Bare metal (host) | DNS local + ad blocking                        |
| Samba       | Bare metal (host) | File sharing                                   |
| nginx       | Docker            | Reverse proxy (routing management)             |
| Immich      | Docker            | Self hosted photo library (iCloud alternative) |
| Fail2ban    | Bare metal (host) | SSH brute-force protection                     |

## Security

- SSH authentication where password is disabled in favor of SSH public key authentication. Root login also disabled.
- Fail2ban deployment to monitor and ban brute force tries on SSH port 22.
- Docker containers run as non-root user (UID 1000) and on custom bridge networks, and are isolated by purposes. Exception: Immich's official images currently run as root upstream, a known limitation tracked by the project [FAQ](https://immich.app/docs/FAQ#how-can-i-run-immich-as-a-non-root-user).
- Samba is setup with SMBv3 protocol, no guest access

## Network

- Docker intern networks segmented
- Pi-hole as a DNS resolver (Cloudflare upstream) and ad-blocker
- Nginx as reverse proxy to centralize local traffic with subdomains.

## Backup strategy

**To implement in a near future**

- **Tool**: Restic (modern solution, secure)
- **Destination**: Object storage via Backblaze B2 Cloud storage
- **Automation**: Using Cron Jobs to save data daily

*Note: for Immich, I need to dump de database first before calling Restic*

## Planned improvements

- Implement Zero-Trust Remote Access via Tailscale (WireGuard Mesh VPN) with MFA.
- Monitor services with Grafana and Prometheus.
