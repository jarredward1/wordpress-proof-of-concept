# WordPress Proof of Concept Deployment (LAMP Stack / Docker / NginX)

> A proof of concept deployment of a WordPress website for a fictional law firm client (Marconi Law Firm), built on a custom virtual network using a LAMP stack, Docker, and an NginX reverse proxy. Includes security hardening and audit documentation.

---

## 📋 Table of Contents

- [Overview](#-overview)
- [Objectives](#-objectives)
- [Network Architecture](#-network-architecture)
- [Technology Stack](#-technology-stack)
- [Deployment Phases](#-deployment-phases)
  - [Phase 1 - Ghost on Docker with NginX Reverse Proxy](#phase-1-ghost-on-docker-with-nginx-reverse-proxy)
  - [Phase 2 - WordPress on Ubuntu LAMP Stack](#phase-2-wordpress-on-ubuntu-lamp-stack)
  - [Phase 3 - WordPress Security Hardening](#phase-3-wordpress-security-hardening)
- [Security Controls Implemented](#-security-controls-implemented)
- [Conclusion](#-conclusion)
- [Documentation](#-documentation)
- [Author](#-author)

---

## 📌 Overview

This project is a proof of concept deployment for Marconi Law Firm, LLC, a fictional client based in Orlando, Florida. The goal was to design, deploy, and secure a WordPress website on a custom virtual network environment. The deployment uses a multi-VM architecture with a Docker-hosted Ghost container, an NginX reverse proxy, and a Ubuntu LAMP stack running WordPress.

The document also serves as audit documentation, providing a comprehensive record of the infrastructure, security controls, and configurations implemented throughout the deployment.

> **Note:** All credentials in this project (usernames, passwords, database configurations) are lab and demo credentials used in a local virtual network environment. They are not used in any production system.

---

## 🎯 Objectives

- Design and deploy a custom virtual network for the client environment
- Deploy a Node.js Ghost application on Docker with NginX reverse proxy
- Install and configure a WordPress site on a Ubuntu LAMP stack
- Harden the WordPress installation with security controls
- Produce audit documentation for the client

---

## 🔷 Network Architecture

```text
                    External Browser
                           │
                    HTTP(S) Request
                           │
                           ▼
                Custom Network / Router
                      Firewall VM
                      10.10.229.1
                           │
                           ▼
              Rocky 8 - NginX Reverse Proxy
                     10.10.229.10
                           │
              ┌────────────┴────────────┐
              │                         │
              ▼                         ▼
   Rocky 8 - Docker              Ubuntu - WordPress
   Ghost Container               LAMP Stack
   10.10.229.11                  10.10.229.12
```

### Network Configuration

| Setting | Value |
|---------|-------|
| Network Name | ITE229 |
| Subnet IP | 10.10.229.0 |
| Subnet Mask | 255.255.255.0 |
| Gateway | 10.10.229.1 |

### VM Inventory

| VM | OS | Role | IP Address |
|----|-----|------|------------|
| Firewall VM | - | Router / Firewall | 10.10.229.1 |
| Rocky8-Docker | Rocky Linux 8 | Ghost Container Host | 10.10.229.11 |
| Rocky8-NginX | Rocky Linux 8 | Reverse Proxy | 10.10.229.10 |
| Ubuntu | Ubuntu | WordPress LAMP Stack | 10.10.229.12 |

---

## 🔩 Technology Stack

| Technology | Purpose |
|------------|---------|
| **Ubuntu** | WordPress LAMP Stack host |
| **Rocky Linux 8** | Docker host and NginX reverse proxy |
| **Apache2** | Web server for WordPress |
| **MySQL** | WordPress database backend |
| **PHP** | Server-side scripting for WordPress |
| **WordPress** | CMS for the client website |
| **Docker** | Ghost container deployment |
| **NginX** | Reverse proxy routing traffic to Ghost |
| **Ghost** | Node.js blogging application |
| **Shield Security** | WordPress application firewall plugin |

---

## 🚀 Deployment Phases

### Phase 1) Ghost on Docker with NginX Reverse Proxy

Deployed a Ghost Node.js blogging application inside a Docker container on Rocky Linux 8. Configured an NginX reverse proxy on a separate Rocky Linux 8 VM to route traffic from the network to the Ghost container.

Key steps:
- Installed Docker CE and initialized the Docker service
- Deployed the Ghost Docker container on port 3001
- Installed and configured NginX as a reverse proxy
- Configured the NginX reverse proxy to forward `/blog` traffic to the Ghost container
- Disabled SELinux on both Rocky VMs for lab compatibility
- Verified Ghost was accessible via `10.10.229.10/blog`

---

### Phase 2) WordPress on Ubuntu LAMP Stack

Installed and configured a full LAMP stack on Ubuntu and deployed WordPress from the official GitHub repository.

Key steps:
- Installed Apache2, MySQL, and PHP with all required libraries
- Configured MySQL with a dedicated WordPress database (`WordPressDB`) and user (`WordPressUser`)
- Cloned WordPress from GitHub to `/var/www/html/`
- Configured Apache2 directives and enabled URL rewrites
- Secured the `.git` directory with an `.htaccess` file
- Completed WordPress setup via the web installer
- Verified the site was accessible and functional at `10.10.229.12`

---

### Phase 3) WordPress Security Hardening

Applied three layers of security hardening to the WordPress installation following the principle of defense-in-depth.

#### File Permissions
- Identified that the default WordPress installation allows broad access to all directories and files
- Restricted directory permissions to `770` and file permissions to `660` using `find` and `chmod`
- Verified that unauthorized users could not access `wp-admin`, `wp-content`, or `wp-includes`

#### Securing wp-config.php
- Identified that `wp-config.php` was stored in the web-accessible `/var/www/html/` directory
- Moved the file to the parent directory `/var/www/` to remove it from public web access
- Verified the move by listing the contents of the parent directory

#### Application Firewall (Shield Security)
- Identified that the base WordPress installation had no firewall
- Installed, activated, and configured the Shield Security plugin
- Verified the plugin was active and functioning in the WordPress dashboard

---

## 🔒 Security Controls Implemented

| Control | Method |
|---------|--------|
| File permission hardening | `chmod 770` (directories) and `chmod 660` (files) |
| Configuration file protection | Moved `wp-config.php` out of web root |
| Application firewall | Shield Security WordPress plugin |
| Git directory protection | `.htaccess` deny rules on `.git/` |
| Reverse proxy isolation | NginX routing traffic to containerized Ghost app |

---

## ✅ Conclusion

This proof of concept demonstrates the full deployment and security hardening of a WordPress website on a custom virtual network. The deployment applied defense-in-depth by layering file permission controls, configuration file protection, and an application firewall. The principles applied here translate directly to securing other systems and web applications in production environments.

---

## 📄 Documentation

[View full proof of concept report (PDF)](./Marconi_Law_Firm_Proof_of_Concept_Technical_Solution.pdf)

---

## 👤 Author

**Jarred Ward**

[![LinkedIn](https://img.shields.io/badge/LinkedIn-blue?logo=linkedin&style=for-the-badge)](https://www.linkedin.com/in/jarredward1/)
