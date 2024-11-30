# murphywants-ansible-role-application-nextcloud

## Description & Purpose

```
Nextcloud:
Image: 'lscr.io/linuxserver/nextcloud'
tag: latest # unless otherwise specificed in the variable NEXTCLOUD_CONTAINER_VERSION

Postgres Database:
Image: 'https://hub.docker.com/_/postgres'
tag: latest # unless otherwise specificed in the variable NEXTCLOUD_POSTGRES_CONTAINER_VERSION

Nextcloud Whiteboard:
Image: 'ghcr.io/nextcloud-releases/whiteboard:release'
tag: release # unless otherwise specificed in the variable
```

This role will standup and configure three applications:
- Nextcloud: An application as a hub for other applications, file shares, calendars, contacts and more
- Postgreqsl: Backend database for nextcloud
- Nextcloud Whiteboard: An addon collaborative whiteboard for nextcloud (not working but soon)

This role is configured to work with Ubuntu with a ZFS subvolume. 

## How to Use

Example Inventory:

yaml form:
```
group:
  hosts:
    hostname.fdqn
  vars: 
    NEXTCLOUD_STORAGE_ZFS_POOL: "pool" # Required, change this to your zfs pool, this role will create a subvolume for nextcloud
    NEXTCLOUD_FQDN: "nextcloud.domain.com" # Required, change this to your domain for nextcloud
    NEXTCLOUD_SSL_CERT_PATH: /path/to/cert # Required, change this to the path for your ssl cert
    # If you are setting this up using the dehydrated cloudflare hook, it may be "/etc/dehydrated/certs/NEXTCLOUD_FQDN/cert.pem" where NEXTCLOUD_FQDN is what was entered above
    NEXTCLOUD_SSL_KEY_PATH: /path/to/key # Required, change this to the path for your SSL key
    # If you are setting this up using the dehydrated cloudflare hook, it may be "/etc/dehydrated/certs/NEXTCLOUD_FQDN/privkey.pem" where NEXTCLOUD_FQDN is what was entered above
    NEXTCLOUD_APP_WHITEBOARD_FQDN: "nextcloud-wb.domain.com" # Required, change this to your domain for nextcloud whiteboard
    NEXTCLOUD_APP_WHITEBOARD_SSL_CERT_PATH: /path/to/cert # Required, change this to the path for your ssl cert
    # If you are setting this up using the dehydrated cloudflare hook, it may be "/etc/dehydrated/certs/NEXTCLOUD_APP_WHITEBOARD_FQDN/cert.pem" where NEXTCLOUD_APP_WHITEBOARD_FQDN is what was entered above
    NEXTCLOUD_APP_WHITEBOARD_SSL_KEY_PATH: /path/to/key # Required, change this to the path for your SSL key
    # If you are setting this up using the dehydrated cloudflare hook, it may be "/etc/dehydrated/certs/NEXTCLOUD_APP_WHITEBOARD_FQDN/privkey.pem" where NEXTCLOUD_APP_WHITEBOARD_FQDN is what was entered above

    NEXTCLOUD_POSTGRES_ENV_POSTGRES_PASSWORD: "EnterANewPassword" # Optionally but strongly recommended
    NEXTCLOUD_DEFAULT_ADMIN_ACCOUNT_USER: "nextcloudAdmin" # Optional, change the default nextcloud admin account name, only created on first run
    NEXTCLOUD_DEFAULT_ADMIN_ACCOUNT_PASSWORD: "Password" # Optional, but strongly recommended, change the default nextcloud admin account password, only created on first run
```

Example Playbook:

```
- hosts: all 
  gather_facts: yes
  become: yes
  roles:
  - role: ansible-role
```

## Requirements/Dependencies
Uses the following modules:
- Default builtin modules
- community.general
-  community.docker

