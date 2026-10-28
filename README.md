# Debian Squid Proxy with ufdbGuard

A Linux-based web proxy solution built with Debian 12, Squid, and ufdbGuard, providing centralized web access control, URL filtering, and Active Directory integration.

---

## Key Features

- **Squid Proxy:** Centralized HTTP and HTTPS proxy service.
- **UFDBGuard:** URL filtering and access policy enforcement.
- **Active Directory:** User and group-based access policies.
- **Kerberos:** Authentication for domain-related services.
- **Access Control:** Filtering based on configured categories and rules.
- **Service Management:** Configuration and maintenance of proxy components.

---

## Architecture

- **Debian 12:** Proxy server operating system.
- **Squid:** Handles proxy requests from clients.
- **UFDBGuard:** Evaluates URL filtering rules and policies.
- **Active Directory and Winbind:** Domain integration and user/group information.
- **Kerberos:** Authentication for domain-related services.

---

## Project Structure

```text
debian-squid-ufdbguard/
├── configs/
│   ├── README.md
│   ├── squid.conf
│   ├── ufdbGuard.conf
│   ├── smb.conf
│   └── krb5.conf
├── docs/
│   ├── authentication.md
│   └── installation.md
├── .gitignore
└── README.md
```

---

## Getting Started

### Documentation

The documentation in the `docs/` directory:

- `installation.md` — installation and configuration guidance.
- `authentication.md` — AD authentication and domain integration.

### Configuration Examples

The `configs/` directory contains sanitized configuration examples:

| File | Description
|------|---
| `squid.conf`      | Squid proxy configuration
| `ufdbGuard.conf`  | UFDBGuard filtering policies
| `smb.conf`        | Samba and Active Directory integration
| `krb5.conf`       | Kerberos configuration

See [`configs/README.md`](configs/README.md) for information about LDAP integration and UFDBGuard domain lists.

---
## License

Licensed under the MIT License.
