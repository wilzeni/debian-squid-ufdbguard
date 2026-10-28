## Active Directory Integration

```bash
ldapsearch -LLL -Y GSSAPI \
  -H ldap://dc01.example.local:3268 \
  -b 'DC=example,DC=local' \
  '(&(objectClass=user)(memberOf:1.2.840.113556.1.4.1941:=CN=SALES,OU=Groups,DC=example,DC=local))' \
  sAMAccountName
```

- **LDAP:** Queries directory information and identifies users belonging to specific groups.
- **Kerberos/GSSAPI:** Provides authenticated access to LDAP when configured.
- **User lookup scripts:** Retrieve account names from Active Directory and format them for use by the filtering configuration.


The Active Directory matching rule `1.2.840.113556.1.4.1941` allows the query to evaluate nested group membership.

## UFDBGuard Blacklists

UFDBGuard uses domain lists to determine whether requested websites match configured filtering categories and policies.

A blacklist can be organized into category directories, for example:

```text
blacklists/
|-- Rule_Social/
|   |-- domains
|   `-- domains.ufdb
|-- Rule_Streaming/
|   |-- domains
|   `-- domains.ufdb
`-- Rule_Adult/
    |-- domains
    `-- domains.ufdb
```

### Domain List Files

The `domains` file is a text file containing the domains associated with a category.

The `ufdbGenTable` utility converts a supported text domain list into a compiled database used by UFDBGuard's lookup process.

```bash
sudo -u ufdb ufdbGenTable -n -W -d /path/to/domain-list.txt
```

The `.ufdb` file is intended to accelerate domain lookups compared with repeatedly processing the text list. After changing a source list, regenerate the compiled database as required by the installation.

- Category names referenced in `ufdbGuard.conf` must match to the configured blacklist categories.