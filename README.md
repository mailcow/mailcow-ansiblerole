# mailcow: dockerized - Ansible role 🐮 + 🐋 = 💕

This role will setup a mailcow dockerized email server.

## Prerequisites

- Up and running Ubuntu/Debian host (other distributions not supported/tested for now)

## Requirements

| Requirements   | Description                            |
|----------------|----------------------------------------|
| docker ce      | Docker has to be installed on the host |
| docker-compose | docker-compose is needed               |

## Notes
this role will use the `inventory_hostname` as mailcow hostname, this means that you have to use the full qualified domain name as your inventory hostname e.g. `mail.mailcow.tld`.

## Variables

|                   name                    |                                  purpose                                   | default value |    note     |
|:-----------------------------------------:|:--------------------------------------------------------------------------:|:-------------:|:-----------:|
|          `mailcow__git_version`           |                   checkout a specific version of mailcow                   |   `master`    |             |
|            `mailcow__timezone`            | used to set the timezone your mailcow runs in during the config generation |    not set    | must be set |
|             `mailcow__theme`              |            set the default mailcow theme in vars.local.inc.php             |    `lumen`    |             |
|        `mailcow__config_http_port`        |                       sets HTTP_PORT in mailcow.conf                       |     `80`      |             |
|        `mailcow__config_http_bind`        |                       sets HTTP_BIND in mailcow.conf                       |   `0.0.0.0`   |             |
|       `mailcow__config_https_port`        |                      sets HTTPS_PORT in mailcow.conf                       |     `443`     |             |
|       `mailcow__config_https_bind`        |                      sets HTTPS_BIND in mailcow.conf                       |   `0.0.0.0`   |             |
|       `mailcow__config_acl_anyone`        |                              sets ACL_ANYONE                               |   disallow    |             |
|     `mailcow__config_maildir_gc_time`     |                    sets MAILDIR_GC_TIME in mailcow.conf                    |    `1440`     |             |
|     `mailcow__config_additional_san`      |                    sets ADDITIONAL_SAN in mailcow.conf                     |               |             |
|    `mailcow__config_skip_lets_encrypt`    |                   sets SKIP_LETS_ENCRYPT in mailcow.conf                   |               |             |
|     `mailcow__config_enable_ssl_sni`      |                    sets ENABLE_SSL_SNI in mailcow.conf                     |               |             |
|      `mailcow__config_skip_ip_check`      |                     sets SKIP_IP_CHECK in mailcow.conf                     |               |             |
| `mailcow__config_skip_http_verification`  |                sets SKIP_HTTP_VERIFICATION in mailcow.conf                 |      `n`      |             |
|       `mailcow__config_skip_clamd`        |                      sets SKIP_CLAMD in mailcow.conf                       |      `n`      |             |
|        `mailcow__config_skip_solr`        |                       sets SKIP_SOLR in mailcow.conf                       |      `n`      |             |
|        `mailcow__config_solr_heap`        |                       sets SOLR_HEAP in mailcow.conf                       |    `1024`     |             |
| `mailcow__config_allow_admin_email_login` |                sets ALLOW_ADMIN_EMAIL_LOGIN in mailcow.conf                |      `n`      |             |
|      `mailcow__config_use_watchdog`       |                     sets USE_WATCHDOG in mailcow.conf                      |      `n`      |             |
|  `mailcow__config_watchdog_notify_email`  |                 sets WATCHDOG_NOTIFY_EMAIL in mailcow.conf                 |               |             |
|   `mailcow__config_watchdog_notify_ban`   |                  sets WATCHDOG_NOTIFY_BAN in mailcow.conf                  |      `y`      |             |
|        `mailcow__config_log_lines`        |                       sets LOG_LINES in mailcow.conf                       |    `9999`     |             |
|   `mailcow__config_sogo_expire_session`   |                  sets SOGO_EXPIRE_SESSION in mailcow.conf                  |     `480`     |             |

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
