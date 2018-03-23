# Ansible Kopano Role

Install and configure kopano, with batteries included

[![Build Status](https://travis-ci.org/computerlyrik/ansible-kopano.svg?branch=master)](https://travis-ci.org/computerlyrik/ansible-kopano)

# Introduction

### Supported OS
**Ubuntu 16.04** and **Ubuntu 18.04**

### Supported Kopano versions
**Kopano Core 8.6** and **Kopano Core 8.7**

### Overview

Installs a complete kopano appliance for one or more domains.

**Functional Capablilities:**

- Kopano Core Support
- Kopano Webapp Support
- Kopano Spamd Support
- Z-Push Support
- SSL / TLS
- Catchall
- Initial User Creation
- Spamfilter & Antivirus including auto-learning

### Postfix Installation & Configuration

This server does not install a mailserver. You should have a postfix installed and configured.

However, this role provides some autoconfiguration of postfix mailserver regarding communication between kopano and amavisd. See the according *Configuration* section below.

# Configuration aka Role Variables

Please see `defaults/main.yml` for an extensive list.

### Mandatory variables

You need to configure at least your `kopano__serial_key` and a `kopano__mysql_password`.

### Supported kopano versions
Runs by default version `8.7`, but supports also `8.6` by setting `kopano__version`

### Database
Configure the `kopano__mysql_*` properties to your need. By default the setup uses a database `kopano` with a user `kopano`.

### SSL
Path to local files given in `kopano__ssl__*`, kopano will activate SSL encryption everywhere possible (imaps, https)

### Initial kopano user
If all user information under `kopano__user_*` is filed, an initial user will be created if not already present.

### Postfix autoconfiguration
To configure postfix either

* set `kopano__postfix__configuration_enable` to true
* define at leaste one entry in `kopano__postfix__alias_maps`

Howto define a catchall within alias_maps, see example below

# Example Playbook

### Simple role call with following some capabilities enabled

- SSL
- Initial User
- Catchall

```
- name: computerlyrik.kopano
  vars:
    kopano__serial_key: "{{ vault_kopano_serial_key }}"

    kopano__mysql_database: mysqlDatabase
    kopano__mysql_user: mysqlUser
    kopano__mysql_password: "{{ vault_kopano_mysql_password }}"

    kopano__ssl__cert_file: /path/to/cert/file.crt
    kopano__ssl__key_file: /path/to/key/file.key

    kopano__user_login: john
    kopano__user_fullname: John Doe
    kopano__user_password: "{{ vault_kopano_admin_password }}"
    kopano__user_email: john.doe@example.com

    kopano__postfix__configuration_enable: true
    kopano__postfix__alias_maps:
      "@example.com": "admin@example.com"
```

# Testing

### Set up kopano locally

Start a local test with your personal kopano serial key:

```
virtualenv ~/.molecule_virtualenv
. ~/.molecule_virtualenv/bin/activate
pip install molecule python-vagrant
```

```
KOPANO_SERIAL_KEY=XXXXXXXXX molecule test --scenario-name local                  

```

### Run molecule tests

`molecule test`

# Migrate from old zarafa

### Database dump on source machine
`mydumper -u ... -p ... -B zarafa-dbname`

### Database restore on target machine
`myloader -d export_dir -B kopano-dbname -o`

### Attachments 
```
rsync -avz --progress src_host:/var/lib/zarafa/attachments/* /var/lib/kopano/attachments
chown kopano:kopano -R /var/lib/kopano/attachments
```


License
-------

Apache v2

Author Information
------------------

An optional section for the role authors to include contact information, or a website (HTML is not allowed).
