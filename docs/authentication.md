# Authentication and Active Directory Integration

## Overview

This document describes the authentication components used in the reference Debian proxy deployment.

The environment integrates Squid with Kerberos-based HTTP authentication and uses Samba and Winbind for Active Directory domain membership and identity integration. ufdbGuard applies URL filtering policies based on configured user sources and client IP addresses.

## 1. Authentication Components

| Component | Role |
|---|---|
| Squid | Handles proxy requests, authentication, and access-control rules. |
| Kerberos | Supports HTTP Negotiate authentication using a service principal and keytab. |
| Samba | Configures the Linux host as an Active Directory domain member. |
| Winbind | Provides domain identity integration and user or group lookup capabilities. |
| ufdbGuard | Applies URL filtering policies using configured sources and categories. |

## 2. Squid Authentication

The Squid configuration uses the `negotiate_kerberos_auth` helper to support Kerberos-based HTTP authentication through the Negotiate mechanism.

```conf
auth_param negotiate program /usr/lib/squid/negotiate_kerberos_auth -k /etc/krb5.keytab -s HTTP/<proxy-fqdn>@<KERBEROS-REALM>
auth_param negotiate children 10
auth_param negotiate keep_alive on

acl authenticated proxy_auth REQUIRED
```

### Authentication Parameters

- **`auth_param negotiate program`** — Specifies the authentication helper executable and its arguments. The `-k` option specifies the Kerberos keytab file, while `-s` specifies the HTTP service principal used by the proxy.

- **`auth_param negotiate children 10`** — Configures the number of authentication helper processes Squid can run. Multiple helpers allow authentication requests to be processed concurrently.

- **`auth_param negotiate keep_alive on`** — Enables connection persistence for the Negotiate authentication exchange. This can be important because Kerberos authentication may require multiple HTTP request-response exchanges. It does not mean that a user remains authenticated indefinitely.

- **`acl authenticated proxy_auth REQUIRED`** — Defines an ACL that matches requests authenticated through a supported proxy authentication scheme. Squid can reference this ACL in its `http_access` rules to require authentication before allowing access.

### Relationship with ufdbGuard

Squid authentication and ufdbGuard URL filtering are related but distinct functions. The `authenticated` ACL identifies authenticated requests; it does not, by itself, restrict ufdbGuard to authenticated users.

The effective behavior depends on Squid's access-control rules, their order, and how ufdbGuard's user sources are configured. Client IP-based exceptions may also allow access without requiring the same authentication flow.

## 3. Kerberos Configuration

Kerberos configuration defines the default realm and maps the Active Directory DNS domain to the corresponding Kerberos realm.

The Squid Negotiate helper relies on the following components:

- **Kerberos keytab:** Contains service authentication keys used to authenticate the proxy service without storing a plaintext password in the helper configuration.
- **HTTP service principal:** Identifies the proxy service and is associated with the proxy hostname.
- **Kerberos realm:** Identifies the Kerberos authentication domain corresponding to the Active Directory environment.

The keytab must be protected and accessible only to the appropriate service context. Keytabs, passwords, tickets, and other authentication secrets must never be committed to the public repository.

## 4. Samba and Winbind Integration

The Samba configuration identifies the Linux host as an Active Directory domain member and uses Active Directory security:

```conf
server role = member server
security = ADS
realm = <KERBEROS-REALM>
kerberos method = secrets and keytab
```

The configuration also defines identity-mapping settings for the default mapping domain and the joined Active Directory domain.

These settings support domain membership and identity integration. Configuration entries alone do not prove that domain membership, Winbind services, or user and group lookups are working correctly. Each component should be validated independently.

## 5. ufdbGuard User Sources and Policies

The ufdbGuard configuration defines multiple user sources through `execuserlist` scripts. These sources can provide information used to associate clients with the appropriate filtering policies.

The configuration also supports sources based on client IP addresses, allowing filtering behavior to be associated with specific clients or groups of clients.

Filtering policies reference these sources and apply URL categories to determine which destinations are allowed or blocked. The effective behavior depends on source definitions, category rules, policy order, and the default policy.

For public examples, internal source names, script paths, organizational group names, and sensitive filtering rules should be replaced with generic values.