Uses the following roles:
- [murphywants-ansible-role-component-docker](https://github.com/MurphyWants/murphywants-ansible-role-component-docker): A role to install and configure the docker daemon
- [murphywants-ansible-role-component-dehydrated](https://github.com/MurphyWants/murphywants-ansible-role-component-dehydrated): A role to setup dehydrated with the cloudflare hook to pull lets encrypt certificates via the DNS api

## Tags
Ansible role template with the following actions & tags:

Tag | Description
--- | ---
Setup/Baseline | Setup the application and apply the configuration baseline
Start | Start required services
Stop | Stop required services
Backup | (TODO) Run the backup for the component or application or OS
Update | Will restart the apps, pull new containers, update nextcloud apps
Remove | (TODO) Remove configurations and applications
Purge | (TODO) Remove all configurations and applications relating to the app/component

## Variables
Variable | Default Value | Description
---|---|---
NEXTCLOUD_PUID | 10003 | [UID that the container files are owned by](https://docs.linuxserver.io/general/understanding-puid-and-pgid/)
NEXTCLOUD_GUID | 10003 | [GID that the container files are owned by](https://docs.linuxserver.io/general/understanding-puid-and-pgid/)
NEXTCLOUD_PATH | "/mnt/nextcloud" | Where the application will be created and files will be stored
NEXTCLOUD_STORAGE_ZFS_POOL | 'apps_pool' | The host's ZFS pool name
NEXTCLOUD_STORAGE_ZFS_FS | 'nextcloud' | The ZFS zubvolume that will be created
NEXTCLOUD_TIMEZONE | "America/New_York" | Timezone for the container
NEXTCLOUD_CONTAINER_VERSION | 'latest' | Nextcloud container tag
NEXTCLOUD_POSTGRES_CONTAINER_VERSION | '15' |  Postgres container version
NEXTCLOUD_HTTPS_PORT | 8084 | Nextcloud HTTP port mapped to the host
NEXTCLOUD_FQDN | nextcloud.local | Domain name for nextcloud to run as
NEXTCLOUD_POSTGRES_ENV_POSTGRES_PASSWORD | "ChangeThisPassword" # https://hub.docker.com/_/postgres Mandatory env var POSTGRES_PASSWORD | See above, change this to something very very secret
NEXTCLOUD_POSTGRES_ENV_POSTGRES_USER | "postgresUser" | Postgres username for database communications
NEXTCLOUD_POSTGRES_ENV_POSTGRES_DB | "postgresDatabase" | Postgres password for database communications
NEXTCLOUD_DEFAULT_ADMIN_ACCOUNT_USER | "nextcloudAdmin" | See above, a default nextcloud admin account is created on initialization
NEXTCLOUD_DEFAULT_ADMIN_ACCOUNT_PASSWORD | "ChangeThisPassword" | See above, a default nextcloud admin account is created on initialization
NEXTCLOUD_SSL_CERT_PATH | undefined | Path to the SSL cert for the nextcloud domain, required by nginx
NEXTCLOUD_SSL_KEY_PATH | undefined | Path to the SSL key for the nextcloud domain, required by nginx
NEXTCLOUD_NETWORK_CIDR | "10.254.0.1/24" | Going to remove at some point, nextcloud docker compose creates its own network for the containers it needs. This is the network space it will use.
NEXTCLOUD_MAINTENENCE_WINDOW_START | 1 | Nextcloud maintennce window for automated maintenance tasks. 1 means 1 am. ["A value of 1 e.g. will only run these background jobs between 01:00am UTC and 05:00am UTC"](https://docs.nextcloud.com/server/28/admin_manual/configuration_server/background_jobs_configuration.html)
NEXTCLOUD_APPS_INSTALL | A long list, see defaults/main.yml | A list of nextcloud applications to install
NEXTCLOUD_APPS_ENABLE | A long list, see defaults/main.yml | A list of nextcloud applications to enable, they must also be in the install list to ensure they are installed
NEXTCLOUD_APPS_DISABLE | Empty list | A list of nextcloud applications to disable
NEXTCLOUD_APPS_REMOVE | Empty List | A list of nextcloud applications to remove
NEXTCLOUD_APP_WHITEBOARD_JWT_SECRET_KEY |  "ChangeThisJWTSecret" | Nextcloud whiteboard requires a shared secret between the two apps
NEXTCLOUD_APP_WHITEBOARD_FQDN |  nextcloud-whiteboard.local | Domain name for the nextcloud whiteboard app
NEXTCLOUD_APP_WHITEBOARD_PORT |  8099 | Nextcloud Whiteboard HTTP port mapped to the host
NEXTCLOUD_APP_WHITEBOARD_SSL_CERT_PATH |  undefined | Path to the SSL cert for the nextcloud whiteboard domain, required by nginx
NEXTCLOUD_APP_WHITEBOARD_SSL_KEY_PATH |  undefined | Path to the SSL key for the nextcloud whiteboard domain, required by nginx

# TODO List
- Create backups