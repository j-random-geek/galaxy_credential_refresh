# Galaxy Credential Refresh

**NOTE**: This repository is intended only for use with AAP 2.7. It will break project sync if used
on AAP 2.5 or 2.6, and it will not work at all on AAP 2.4 or older.

In Red Hat Ansible Automation Platform 2.7, the Private Automation Hub no longer has an API token
that is separate from the authentication and authorization capability of the Gateway component.
This has two effects:
1. Any Private Automation Hub API tokens from AAP 2.6 will no longer work, and
2. Gateway tokens, which have a finite lifetime, are now used instead.

Due to Gateway tokens expiring periodically (one year by default, but some companies using
AAP may desire to have a shorter lifespan for security reasons: see
https://access.redhat.com/solutions/7111986), the Galaxy credentials within the AAP platform
will need to be refreshed periodically to ensure that project updates won't fail.

This repository includes a playbook and a role designed to:
- Generate a new Gateway OAuth token with read-only scope, and 
- Apply it to any Galaxy credentials that point to the same AAP instance.

This code will not update Galaxy credentials whose `url` component points to a different system
such as Ansible Galaxy, console.redhat.com, or another AAP instance.

## Usage

To use this playbook within AAP, create a Job Template with the default Demo Inventory (or any
other inventory that contains `localhost`) and associate a Red Hat Ansible Automation Platform credential to it. Note that this Credential must have the AAP host set to the base url (i.e. `https://hostname.domain`) rather than just the hostname.

To ensure that project updates continue successfully, it may be worth creating a scheduled job that
invokes this Job Template at an interval slightly more frequent than the chosen token lifetime
setting.

The credentials used will need the ability to modify the existing Galaxy credentials. A platform
administrator account, such as `admin`, might be a good choice for this, though another account
can be used as long as it has the ability to use collections from Private Automation Hub and to
update the Galaxy credentials.

To use the playbook on the commandline, first install the required collections:

`ansible-galaxy collection install -r collections/requirements.yml`

and then run the playbook, passing in the `aap_hostname`, `aap_username`, and `aap_password`
variables (you should use a vaulted variables file for this). Optionally, also pass in
`aap_validate_certs=false` if your AAP instance's SSL certificates are self-signed, as shown below:

`ansible-playbook playbook.yml -e aap_hostname=https://aap.domain -e aap_username=admin -e aap_validate_certs=false`


