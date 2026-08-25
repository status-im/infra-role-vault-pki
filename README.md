# Description

This role helps generate certificates from HashiCorp Vault PKI.

# Usage

This role can be use for 2 purposes

## Generate Application CA

> !Alert! Due to an issue with the verification of certificate, it isn't recommended to used this part yet.
> Insted generate a Root CA with the command line or interface, and save the keys

The role checks the local CA certificate and generates a new one when it is missing or near expiry.

### Process

```
    +-------------------+   Missing/expiring   +--------------+          +----------+          +---------------+          +-----------------+
    |                   |--------------------->| Generate CSR |--------->| Sign CSR |--------->| Add CRT as CA |--------->| Write CRT + KEY |
    | Check Local Files |                      |              |          |          |          |               |          |    on host      |
    |                   |                      +--------------+          +----------+          +---------------+          +-----------------+
    +-------------------+
                |
                | CRT and KEY exist, CRT valid beyond renewal window
                v
              Stop
```

### Configuration

```yaml
cert_common_name:           'application-ca'
cert_role_allow_domains:    'example.org'
cert_info:
  organization: 'Org'
  unit:         'Unit'
  country:      'County'
cert_ca_ttl: '365d'
cert_certificate_path: '/certs/application-ca.crt'
cert_private_key_path: '/certs/application-ca.key'
cert_renew_before: '+30d'
```



## Generate Client certificate

### Process

```
+-------------------+   Missing/expiring   +-------------+        +--------------+          +-----------------+
|                   |--------------------->| Create Role |------->| Generate CRT |--------->| Write CRT + KEY |
| Check Local Files |                      |             |        |              |          |    on host      |
|                   |                      |             |        |              |          |                 |
+-------------------+                      +-------------+        +--------------+          +-----------------+
           |
           | CRT and KEY exist, CRT valid beyond renewal window
           v
         Stop
```

### Configuration

```yaml
cert_common_name: 'Client'
cert_domain_sans: 'example.org'
cert_ip_sans:     '192.168.1.0'
cert_certificate_path: '/certs/application.crt'
cert_private_key_path: '/certs/application.key'
cert_renew_before: '+30d'


# Role Configuration
cert_role: 'application-role'
cert_role_issuer_name: 'Application'
cert_role_info:
  organization: 'Org'
  unit:         'Unit'
  country:      'County'
cert_role_max_ttl: '365d'
cert_role_allow_domains: 'example.org'
cert_role_allow_subdomains: True
```
