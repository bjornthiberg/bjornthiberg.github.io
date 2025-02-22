---
layout: post
title:  "lab3: a declarative homelab"
date:   2025-02-22 12:00:00 +0200
category: homelab
---

I am migrating the core of my homelab setup, my home server, from Proxmox to NixOS. Mainly to:
- enable storage and version management of configs.
- learn nix and IaC/CaC.
- enable a future kubernetes setup.

## installing Nix
My first attempt was to use [nixos-anywhere](https://github.com/nix-community/nixos-anywhere) to install NixOS from a Proxmox VM. This strategy did not end up working out, and broke ssh access. After some frustration, I opted to install manually instead.

I created this `configuration.nix` after some tinkering, inspired by example configurations. The server is not directly accessible on my public IP (I port forward to nginx proxy manager), so the open ports are only relevant for the local network (I think). I access my home network using a WireGuard server on my OPNsense router.

```nix
{ config, lib, pkgs, ... }:

{
  imports = [
    ./hardware-configuration.nix
  ];

  # Bootloader
  boot.loader.systemd-boot.enable = true;
  boot.loader.efi.canTouchEfiVariables = true;

  # Networking
  networking.hostName = "homelab";
  networking.networkmanager.enable = true;

  # Set Timezone and Locale
  time.timeZone = "Europe/Stockholm"; 
  i18n.defaultLocale = "en_US.UTF-8";
  console.keyMap = "sv-latin1";

  # Enable OpenSSH for remote access
  services.openssh.enable = true;
  users.users.dude = {
    isNormalUser = true;
    extraGroups = [ "docker" "wheel" ]; # Allows `sudo`
    initialPassword = "changeme"; # Change after login
    openssh.authorizedKeys.keys = [
      "ssh-ed25519 AAAAC3NzaC1lZDI1NTE5AAAAIGLBky0/UL1HffqLhIaiyUI7/kMJoSXLRzB6dOnCs1vo"
    ];
  };

  # Installed packages
  environment.systemPackages = with pkgs; [
    vim git curl wget htop docker-compose
  ];

  virtualisation.docker.enable = true;

  # firewall, auth
  networking.firewall.enable = true;
  networking.firewall.allowedTCPPorts = [ 
    22 #ssh 
    80 
    8080
    443
    53
    3000
    5000
  ];

  networking.firewall.allowedUDPPorts = [
    53
  ];

  # Set system compatibility version
  system.stateVersion = "24.11"; 
}
```

this along with everything else is in my [homelab GitHub repo](https://github.com/bjornthiberg/homelab).

## running containers

The next step is to set up all my goodies, that were previously running as LXC containers on Proxmox. 

I opted to use Docker containers and Docker compose. This is not really the "Nix-y" way of doing it, that might include building the images using Nix and use something like `virtualisation.oci-containers`. However, using Docker Compose and storing the compose file in the git repo seemed like a good initial mix of declarative-ness and simplicity.

The current `compose.yml` can be [found here](https://github.com/bjornthiberg/homelab/blob/47e88e20fae9c3bed2765b854a4ca2b2ca02172a/containers/compose.yml).

Instead of remembering and documenting a bunch of random ports for different services, I decided to use [Docker's Macvlan network driver](https://docs.docker.com/engine/network/drivers/macvlan/) to have separate IPs and MAC addresses for all the containers. This has the added benefit of emulating my previous IP setup, so I e.g. did not have to change the already established IP for my AdGuard Home service.

I have set up the following services so far as containers using docker compose:

- adguard home
- nginx proxy manager
- backend for my [project frej](https://github.com/bjornthiberg/project_frej)
- Unifi Network Manager

## thoughts
I thoroughly enjoy the *idea* of a declarative homelab, and a practical advantage is to have a clear record of everything running when I get back to it after a few months' hiatus (not yet the case, as the service specific configs are essentially imperative).

I sometimes miss the GUI.

## next steps for homelab
- additional fun services (dashboard, prometheus, jellyfin)
- podman, nix oci-container
- nix flakes
- include service configs in git

in the long-term:
- segment network using VLANs tags.
- backups
- automate using terraform/ansible etc
