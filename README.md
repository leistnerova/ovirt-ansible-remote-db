oVirt Engine Remote DB
======================

This role create engine and DWH postgres database to be used in engine as remote connection.

- install and configure PostgreSQL server on machine (latest from repository)
- create necessary roles and databases for engine and DWH (based on variables)
- update firewalld and iptables (based on service present)

- if PostgreSQL server is already installed then create only roles and databases

Target systems
--------------

* database

Requirements
------------

Preinstalled clean environment with configured repositories.

Role Variables
--------------

```yaml
---
# vars for PostgreSQL
ovirt_engine_remote_db_port: PostgreSQL port for listening (default: 5432)
ovirt_engine_remote_db_listen_address: Listen address (default '*')

ovirt_engine_remote_db: [yes, no] Create engine database
ovirt_engine_dwh_remote_db: [yes, no] Create DWH database

# vars for databases and roles
ovirt_engine_db_name: DB name for ovirt-engine (default: 'engine')
ovirt_engine_db_user: DB user which can access ovirt-engine DB (default: 'engine')
ovirt_engine_db_password: password for user of ovirt-engine DB
ovirt_engine_dwh_db_name: DB name for ovirt-engine-dwh (default: 'ovirt_engine_history')
ovirt_engine_dwh_db_user: DB user which can access ovirt-engine-dwh DB (default: 'ovirt_engine_history')
ovirt_engine_dwh_db_password: password for user of ovirt-engine DB

ovirt_engine_remote_db_access:  # configure access to engine remote DBs
  -
    type: host / local
    address: 0.0.0.0/0   # mask for host, omitted for local
    method: md5 / trust / ident / peer

ovirt_engine_remote_db_vacuum: [True, False] True if set db configuration for
  vacuum feature (default: True)
ovirt_engine_remote_db_vacuum_config: Default postgresql cofiguration for
  vacuum feature. See default varibles in defaults/main.yml

ovirt_engine_remote_db_force: [True, False] If engine or DWH database already exists -
  True: drop them and create clean ones, False: playbook will fail with error. (default: False)

ovirt_engine_remote_db_dump: Path to .tar dump of engine database on local machine (generated with pg_dump -F t)
ovirt_engine_remote_db_dwh_dump: Path to .tar dump of DWH database on local machine (generated with pg_dump -F t)
```

Dependencies
------------

* oVirt.repositories - this is only a must on ovirt > 4.1

Example Playbook
----------------

```yaml
- hosts: database
  vars:
    ovirt_engine_version: '4.2'
  # vars for PostgreSQL
    ovirt_engine_remote_db_port: 5432
    ovirt_engine_remote_db_listen_address: '*'

    ovirt_engine_remote_db: yes
    ovirt_engine_dwh_remote_db: no

    # vars for databases and roles
    ovirt_engine_db_name: 'engine'
    ovirt_engine_db_user: 'engine'
    ovirt_engine_db_password: 'password'
    ovirt_engine_dwh_db_name: 'ovirt_engine_history'
    ovirt_engine_dwh_db_user: 'ovirt_engine_history'
    ovirt_engine_dwh_db_password: 'password'
    ovirt_engine_remote_db_access:
      -
        type: host
        address: XX.XX.XX.XX/32
        method: md5
    ovirt_engine_remote_db_force: True
    ovirt_engine_remote_db_dump: engine_dump/engine.tar
    ovirt_engine_remote_db_dwh_dump: engine_dump/dwh.tar
  roles:
    - ansible-role-ovirt-remote-db
```

Author Information
------------------

Petr Kubica
pkubica@redhat.com

Lucie Leistnerova
lucie.leistnerova@gmail.com
