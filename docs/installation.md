# Installation Guide

## Overview

This guide documents the installation prerequisites and manual compilation workflow for a Debian 12 web proxy using Squid and ufdbGuard.

The ufdbGuard procedure is based on the deployment experience with version 1.35.8. Build dependencies and installation paths may differ in other versions.

## Requirements

- Debian 12 (Bookworm)
- Squid 5.7-2+deb12u6
- ufdbGuard 1.35.8
- Kerberos 1.20.1-2+deb12u5
- Winbind 4.17.12+dfsg-0+deb12u4

### Software Components

| Component              | Purpose 
|------------------------|---------
| Squid                  | HTTP/HTTPS proxy and access-control engine 
| ufdbGuard              | URL filtering and policy enforcement integration 
| build-essential        | Compiler, linker, Make, and essential build tools 
| zlib development files | Compression library headers and development files 
| bzip2 and libbz2-dev   | Bzip2 utilities and development files 
| libssl-dev             | OpenSSL development files 
| netcat-openbsd         | Network diagnostic utility 
| wget                   | Download the source archive 
| tar                    | Extract the source archive 

## 1. Install Squid

Install Squid from the configured Debian repositories:

```bash
sudo apt update
sudo apt install -y squid
```

Verify the installation:

```bash
squid -v
systemctl status squid --no-pager
```

Squid configuration and integration with ufdbGuard are covered separately.

## 2. Install ufdbGuard Build Dependencies

Update package metadata and install the required build tools and libraries:

```bash
sudo apt update
sudo apt install -y \
    build-essential \
    gcc \
    zlib1g-dev \
    libbz2-dev \
    bzip2 \
    libssl-dev \
    netcat-openbsd \
    wget
```

These packages provide the compiler toolchain, compression and SSL development libraries, download utility, and network diagnostic tool used by this deployment procedure.

## 2.1. Download and Extract ufdbGuard

Download the ufdbGuard 1.35.8:

```bash
wget https://www.urlfilterdb.com/files/downloads/ufdbGuard-1.35.8.tar.gz
```

Extract the archive:

```bash
tar -xzf ufdbGuard-1.35.8.tar.gz
cd ufdbGuard-1.35.8
```

Confirm that the source directory contains the `configure` script before continuing.

## 2.2. Configure and Compile

Make standard system paths available to the current shell:

```bash
export PATH=/usr/local/sbin:/usr/local/bin:/usr/sbin:/usr/bin:/sbin:/bin:/usr/local/ufdbguard/bin
```

Configure the source:

```bash
./configure
```

If configuration completes successfully, compile the program:

```bash
make
```

Review the output and resolve any compilation errors before installing.


## 2.3. Create the Service Account

Create a dedicated system group and service account:

```bash
sudo groupadd -r ufdb
sudo useradd -r -g ufdb -s /bin/false ufdb
```

If the group or account already exists, inspect it before attempting to create it again.

## 2.4. Install ufdbGuard

Install the compiled files:

```bash
sudo make install
```

The deployment documented here uses `/usr/local/ufdbguard/` as the expected installation prefix. The actual paths depend on the build configuration and installation output.

Inspect the installed files before creating symbolic links.


## 2.5. Configure Command Paths with Symbolic Links

### Why Symbolic Links Were Used

During this deployment, ufdbGuard executables were installed under `/usr/local/ufdbguard/bin/`.

Some commands or integration scripts may expect executables to be available through standard system directories, such as `/usr/sbin/` or `/usr/local/bin/`.

Symbolic links provide alternative paths to existing executables without duplicating the files.

**Important:** These symbolic links are specific to the reference environment and are not a universal ufdbGuard installation requirement. Other installations may use different paths or may not require additional links.

### Verify the Installation Paths

Before creating links, confirm that the source executables exist:

```bash
ls -l /usr/local/ufdbguard/bin/ufdbgclient
ls -l /usr/local/ufdbguard/bin/ufdbGenTable
```

Inspect the destination paths as well, because `ln -sf` may replace an existing file or symbolic link.

### Create the Symbolic Links

In the reference environment, the following links were used:

```bash
sudo ln -sf /usr/local/ufdbguard/bin/ufdbgclient /usr/sbin/ufdbgclient
sudo ln -sf /usr/local/ufdbguard/bin/ufdbgclient /usr/sbin/ufdbclient
sudo ln -sf /usr/local/ufdbguard/bin/ufdbGenTable /usr/local/bin/ufdbGenTable
```

The links serve the following purposes:

- `/usr/sbin/ufdbgclient`: provides an alternative command path for `ufdbgclient`.
- `/usr/sbin/ufdbclient`: provides a compatibility alias where required by the deployed configuration.
- `/usr/local/bin/ufdbGenTable`: makes the table-generation utility available through `/usr/local/bin/`.

Create only the links required by the actual installation and integration configuration.

### Verify the Symbolic Links

Inspect the links:

```bash
ls -l /usr/sbin/ufdbgclient
ls -l /usr/sbin/ufdbclient
ls -l /usr/local/bin/ufdbGenTable
```

Confirm that each link points to an existing executable. If a link is broken, verify the installation prefix and the actual executable location before recreating it.

Finally, test the relevant command using the options supported by the installed ufdbGuard version.


## 3. Post-Installation Validation

Before integrating ufdbGuard with Squid, verify:

- The installed ufdbGuard version and executable paths.
- Ownership and permissions of required files and directories.
- The service account and its access to required resources.
- Availability and generation of the URL filtering database.
- The ufdbGuard service status, where applicable.
- Squid-to-ufdbGuard integration and filtering behavior.
- Logs and error messages during startup and test requests.

