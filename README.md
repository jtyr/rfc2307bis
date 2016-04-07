rfc2307bis
==========

This repo contains LDAP schema of the official [IETF](http://ietf.org/) draft
for the Network Information Service (NIS) called
[rfc2307bis](http://tools.ietf.org/html/draft-howard-rfc2307bis-02). It is
trying to map entities related to TCP/IP and the UNIX system so that they may
be resolved with LDAP. Comparing to the original
[NIS](http://tools.ietf.org/html/rfc2307) schema, rfc2307bis allows to create
`posixGroup`
[without members](http://tools.ietf.org/html/draft-findlay-ldap-groupofentries-00).

The schema was slightly modified in order to work with OpenLDAP:

- The `uidNumber` and `gidNumber` was commented out as they are hardcoded in
  the OpenLDAP.
- The `authPassword` attribute was removed from all attribute types as the RFC
  3112 is not currently implemented.
- The `SUBSTRING` keyword was renamed to `SUBSTR`.
- The `namedObject` object class was added to support the creation of empty
  groups.


Usage
-----

Copy the `rfc2307bis.schema` file into the `/etc/openldap/schema/` directory on
your OpenLDAP server.

Load the schema in the `slapd.conf`:

```
include /etc/openldap/schema/rfc2307bis.schema
```

Then it's possible to define an empty group:

```
dn: cn=mygroup,ou=Groups,dc=example,dc=com
cn: mygroup
objectClass: top
objectClass: namedObject
objectClass: posixGroup
gidNumber: 1001
description: My Group
```


Resources
---------

- [An Approach for Using LDAP as a Network Information Service](http://tools.ietf.org/html/draft-howard-rfc2307bis-02)
- [Original NIS](https://tools.ietf.org/html/rfc2307)
- [The LDAP groupOfEntries object class](http://tools.ietf.org/html/draft-findlay-ldap-groupofentries-00)


Author
------

Jiri Tyr


License
-------

MIT
