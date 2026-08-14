---
title: "DuckDNS domain, Caddy HTTPS reverse proxy & CORS"
date: 2026-08-06
weight: 5
chapter: false
pre: " <b> 5.5. </b> "
---

#### Overview

In this module, you will configure a public domain name and automatic **HTTPS** encryption for the backend API, enabling secure communication with your Vercel-hosted frontend:

- **Resolving Mixed Content errors**: Overcoming browser security blocks caused when HTTPS frontends call HTTP backend endpoints.
- **DuckDNS domain**: Registering a free domain `https://nestro.duckdns.org` pointing directly to the EC2 Elastic IP.
- **Caddy Web Server**: Reverse proxy server providing fully automated SSL/TLS certificate provisioning and renewal, proxying HTTPS port 443 requests to internal Docker port 4000.
- **NestJS CORS integration**: Granting cross-origin permission to your Vercel application domain.

#### Module steps

1. [Configuring DuckDNS domain, Caddy HTTPS reverse proxy & CORS](5.3.1-domain-ssl/)
