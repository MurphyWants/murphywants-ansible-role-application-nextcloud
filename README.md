# ansible-role-template

## Description & Purpose

```
Image: ''
tag: latest # unless otherwise specificed in the variables
```

This role will:

## How to Use

Example Inventory:

yaml form:
```
group:
  hosts:
    hostname.fdqn
  vars:
    EXAMPLE_VARIABLE: EXAMPLE_VALUE # This variable is required to run
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

Uses the following roles:

This role uses OOTB ansible modules. 


## Tags
Ansible role template with the following actions & tags:

Tag | Description
--- | ---
Setup/Baseline | Setup the application and apply the configuration baseline
Start | Start required services
Stop | Stop required services
Backup | Run the backup for the component or application or OS
Update | Update the component applications
Remove | Remove configurations and applications
Purge | Remove all configurations and applications relating to the app/component

## Variables
Variable | Default Value | Description
---|---|---

# TODO List
