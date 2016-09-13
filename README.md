rfc2307bis
==========

This repo contains LDAP schema of the official [IETF](http://ietf.org/) draft
for the Network Information Service (NIS) called
[rfc2307bis](http://tools.ietf.org/html/draft-howard-rfc2307bis-02). It is
trying to map entities related to TCP/IP and the UNIX system so that they may
be resolved with LDAP. Comparing to the original
[NIS](http://tools.ietf.org/html/rfc2307) schema, rfc2307bis allows to create
`posixGroup`
[with no members](http://tools.ietf.org/html/draft-findlay-ldap-groupofentries-00).

The schema was slightly modified in order to work with OpenLDAP:

- The `uidNumber` and `gidNumber` was commented out as they are hardcoded in
  the OpenLDAP.
- The `authPassword` attribute was removed from all attribute types as the RFC
  3112 is not currently implemented.
- The `SUBSTRING` keyword was renamed to `SUBSTR`.


Usage
-----

Copy the `rfc2307bis.schema` file into the `/etc/openldap/schema/` directory on
your OpenLDAP server.

Load the schema in the `slapd.conf`:

```
include /etc/openldap/schema/rfc2307bis.schema
```

Let's create basic DB structure:

```
$ ldapadd -W -H ldap:/// -D 'cn=Manager,dc=example,dc=com' <<LDIF
# Create a new organization
dn: dc=example,dc=com
objectClass: dcObject
objectClass: organization
dc: example
o: My Organization
description: The Example organization

# Create role for the directory manager
dn: cn=Manager,dc=example,dc=com
objectClass: organizationalRole
cn: Manager
description: Directory Manager

# Create unit for groups
dn: ou=groups,dc=example,dc=com
objectClass: top
objectClass: organizationalUnit
ou: groups
description: Organizational unit for user groups

# Create unit for people
dn: ou=people,dc=example,dc=com
objectClass: top
objectClass: organizationalUnit
ou: people
description: Organizational unit for user accounts

# Create a test user in the people unit
dn: uid=test,ou=people,dc=example,dc=com
objectClass: top
objectClass: person
objectClass: posixAccount
uid: test
cn: Test User
sn: Test
uidNumber: 10001
gidNumber: 100
homeDirectory: /home/test
loginShell: /bin/bash
LDIF
```

Now we can create an empty group (group with no members):

```
$ ldapadd -W -H ldap:/// -D 'cn=Manager,dc=example,dc=com' <<LDIF
# Creating ops group in the groups unit
dn: cn=ops,ou=groups,dc=example,dc=com
objectClass: top
objectClass: posixGroup
objectClass: groupOfMembers
cn: ops
gidNumber: 10001
description: Operations Group
LDIF
```

Then we can add a new `member` attribute into the empty group:

```
$ ldapadd -W -H ldap:/// -D 'cn=Manager,dc=example,dc=com' <<LDIF
# Setting the test user as a member of the ops group
dn: cn=ops,ou=groups,dc=example,dc=com
changetype: modify
add: member
member: uid=test,ou=people,dc=example,dc=com
LDIF
```


Resources
---------

- [An Approach for Using LDAP as a Network Information Service](http://tools.ietf.org/html/draft-howard-rfc2307bis-02)
- [Original NIS](https://tools.ietf.org/html/rfc2307)
- [LDAP Authentication Password Schema](https://tools.ietf.org/html/rfc3112)
- [The LDAP groupOfEntries object class](http://tools.ietf.org/html/draft-findlay-ldap-groupofentries-00)


Author
------

Jiri Tyr


License
-------

MIT
