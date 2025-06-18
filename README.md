# mailcow: dockerized - Ansible role 🐮 + 🐋 = 💕

This role will setup a mailcow dockerized email server.

## Prerequisites

- Up and running Ubuntu/Debian host (other distributions not supported/tested for now)
- Docker Compose v2 is required!

## Requirements

| Requirements   | Description                            |
| -------------- | -------------------------------------- |
| docker ce      | Docker has to be installed on the host |
| docker-compose | docker-compose is needed               |

## Notes
This role will use by default the `inventory_hostname` as mailcow hostname, this means that you have to use the full qualified domain name as your inventory hostname e.g. `mail.mailcow.tld` or you set `mailcow__hostname` to the correct FQDN.

## Variables
|                   name                    |                                   purpose                                   |                    default value                    |                             note                              |
| :---------------------------------------: | :-------------------------------------------------------------------------: | :-------------------------------------------------: | :-----------------------------------------------------------: |
|           `mailcow__hostname`             |                            sets MAILCOW_HOSTNAME                            |                `inventory_hostname`                 |           needs to be an full qualified domain name           |
|          `mailcow__install_path`          |       sets the path where the mailcow-dockerized repo will be cloned        |              `/opt/mailcow-dockerized`              |                                                               |
|            `mailcow__git_repo`            |                   Get mailcow from a specific repository                    | `https://github.com/mailcow/mailcow-dockerized.git` |                                                               |
|          `mailcow__git_version`           |                   checkout a specific version of mailcow                    |                      `master`                       |                                                               |
|            `mailcow__timezone`            | used to set the timezone your mailcow runs in during the config generation  |                       not set                       |                        **must be set**                        |
|  `mailcow__docker_compose_project_name`   |        sets the docker-compose projectname to a user-defined string         |                 `mailcowdockerized`                 |                                                               |
|             `mailcow__theme`              |             set the default mailcow theme in vars.local.inc.php             |                       `lumen`                       |                                                               |
|             `mailcow__config`             |                       set mailcow.conf overrides                            |                                                     |               see [Mailcow.conf](#mailcowconf)                |
|        `mailcow__install_updates`         | if `yes` the mailcow ansible role will also update an existing installation |                        `yes`                        |                                                               |
|      `mailcow__rspamd_clamd_servers`      |                 configures the clamd server used by rspamd                  |                    `clamd:3310`                     |                                                               |
|     `mailcow__rspamd_clamd_patterns`      |    configures custom clamd rspamd patterns inside rspamd antivirus.conf     |                                                     |             needs to be a list  of name and regex             |
|        `mailcow__compose_command`         |               configures the command that is used for compose               |                  `docker compose`                   | set to `docker-compose` for the standalone version of compose |


> [!CAUTION]
> The Variable `mailcow__redirect_http_to_https` is **deprecated** but still accepted and will be removed on a later date. Please use the replacement: `mailcow__config_http_redirect` instead.

## Mailcow.conf

To set mailcow.conf variables use the `mailcow__config` variable. These variables are all read as strings and stored as-is in mailcow.conf.

Variables that are not specified will use these defaults:

```yaml
MAILCOW_HOSTNAME: "{{ mailcow__hostname }}"
TZ: "{{ mailcow__timezone }}"
HTTP_PORT: "80"
HTTP_BIND: ""
HTTPS_PORT: "443"
HTTPS_BIND: ""
ACL_ANYONE: "disallow"
MAILDIR_GC_TIME: "1440"
ADDITIONAL_SAN: ""
ADDITIONAL_SERVER_NAMES: ""
SKIP_LETS_ENCRYPT: "n"
ENABLE_SSL_SNI: "n"
SKIP_IP_CHECK: "n"
SKIP_HTTP_VERIFICATION: "n"
SKIP_CLAMD: "n"
SKIP_SOGO: "n"
ALLOW_ADMIN_EMAIL_LOGIN: "n"
USE_WATCHDOG: "n"
WATCHDOG_NOTIFY_EMAIL: ""
WATCHDOG_NOTIFY_BAN: "y"
WATCHDOG_SUBJECT: ""
WATCHDOG_EXTERNAL_CHECKS: "n"
LOG_LINES: "9999"
SOGO_EXPIRE_SESSION: "480"
COMPOSE_PROJECT_NAME: "mailcowdockerized"
ACME_CONTACT: ""
FTS_HEAP: "128"
FTS_PROCS: "1"
SKIP_FTS: "n"
HTTP_REDIRECT: "n"
```

Example:

```yaml
mailcow__config:
  HTTP_PORT: "8080"
  HTTPS_PORT: "4443"
  SKIP_LETS_ENCRYPT: "y"
```

> [!CAUTION]
> Previous versions of the role supported setting mailcow.conf variables with ansible variables starting with `mailcow__conf_`. This is deprecated and will be removed at a later date. Please use the replacement: `mailcow__conf`.

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
