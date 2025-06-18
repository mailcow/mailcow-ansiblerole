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
|          `mailcow__config_keep`           | These values are kept in mailcow.conf even if they are not specified in ansible | [DBNAME, DBUSER, DBPASS, DBROOT, REDISPASS, IPV4_NETWORK, IPV6_NETWORK]     |               see [Mailcow.conf](#mailcowconf)                |
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
ACL_ANYONE: "disallow"
ACME_CONTACT: ""
ADDITIONAL_SAN: ""
ADDITIONAL_SERVER_NAMES: ""
ALLOW_ADMIN_EMAIL_LOGIN: "n"
AUTODISCOVER_SAN: "y"
COMPOSE_PROJECT_NAME: "{{ mailcow__docker_compose_project_name }}"
DISABLE_NETFILTER_ISOLATION_RULE: "n"
DOCKER_COMPOSE_VERSION: "native"
DOVEADM_PORT: "127.0.0.1:19991"
DOVECOT_MASTER_PASS: ""
DOVECOT_MASTER_USER: ""
ENABLE_SSL_SNI: "n"
FTS_HEAP: "128"
FTS_PROCS: "1"
HTTP_BIND: ""
HTTP_PORT: "80"
HTTP_REDIRECT: "n"
HTTPS_BIND: ""
HTTPS_PORT: "443"
IMAP_PORT: "143"
IMAPS_PORT: "993"
LOG_LINES: "9999"
MAILCOW_HOSTNAME: "{{ mailcow__hostname }}"
MAILCOW_PASS_SCHEME: "BLF-CRYPT"
MAILDIR_GC_TIME: "1440"
MAILDIR_SUB: "Maildir"
POP_PORT: "110"
POPS_PORT: "995"
REDIS_PORT: "127.0.0.1:7654"
SIEVE_PORT: "4190"
SKIP_CLAMD: "n"
SKIP_FTS: "n"
SKIP_HTTP_VERIFICATION: "n"
SKIP_IP_CHECK: "n"
SKIP_LETS_ENCRYPT: "n"
SKIP_OLEFY: "n"
SKIP_SOGO: "n"
SKIP_UNBOUND_HEALTHCHECK: "n"
SMTP_PORT: "25"
SMTPS_PORT: "465"
SOGO_EXPIRE_SESSION: "480"
SPAMHAUS_DQS_KEY: ""
SQL_PORT: "127.0.0.1:13306"
SUBMISSION_PORT: "587"
TZ: "{{ mailcow__timezone }}"
USE_WATCHDOG: "n"
WATCHDOG_EXTERNAL_CHECKS: "n"
WATCHDOG_NOTIFY_BAN: "y"
WATCHDOG_NOTIFY_EMAIL: ""
WATCHDOG_NOTIFY_START: "y"
WATCHDOG_SUBJECT: ""
WATCHDOG_VERBOSE: "n"
WEBAUTHN_ONLY_TRUSTED_VENDORS: "n"
```

Any other variables that are not in `mailcow__config_keep` either will be commented out.

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
