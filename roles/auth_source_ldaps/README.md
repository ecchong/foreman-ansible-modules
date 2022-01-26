theforeman.foreman.auth_source_ldaps
====================================

This role manages LDAP authentication sources, allowing users from an external source such as Active Directory or
FreeIPA to authenticate to Foreman.

Role Variables
--------------

This role supports the [Common Role Variables](https://github.com/theforeman/foreman-ansible-modules/blob/develop/README.md#common-role-variables).

Role specific variables should be documented as below:

The main data structure for this role is the list of `foreman_auth_source_ldaps`. Each `auth_source_ldap` requires the following fields:

- `name`: The name of the authentication source.

For all other fields see the `auth_source_ldap` module.

Example Playbooks
-----------------

Configure FreeIPA as an authentication source, with automatic registration:

```yaml
- hosts: localhost
  roles:
    - role: theforeman.foreman.auth_source_ldaps
      vars:
        foreman_server_url: https://foreman.example.com
        foreman_username: "admin"
        foreman_password: "changeme"
        foreman_organization: "ACME"
        foreman_auth_source_ldaps:
          - name: "Example LDAP"
            host: "ldap.example.org"
            onthefly_register: True
            account: uid=ansible,cn=sysaccounts,cn=etc,dc=example,dc=com
            account_password: secret
            base_dn: dc=example,dc=com
            groups_base: cn=groups,cn=accounts, dc=example,dc=com
            server_type: free_ipa
            attr_login: uid
            attr_firstname: givenName
            attr_lastname: sn
            attr_mail: mail
            attr_photo: jpegPhoto
            state: present
```

To instead integrate with Active Directory, only allowing users who are member of the "Domain Users" group:

```yaml
- hosts: localhost
  roles:
    - role: theforeman.foreman.auth_source_ldaps
      vars:
        foreman_server_url: https://foreman.example.com
        foreman_username: "admin"
        foreman_password: "changeme"
        foreman_organization: "ACME"
        foreman_auth_source_ldaps:
          - name: "Example AD"
            host: "ad.example.org"
            onthefly_register: True
            account: EXAMPLE\ansible
            account_password: secret
            base_dn: cn=Users,dc=example,dc=com
            groups_base: cn=Users,dc=example,dc=com
            server_type: active_directory
            attr_login: sAMAccountName
            attr_firstname: givenName
            attr_lastname: sn
            attr_mail: mail
            ldap_filter: (memberOf=CN=Domain Users,CN=Users,DC=example,DC=com)
            state: present
```