pah_token_renew
===============

This role automates the update of Ansible Galaxy credentials in Ansible Automation Platform 2.7 now
that Gateway tokens are used and Private Automation Hub (PAH) API tokens no longer exist. Unlike
PAH API tokens, Gateway tokens expire after one year, by default, but some AAP users may choose to
enforce a shorter lifespan due to cybersecurity concerns. Token expiry means that your project
updates may fail to work, hence the requirement to update them.

This role will only update Galaxy credentials where the `url` component of the credential points
to the same AAP instance we are running against (as denoted by the `aap_hostname` variable).

This role will not take any action if you are using AAP 2.6 or earlier; in AAP 2.5 or 2.6, it would
invalidate the Galaxy credentials (those versions do not use Gateway tokens for Automation Hub API
access), and in AAP 2.4 or earlier it would simply fail.

Requirements
------------

To work with AAP, you must create a Job Template with a suitable Red Hat Ansible Automation
Platform credential. You can also run this on the commandline by specifying the authentication
variables noted below.


Role Variables
--------------

The variables used by this role are as follows:

| Variable name  | Type   | Required | Default | Description |
|:---------------|:-------|:--------:|:-------:|:------------|
| `aap_hostname` | String | Y        | -       | The base URL of the AAP 2.7 instance, e.g. `https://aap.my.domain` |
| `aap_username` | String | Y        | -       | The username of a user with privilege to a) update Galaxy credentials, and b) read collections from PAH |
| `aap_password` | String | Y        | -       | The password for the user specified in `aap_username` |
| `aap_validate_certs` | Boolean | N | `true`  | Whether or not to validate SSL certificates when connecting to AAP. Set to `false` if your AAP instance uses self-signed certificates. |


Dependencies
------------

This role has the following dependencies:

```
collections:
  - name: ansible.platform
    version: "">=2.7.0,<2.8.0"
  - name: ansible.controller
    version: ">=4.8.0,<4.9.0"
```


Example Playbook
----------------

```
---
- name: Renew Private Automation Hub credentials
  hosts: localhost
  gather_facts: false
  become: false
  tasks:
    - name: Invoke the pah_token_renew role
      ansible.builtin.import_role:
        name: pah_token_renew
...
```

License
-------

BSD

Author Information
------------------

Richard Stevenson
