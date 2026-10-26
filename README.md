
# Debian Squid Proxy with ufdbGuard

A Linux-based web proxy solution built with Debian 12, Squid, and ufdbGuard, providing centralized web access control, URL filtering, and Active Directory integration.

## Overview

This project documents the deployment, operation, and ongoing refinement of a web proxy solution integrated with a Windows Active Directory environment.

The solution is operational in a production network. Current work focuses on configuration refinement, policy validation, troubleshooting, and improving documentation and maintainability.

## Architecture

- **Debian 12:** proxy server operating system.
- **Squid:** HTTP and HTTPS proxy service.
- **ufdbGuard:** URL filtering and access policy enforcement.
- **Active Directory and Winbind:** domain integration and user/group information.
- **Kerberos:** authentication for domain-related services.

## Key Features

- Centralized web access control.
- URL filtering based on defined policies and categories.
- User and group-based access policies.
- Integration with Active Directory.
- Operational logging and troubleshooting.
- Automated maintenance tasks and service management.

## Project Status

**Operational in production — ongoing refinement.**

The proxy is in active use. Development activities focus on improving reliability, validating access policies, refining authentication and user/group integration, and documenting administrative procedures.



## Disclaimer

This is an independent technical documentation project. Public materials do not include confidential company configuration or production credentials.

