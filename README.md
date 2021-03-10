# Ansible Role MySQL
[![License](https://img.shields.io/badge/license-Apache-green.svg?style=flat)](https://raw.githubusercontent.com/lean-delivery/ansible-role-mysql/master/LICENSE)
![Ansible](https://img.shields.io/badge/dynamic/json.svg?label=min_ansible_version&url=https%3A%2F%2Fgalaxy.ansible.com%2Fapi%2Fv1%2Froles%2F35413%2F&query=$.min_ansible_version)

Version: 0.0.2

Index
=================
<!--ts-->
   * [Summary](#Summary)
   * [Role tasks](#Role-tasks)
   * [Requirements](#Requirements)
   * [Support](#Support)
   * [Homologated](#Homologated)
      * [MySQL](#MySQL)
      * [MariaDB](#MariaDB)
   * [Requirements file](#Requirements-file)
   * [Variables](#Variables)
   * [Dependencies](#Dependencies)
   * [Example Playbooks](#Example-Playbooks)
      * [Installing MySQL 5.5 / 5.6 / 5.7](#Installing-MySQL-55--56--57)
      * [Installing MySQL 8.0](#Installing-MySQL-80-version)
      * [Installing MariaDB 10.3 / 10.4 / 10.5](#Installing-MariaDB-103--104--105)
   * [Tests](#Tests)
   * [License](#License)
   * [Author Information](#Author-Information)
<!--te-->

## Summary

### Simple, practical and easy to use

This role installs and configures MySQL or MariaDB server on RHEL/CentOS/Ubuntu/Amazonlinux servers.

## Role tasks

  - Installs MySQL/MariaDB
  - Reset root password for mysql
  - Create db and users
  - Create .my.cnf in root

## Requirements

To apply:
  - Ansible >= 2.9.x

To tests:
  - Ansible >= 2.9.x
  - VirtualBox >= 6.1
  - Vagrant >= 2.2.6
  - vagrant-hostsupdater

## Support

### Supported versions:
| Oracle Mysql | Mariadb |
| ------------ | ------- |
| 5.5          | -       |
| 5.6          | 10.3    |
| 5.7          | 10.4    |
| 8.0          | 10.5    |

### Supported OS:
| Ubuntu | Debian | RHEL | CentOS | Amazonlinux |
| ------ | ------ | ---- | ------ | ----------- |
| 18.04  | 9      | 7    | 7      | -           |
| -      | 10     | 8    | 8      | 2           |

## Homologated

### MySQL
| Version | Ubuntu 18.04 | Debian 9 | Debian 10 | Rhel 7 | Rhel 8 | Centos 7 | CentOS 8 | Amazonlinux 2 |
| ------- | ------------ | -------- | --------- | ------ | ------ | -------- | -------- | ------------- |
| 5.5     | ✔️            | ✔️        | ❌        | ✔️      | ❌     | ✔️        | ❌       | ✔️             |
| 5.6     | ✔️            | ✔️        | ❌        | ✔️      | ❌     | ✔️        | ❌       | ✔️             |
| 5.7     | ✔️            | ✔️        | ❌        | ✔️      | ❌     | ✔️        | ❌       | ✔️             |
| 8.0     | ✔️            | ✔️        | ✔️         | ✔️      | ✔️      | ✔️        | ✔️        | ✔️             |

### MariaDB
| Version | Ubuntu 18.04 | Debian 9 | Debian 10 | Rhel 7 | Rhel 8 | Centos 7 | CentOS 8 | Amazonlinux 2 |
| ------- | ------------ | -------- | --------- | ------ | ------ | -------- | -------- | ------------- |
| 10.3    | ✔️            | ✔️        | ✔️         | ✔️      | ✔️      | ✔️        | ✔️        | ✔️             |
| 10.4    | ✔️            | ✔️        | ✔️         | ✔️      | ✔️      | ✔️        | ✔️        | ✔️             |
| 10.5    | ✔️            | ✔️        | ✔️         | ✔️      | ✔️      | ✔️        | ✔️        | ✔️             |

## Requirements file

Requirements file example (`requirements.yml`):

```sh
- src: git@github.com:mrlucasfreitas/ansible-role-mysql.git
  scm: git
  version: "v0.0.2"
  name: mysql
```

## Variables

Available variables are listed below, along with default values:

Mysql/MariaDB repository settings:

    mysql_repo: *default value depends on OS*   
    mysql_gpgkey: *default value depends on OS*   
    mysql_apt_keyserver: *default value depends on OS*  
    mysql_repofile: /etc/yum.repos.d/mysql.repo|/etc/yum.repos.d/mariadb.repo
    mysql_apt_key_id: *default value depends on OS*   
    mysql_repo_disable_list: *default - undefined*. For CentOS 8 it's now list of `AppStream` and `Stream-AppStream`.

    mysql_packages:
      - mysql-community-server   # (mysql-community-server/MariaDB-server)
      - mysql-community-client   # (mysql-community-client/MariaDB-client)
      
If you want to select a specific minor version of package, you can enter appropriate package name, for instance:

    mysql_packages:
      - mysql-community-server-8.0.16-2.el7.x86_64
      - mysql-community-client-8.0.16-2.el7.x86_64
      
                                 # (MariaDB-common)
    mysql_daemon: mysqld         # (mysqld/mariadb)
    mysql_version: 5.7           # (for mysql = 5.5/5.6/5.7; for mariadb = last (10.5) )

(OS-specific, RedHat/CentOS defaults listed here) Packages to be installed. In some situations, you may need to add additional packages, like `mysql-devel`.

    mysql_user_home: /root
    mysql_user_name: root
    mysql_user_password: root

The home directory inside which Python MySQL settings will be stored, which Ansible will use when connecting to MySQL. This should be the home directory of the user which runs this Ansible role. The `mysql_user_name` and `mysql_user_password` can be set if you are running this role under a non-root user account and want to set a non-root user.

    mysql_root_home: /root
    mysql_root_username: root
    mysql_root_password: root

Enable .my.cnf credentials in /root.

    mysql_root_mycnf_credential: true

The MySQL root user account details.

    mysql_config_file: *default value depends on OS*
    mysql_config_include_dir: *default value depends on OS*
    
The main my.cnf configuration file and include directory.

    overwrite_global_mycnf: true

Whether the global my.cnf should be overwritten each time this role is run. Setting this to `no` tells Ansible to only create the `my.cnf` file if it doesn't exist. This should be left at its default value (`yes`) if you'd like to use this role's variables to configure MySQL.

    mysql_config_include_files: []

A list of files that should override the default global my.cnf. Each item in the array requires a "src" parameter which is a path to a file. An optional "force" parameter can force the file to be updated each time ansible runs.

    mysql_databases: []

The MySQL databases to create. A database has the values `name`, `encoding` (defaults to `utf8`), `collation` (defaults to `utf8_general_ci`) and `replicate` (defaults to `1`, only used if replication is configured). The formats of these are the same as in the `mysql_db` module.

    mysql_users: []

The MySQL users and their privileges. A user has the values:

  - `name`
  - `host` (defaults to `localhost`)
  - `password` (can be plaintext or encrypted—if encrypted, set `encrypted: yes`)
  - `encrypted` (defaults to `no`)
  - `priv` (defaults to `*.*:USAGE`)
  - `append_privs` (defaults to `no`)
  - `state`  (defaults to `present`)

The formats of these are the same as in the `mysql_user` module.

    mysql_port: "3306"
    mysql_bind_address: '0.0.0.0'
    mysql_datadir: /var/lib/mysql
    mysql_socket: *default value depends on OS*
    mysql_pid_file: *default value depends on OS*

Default MySQL connection configuration.

    mysql_log_file_group: mysql *adm on Debian*
    mysql_log: ""
    mysql_log_error: *default value depends on OS*
    mysql_syslog_tag: *default value depends on OS*

MySQL logging configuration. Setting `mysql_log` (the general query log) or `mysql_log_error` to `syslog` will make MySQL log to syslog using the `mysql_syslog_tag`.

    mysql_slow_query_log_enabled: false
    mysql_slow_query_log_file: *default value depends on OS*
    mysql_slow_query_time: 2

Slow query log settings. Note that the log file will be created by this role, but if you're running on a server with SELinux or AppArmor, you may need to add this path to the allowed paths for MySQL, or disable the mysql profile. For example, on Debian/Ubuntu, you can run `sudo ln -s /etc/apparmor.d/usr.sbin.mysqld /etc/apparmor.d/disable/usr.sbin.mysqld && sudo service apparmor restart`.

    mysql_key_buffer_size: "256M"
    mysql_max_allowed_packet: "64M"
    mysql_table_open_cache: "256"
    [...]

The rest of the settings in `defaults/main.yml` control MySQL's memory usage and some other common settings. The default values are tuned for a server where MySQL can consume ~512 MB RAM, so you should consider adjusting them to suit your particular server better.

    mysql_server_id: "1"
    mysql_max_binlog_size: "100M"
    mysql_binlog_format: "ROW"
    mysql_expire_logs_days: "10"
    mysql_replication_role: ''
    mysql_replication_master: ''
    mysql_replication_user: []

Replication settings. Set `mysql_server_id` and `mysql_replication_role` by server (e.g. the master would be ID `1`, with the `mysql_replication_role` of `master`, and the slave would be ID `2`, with the `mysql_replication_role` of `slave`). The `mysql_replication_user` uses the same keys as `mysql_users`, and is created on master servers, and used to replicate on all the slaves.

`mysql_replication_master` needs to resolve to an IP or a hostname which is accessable to the Slaves (this could be a `/etc/hosts` injection or some other means), otherwise the slaves cannot communicate to the master.

### additional_parameters
Also you can set other parametrs, which are not listed here and it will be written to the configuration file `my.cnf`. 

Example:
```yaml
     additional_parameters:
        - name: mysql_expire_logs_days
          value: 11
```
#

### MariaDB usage

This role works with either MySQL or a compatible version of MariaDB. On RHEL/CentOS 7+, the mariadb database engine was substituted as the default MySQL replacement package. No modifications are necessary though all of the variables still reference 'mysql' instead of mariadb.

## Dependencies

Due to new breaking changes in MySQL 8.0 we included modified module `mysql_user`. It's shipping with that role and resides in `library` directory. Current Ansible module `mysql_user` is not compatible with latest changes but fixes are already in place and new Ansible release 2.8 should not require customized module to run.

## Example Playbooks

**ATTENTION!** Mysql role must be executed before any other because of package dependency.

Example:
```sh
---
  - name: Deploy MySQL Server
    hosts: mysqlserver

    roles:
      # mysql role must be executed before any other because of package dependency
      - role: mysql
        tags: mysql
      - role: nginx
        tags: nginx
      - role: php
        tags: php
```

### Installing MySQL 5.5 / 5.6 / 5.7:
```yaml
- hosts: ubuntu18 centos7 amazonlinux2
  become: true
  remote_user: vagrant
  roles:
    - ansible-role-mysql
  vars:
  
    mysql_daemon: mysqld
    mysql_version: 5.7
    
    mysql_root_username: root
    mysql_root_password: r00t.P4$$
    mysql_root_mycnf_credential: true
  
    mysql_port: "3306"
    mysql_bind_address: "0.0.0.0"
    mysql_skip_name_resolve: false

    mysql_databases:
      - name: example_db
        encoding: latin1
        collation: latin1_general_ci
    
    mysql_users:
      - name: example_user
        host: "%"
        password: ex4mpl3_Pa$$
        priv: "example_db.*:ALL"
``` 

### Installing MySQL 8.0 version:
```yaml
- hosts: ubuntu18 centos7 centos8 amazonlinux2
  become: true
  remote_user: vagrant
  roles:
    - ansible-role-mysql
  vars:
  
    mysql_daemon: mysqld
    mysql_version: 8.0
    
    mysql_root_username: root
    mysql_root_password: r00t.P4$$
    mysql_root_mycnf_credential: true
  
    mysql_port: "3306"
    mysql_bind_address: "0.0.0.0"
    mysql_skip_name_resolve: false

    mysql_databases:
      - name: example_db
        encoding: latin1
        collation: latin1_general_ci
    
    mysql_users:
      - name: example_user
        host: "%"
        password: ex4mpl3_Pa$$
        priv: "example_db.*:ALL"
``` 

### Installing MariaDB 10.3 / 10.4 / 10.5:
```yaml
- hosts: ubuntu18 centos7 centos8 amazonlinux2
  become: true
  remote_user: vagrant
  roles:
    - ansible-role-mysql
  vars:
  
    mysql_daemon: mariadb
    mysql_version: 10.5
    
    mysql_root_username: root
    mysql_root_password: r00t.P4$$
    mysql_root_mycnf_credential: true
  
    mysql_port: "3306"
    mysql_bind_address: "0.0.0.0"
    mysql_skip_name_resolve: false

    mysql_databases:
      - name: example_db
        encoding: latin1
        collation: latin1_general_ci
    
    mysql_users:
      - name: example_user
        host: "%"
        password: ex4mpl3_Pa$$
        priv: "example_db.*:ALL"
``` 

## Tests

To test on vagrant:
```sh
# Show vms
vagrant status
```

Edit `tests/test.yml` and adjust as needed:
```sh
---
- name: Install Python on Ubuntu
  hosts: ubuntu18
  become: true
  gather_facts: no

  tasks:
    - name: Install Python 2 on Ubuntu
      raw: test -e /usr/bin/python || (apt install -y python-minimal)
      changed_when: false
  
    - name: Install Python 3 on Ubuntu
      raw: test -e /usr/bin/python3 || (apt install -y python3-minimal)
      changed_when: false

- hosts: ubuntu18 centos7 centos8 amazonlinux2
  become: true
  remote_user: vagrant
  roles:
    - ansible-role-mysql
  vars:
    # Define a what install mysqd (version 5.5/5.6/5.7/8.0) or mariabd (version 10.3/10.4/10.5)
    mysql_daemon: mysqld
    mysql_version: 8.0
  ...
```

Run VM:
```sh
# Create example
vagrant up amazonlinux2
```

Apply modifications from `tests/test.yml`
```sh
# Provision example
vagrant provision amazonlinux2
# or
ansible-playbook tests/test.yml -i tests/inventory -l amazonlinux2 -u vagrant
```

After testing:
```sh
# Delete example
vagrant destroy amazonlinux2
```

More information about vagrant [here](https://github.com/mrlucasfreitas/easy-vagrant).

#
__Note__: CentOS always do password reset via `rescue` section: It should be noted that the play continues if a rescue section completes successfully as it ‘erases’ the error status (but not the reporting), this means it will appear in the **playbook statistics** ONLY.

**ATTENTION!** Note that override parameters in playbook have to be set as `role parameters` (see example above). Parameters set as usual hostvars or inventory parameters will not supercede default role parameters set by role scenario depending on OS version etc. 

## License
Apache

## Author Information
Authors:
  - Lean Delivery Team team@lean-delivery.com
  - Lucas Freitas (mrlucasfreitas)

Revised, modified and approved:
  - Lucas Freitas (mrlucasfreitas)
