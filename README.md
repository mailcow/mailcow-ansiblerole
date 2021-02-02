# mailcow: dockerized - Ansible role 🐮 + 🐋 = 💕

[![Build Status](https://drone.mailcow.email/api/badges/mailcow/mailcow-ansiblerole/status.svg)](https://drone.mailcow.email/mailcow/mailcow-ansiblerole)

This role will setup a mailcow dockerized email server.

## Prerequisites

- Up and running Ubuntu/Debian host (other distributions not supported/tested for now)

## Requirements

| Requirements   | Description                            |
|----------------|----------------------------------------|
| docker ce      | Docker has to be installed on the host |
| docker-compose | docker-compose is needed               |

## Notes
This role will use by default the `inventory_hostname` as mailcow hostname, this means that you have to use the full qualified domain name as your inventory hostname e.g. `mail.mailcow.tld` or you set `mailcow__hostname` to the correct FQDN.

## Variables
<!-- Markdown table can be edited with https://www.tablesgenerator.com/markdown_tables -->
|                    name                   |                                   purpose                                   |       default value       |                                    note                                   |
|:-----------------------------------------:|:---------------------------------------------------------------------------:|:-------------------------:|:-------------------------------------------------------------------------:|
|            `mailcow__hostname `           |                            sets MAILCOW_HOSTNAME                            |    `inventory_hostname`   |                 needs to be an full qualified domain name                 |
|          `mailcow__install_path`          |        sets the path where the mailcow-dockerized repo will be cloned       | `/opt/mailcow-dockerized` |                                                                           |
|           `mailcow__git_version`          |                    checkout a specific version of mailcow                   |          `master`         |                                                                           |
|            `mailcow__timezone`            |  used to set the timezone your mailcow runs in during the config generation |          not set          |                              **must be set**                              |
|   `mailcow__docker_compose_project_name`  |         sets the docker-compose projectname to a user-defined string        |    `mailcowdockerized`    |                                                                           |
|              `mailcow__theme`             |             set the default mailcow theme in vars.local.inc.php             |          `lumen`          |                                                                           |
|        `mailcow__config_http_port`        |                        sets HTTP_PORT in mailcow.conf                       |            `80`           |                                                                           |
|        `mailcow__config_http_bind`        |                        sets HTTP_BIND in mailcow.conf                       |         `0.0.0.0`         |                                                                           |
|        `mailcow__config_https_port`       |                       sets HTTPS_PORT in mailcow.conf                       |           `443`           |                                                                           |
|        `mailcow__config_https_bind`       |                       sets HTTPS_BIND in mailcow.conf                       |         `0.0.0.0`         |                                                                           |
|        `mailcow__config_acl_anyone`       |                               sets ACL_ANYONE                               |          disallow         |                                                                           |
|     `mailcow__config_maildir_gc_time`     |                     sets MAILDIR_GC_TIME in mailcow.conf                    |           `1440`          |                                                                           |
|      `mailcow__config_additional_san`     |                     sets ADDITIONAL_SAN in mailcow.conf                     |                           |                            needs to be a list                             |
|    `mailcow__config_skip_lets_encrypt`    |                    sets SKIP_LETS_ENCRYPT in mailcow.conf                   |                           |                                                                           |
|      `mailcow__config_enable_ssl_sni`     |                     sets ENABLE_SSL_SNI in mailcow.conf                     |                           |                                                                           |
|      `mailcow__config_skip_ip_check`      |                      sets SKIP_IP_CHECK in mailcow.conf                     |                           |                                                                           |
|  `mailcow__config_skip_http_verification` |                 sets SKIP_HTTP_VERIFICATION in mailcow.conf                 |            `n`            |                                                                           |
|        `mailcow__config_skip_clamd`       |                       sets SKIP_CLAMD in mailcow.conf                       |            `n`            |                                                                           |
|        `mailcow__config_skip_solr`        |                        sets SKIP_SOLR in mailcow.conf                       |            `n`            |                                                                           |
|        `mailcow__config_solr_heap`        |                        sets SOLR_HEAP in mailcow.conf                       |           `1024`          |                                                                           |
|        `mailcow__config_skip_sogo`        |                        sets SKIP_SOGO in mailcow.conf                       |            `n`            |                                                                           |
| `mailcow__config_allow_admin_email_login` |                 sets ALLOW_ADMIN_EMAIL_LOGIN in mailcow.conf                |            `n`            |                                                                           |
|       `mailcow__config_use_watchdog`      |                      sets USE_WATCHDOG in mailcow.conf                      |            `n`            |                                                                           |
|  `mailcow__config_watchdog_notify_email`  |                  sets WATCHDOG_NOTIFY_EMAIL in mailcow.conf                 |                           |                                                                           |
|   `mailcow__config_watchdog_notify_ban`   |                   sets WATCHDOG_NOTIFY_BAN in mailcow.conf                  |            `y`            |                                                                           |
|        `mailcow__config_log_lines`        |                        sets LOG_LINES in mailcow.conf                       |           `9999`          |                                                                           |
|   `mailcow__config_sogo_expire_session`   |                   sets SOGO_EXPIRE_SESSION in mailcow.conf                  |           `480`           |                                                                           |
|         `mailcow__install_updates`        | if `yes` the mailcow ansible role will also update an existing installation |           `yes`           |                                                                           |
|     `mailcow__redirect_http_to_https`     |         if `yes`, all requests via HTTP will be redirected to HTTPS         |            `no`           | also see https://mailcow.github.io/mailcow-dockerized-docs/u_e-80_to_443/ |

## Usage

Minimal playbook:

```yaml
---

- name: Install Python3
  hosts: all
  become: true
  gather_facts: false
  roles:
    - { role: raw,0.0, vars: {command: 'apt-get install -y python3 python3-pip'} }

- name: Main Playbook
  hosts: all
  become: true
  gather_facts: true
  vars:
    mailcow__timezone: Europe/Berlin
  roles:
    - Ansible-Roles.docker-ce
    - Ansible-Roles.docker-compose
    - Ansible-Roles.mailcow
```
