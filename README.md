# ops-guru.godaddy

This role helps to manipulates your GoDaddy account using the REST API

## Requirements

* Obtain GoDaddy REST API Credentials [GoDaddy Dev Site](https://developer.godaddy.com/keys)
    * Make sure you create a "Production" key
* To simplify execution and command length, you may want to follow example's idea of creating variables `.yml` file
* just an idea: do not keep your api credentials in source control



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

godaddy_api_key:      '{{lookup("env", "GODADDY_API_KEY")}}'
godaddy_api_secret:   '{{lookup("env", "GODADDY_API_SECRET")}}'
godaddy_domain_name:  '{{lookup("env", "GODADDY_DOMAIN")}}'

```

### Example playbook

* Assumptions:
    * The below is executed under your Ansible 'project' root folder
    * The role is already consumed by `ansible-galaxy` or `git`, i.e. it is locally available
    * The Playbook is a copy of the playbook from `tests` folder of the role
        * Under convenient name, here I assume it is `site.yml`

Just to make things standard, here's that file:

```yamlex
# vim: ts=2 sw=2 et ft=ansible.yaml
---
# test.yml
- name: run role ops-guru.godaddy
  hosts: localhost
  become: False
  roles:
  - role: ops-guru.godaddy
```
### Preparation:

Set environment variables (for convenience)

```bash
$ export GODADDY_API_KEY="<your key>"
$ export GODADDY_API_SECRET="<your secret>"
$ export GODADDY_DOMAIN="<your domain>"
$ cat > godaddy_data.yml << _EOF
godaddy_api_key: '${GODADDY_API_KEY}'
godaddy_api_secret: '${GODADDY_API_SECRET}'
godaddy_domain_name: '${GODADDY_DOMAIN}'
_EOF
```

#### To add dns 'A' record 'myservice.mydomain.com':
```bash
$ ansible-playbook \
    -i inventory \
    -e @godaddy_data.yml \
    -e godaddy_record_name=myservice \
    -e record_data=1.2.3.4 \
    site.yml
```

#### To remove dns "A" record "myservice.${GODADDY_DOMAIN}":
```bash
$ ansible-playbook \
    -i inventory \
    -e @godaddy_data.yml \
    -e godaddy_record_name=myservice \
    -e record_data=1.2.3.4 \
    -e action=remove \
    site.yml
```
#### To add dns 'SRV' record for ldap service on port 389 tcp:
```bash
$ ansible-playbook \
    -i inventory \
    -e @godaddy_data.yml \
    -e godaddy_record_name=ldap \
    -e record_data=1.2.3.4 \
    -e service=ldap \
    -e protocol=tcp \
    -e port=389 \
    site.yml
```


License
-------

BSD

Author Information
------------------

Max Kovgan <max@opsguru.io>
