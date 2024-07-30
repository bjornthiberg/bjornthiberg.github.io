---
layout: post
title:  "H1: Preparing To Move Home(lab)"
date:   2024-07-30 11:00:00 +0200
category: homelab
---

I’ve revived my Proxmox home server to host the ASP.NET Core backend of my latest project, moving away from a paid VPS. Before that, I’ll set up necessary backend tools (e.g., nginx, monitoring software) and some fun self-hosted applications.

## step 1: update to Proxmox 8 and finding a dead HDD
Bad news, but expected. The HDD has been with me for 15 years and she finally gave out. 

Updating Proxmox 7 -> 8 was a breeze.

## step 2: monitoring software
I use the excellent [Proxmox VE Helper-Scripts](https://tteck.github.io/Proxmox/) by [@tteck](https://github.com/tteck) for this, to set up LXCs for:

- [prometheus](https://tteck.github.io/Proxmox/#prometheus-lxc)
- [alpine-grafana](https://tteck.github.io/Proxmox/#grafana-lxc)

Both with default settings for RAM, storage, vCPU cores.

## step 3: setting up prometheus node exporters
I then set up node exporters on:
- The Promethus LXC itself
- The Linode VM running the ASP.NET Core backend. I extended the nginx setup to include the monitoring endpoint.
- My Raspberry Pi with the Sense HAT. Specifically monitoring the systemd service handling transmission.

On the VM and RPi, I created new systemd services:

```ini
[Unit]
Description=Node Exporter
Wants=network-online.target
After=network-online.target

[Service]
User=node_exporter
Group=node_exporter
Type=simple
ExecStart=/usr/local/bin/node_exporter

[Install]
WantedBy=multi-user.target
```

## step 4: some other fun tools
While I'm at it, I set up some other tools running as LXC containers:
- nginx proxy manager (for use later)
- AdGuard Home (DNS filtering for the home network)
- Flame (dashboard for the homelab)
- MySpeed (speed testing)

## step 5: Getting those tools up and running
I proceeded with the following:
- Set up **AdGuard Home**

Configured with blocklists and set as the upstream DNS in the router. I also added a rule in my OPNsense router to redirect all DNS requests to AdGuard to counteract hard-coded DNS servers on some IoT devices.

- Added services to **Flame** homepage.

I have Grafana/Prometheus for looking at pretty data. Instead, essentially just links to the different services running on the home network.

![homelab dashboard]({{ site.baseurl }}/assets/images/homelab-dashboard.jpeg)

- **MySpeed**

This is a simple tool that periocally gathers speed data. The FOSS speed test LibreSpeed was one of the options, instead of just Ookla. Not very accurate though, as the small size of the LXC the test is running in will seriously bottleneck speeds.

## Next steps for the homelab:
- Move ASP.NET Core API to home server.
- Modify the GitHub workflow to deploy to home server.
- Set up downtime alerts for the backend LXC and the Raspberry Pi.
- Set up automtaed backup, at least for the database.
- Draw a nice network scheme/map
- Try out some IaC tools such as Ansible or Terraform
- (requires $) Upgrade to a managed switch to use VLANs
