# System Architecture

## Overview

This project documents a Linux-based web proxy solution built with Debian 12, Squid, and ufdbGuard.

The solution centralizes web traffic handling, applies URL filtering policies, and supports integration with a Windows Active Directory environment.

## Components

### Debian 12

Provides the Linux operating system for the proxy server, including system services, networking, package management, and logging.

### Squid

Acts as the web proxy service. It receives client requests, handles HTTP and HTTPS proxy connections, and applies access-control rules.

### ufdbGuard

Integrates with Squid to enforce URL filtering policies. It evaluates requested destinations against configured rules and category-based access policies.

### Active Directory

Provides centralized identity management for domain users and groups. Integration allows access policies to be associated with user identities or group membership, where configured.

### Kerberos and Winbind

Support domain authentication and integration with Active Directory. Their exact responsibilities depend on the authentication method and deployed configuration.

## Request Flow

1. A client sends a web request through the configured proxy.
2. Squid evaluates the request against its access-control rules.
3. Authentication identifies the user when required by the configured method.
4. ufdbGuard evaluates the destination against the applicable filtering policies.
5. Squid allows or denies the request according to the configured rules.
6. Request outcomes are recorded in the relevant logs.

## Security Principles

- Apply least-privilege access controls.
- Keep credentials, keytabs, authentication tickets, and secrets out of version control.
- Avoid publishing internal IP addresses, domain details, usernames, or production logs without authorization.
- Validate configuration changes before applying them to production.
- Document operational procedures and troubleshooting steps.

## Scope and Limitations

This document describes the logical architecture. Actual request flow, authentication behavior, and policy evaluation depend on the deployed Squid, ufdbGuard, and Active Directory configuration.

Public documentation should use sanitized examples rather than confidential production configuration.
