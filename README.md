# opsguru.godaddy

This role helps to manipulate DNS records hosted on GoDaddy, using their new REST API

## Requirements

* Obtain GoDaddy REST API Credentials [GoDaddy Dev Site](https://developer.godaddy.com/keys)
* just an idea: do not keep your api credentials in source control
* To simplify execution and command length, you may want to follow example's idea of creating variables `.yml` file



## Role Variables

The variables are defined in `vars/main.yml` with defaults in `defaults/main.yml`

| Name                  | Meaning                        | Mandatory                  | Default     | Example                                      |
|:--------------------- |:------------------------------ |:-------------------------: |:-----------:|:-------------------------------------------- |
| `action`              | what do do?                    | `N`                        | `add`       | either `add` or `remove`                     |
| `godaddy_api_key`     | GoDaddy REST API "key" field   | `Y`                        | N/A         | 33 chars long alphanumeric mixed case string |
| `godaddy_api_secret`  | GoDaddy REST API "secret" field| `Y`                        | N/A         | 22 chars long alphanumeric mixed case string |
| `godaddy_domain_name` | Domain you're managing         | `Y`                        | N/A         | `mydomain.com`                               |
| `godaddy_record_name` | Record Name                    | `Y`                        | N/A         | `service1`                                   |
| `record_data`         | Record Value                   | `Y`                        | N/A         | `1.2.3.4`                                    |
| `type`                | Record Type                    | `N`                        | `A`         | refer to list of DNS record types            |
| `ttl`                 | Record TTL in seconds          | `N`                        | `3600`      | positive integer                             |
| `priority`            | Record priority                | `N`                        | Set by API  | positive integer                             |
| `service`             | Service Record `Service` Field | `Y` for `SRV` records only | N/A         | `ldap`, full list in `/etc/services`         |
| `protocol`            | Service Record `Proto` Field   | `Y` for `SRV` records only | N/A         | `TCP`, `UDP` and maybe others                |
| `port`                | Service Record `port` Field    | `Y` for `SRV` records only | N/A         | positive integer port of the service         |
| `weight`              | Service Record `weight` Field  | `N`                        | Set by API  | positive integer                             |



## Dependencies

None

## Examples

### Example extra variables file


```yamlex
# godaddy_vars.yml
---
# this will allow you shorter commadn lines
# Obtain API Key and Secret at https://developer.godaddy.com
# you most probably will need to add several records of the same domain

godaddy_api_key:      '123456789abcdef0123456789abcdef'
godaddy_api_secret:   'ABCDEFG012357asdfasdasdj1234'
godaddy_domain_name:  'mydomain.com'

```

### Example playbook
```yamlex
# site.yml
---
- name: showcase of role opsguru.godaddy
  hosts: localhost
  roles:
  - role: opsguru.godaddy

```

#### To add dns 'A' record 'myservice.mydomain.com':
```bash

$ ansible-playbook \
    -i inventory \
    -e "@godaddy_vars.yml" \
    -e godaddy_record_name=myservice \
    -e record_data=1.2.3.4 \
    site.yml
```

#### To remove dns 'A' record 'myservice.mydomain.com':
```bash

$ ansible-playbook \
    -i inventory \
    -e "@godaddy_vars.yml" \
    -e action=remove \
    -e godaddy_record_name=myservice \
    -e record_data=1.2.3.4 \
    site.yml
```

License
-------

BSD

Author Information
------------------

Max Kovgan <max@opsguru.io>
