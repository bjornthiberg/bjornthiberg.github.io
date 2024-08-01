---
layout: post
title:  "lab2: self-hosted ASP.NET Core backend"
date:   2024-07-31 12:00:00 +0200
category: homelab
---

I've successfully set up the new backend on my home server, complete with SSL certificates, all for free (electricity costs do not count). The next step is to modify the GitHub Actions workflow for this new setup, to correctly automate deployment.

## modifying the deployment workflow
Current Deployment Workflow:
- Checkout the repo.
- Build and test the .NET application locally.
- Build the Docker image and push it to GitHub Container Registry.
- SSH into the backend server.
- Pull and run the Docker container.

The challenge: Moving this setup to the home server would mean exposing SSH/port 22 via the public IP, which I'd prefer to avoid for security reasons. I considered two alternatives:

1. Using the existing WireGuard server on my OPNsense router and SSHing locally.
2. Setting up a self-hosted runner on my Proxmoserver.

While 2. would be more fun, 1. requires only a simple change, so I landed on that.

The process was straightforward:

- Created a new WireGuard key-pair.
- Added it as an environmental secret to the repository.
- Added it as a Peer to the WireGuard interface in OPNsense.

## finishing touches

Now, the workflow deploys the backend to the home server, which is in turn publically accessible. It's a bit slower, but the application isn't performance-sensitive, so it's not a major issue.

I also had some issues with SSL/TLS when accessing the API using the domain name, but over LAN. That ended up being solved by enabling NAT reflection in OPNsense.

After that, I set up some downtime alerts using Uptime Kuma + a Telegram bot.

## network map
This is the current network. There's not much use in doing a more detailed sketch before I can add VLANs. That requires at least a new AP that can handle multiple SSIDs, and preferably a VLAN-aware switch.

- **proxmox: 192.168.1.1–92.168.1.49**
```
192.168.1.1 OPNsense router
192.168.1.2:8006 Proxmox server
192.168.1.3:9090 Prometheus
192.168.1.4:3000 Grafana
192.168.1.5:81 Nginx proxy manager
192.168.1.6:3000 AdGuard Home
192.168.1.7:5216 MySpeed
192.168.1.8:5005 Flame Dashboard
192.168.1.9:5000 Project Frej backend
```
- **other devices: 192.168.1.50-192.168.1.59**
```
192.168.1.50 Raspberry Pi
192.168.1.51 Unifi switch
192.168.1.52 Wi-Fi router in AP mode
```
- **IoT devices: 192.168.1.60-192.168.79**
```
192.168.1.61: Smart bulb hub
192.168.1.62: Air Filter
192.168.1.63: Chromecast
192.168.1.64: Wi-Fi connected Body Scale
```
- **DHCP: 192.168.1.100-192.168.1.199**
- **Reserved for later use: 192.168.1.200-192.168.1.254**

## Next Steps
- Get Project Frej in a finished state (documentation, testing, refactoring)
- Experiment with Infrastructure as Code (IaC) tools like Ansible or Terraform.
- Upgrade to a new AP and managed switch to use VLANs.
