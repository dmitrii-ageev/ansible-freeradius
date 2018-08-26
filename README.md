Freeradius role for Ansible
===========================

A role for deploying and configuring [Freeradius AAA server](http://www.freeradius.org/) and extensions on Unix hosts using [Ansible](http://www.ansible.com/).

Supports
--------

- Ubuntu 16
- CentOS 7

Usage
-----

This role comes with default settings preloaded. You can override role variables in hosts/group vars, in your inventory, or in your playbook.
See the annotated defaults in `defaults/main.yml` for help in configuration. All provided variables start with `freeradius__`.
(i) Please note: this role has OS-specific variables for RedHat/Debian-based systems, these variables are declared in default/RedHat.yml and default/Debian.yml files.

These variables you might want to redefine, please check the default/main.yml for the full list:

 * freeradius__conf_dir:          main configuration directory;
 * freeradius__cert_dir:          path to the dynamic keys directory;
 * freeradius__certificate_file:  full path to a certificate file
 * freeradius__private_key_file:  full path to a cerfiticate key
 * freeradius__log_dir:           path to the log directory
 * freeradius__radacct_dir:       path to the accounting directory
 * freeradius__config:            this variable holds all freeradius configuration, it's split into several sections (hints, huntgroups, users, proxies, clients, dictionary, templates, radius_config, triggers, eap).


Example I. Let's say you need to add a few lines to a dictionary file:

```
freeradius__config.dictionary: |
#Class      Number  Attribute           Value  Type
VENDORATTR  12345   Vendor-Attribute-A  1      string
VENDORATTR  12345   Vendor-Attribute-A  2      string

# (i) dictionary it's a multiline string variable
```

Example II. Add a record to users file:
```
freeradius__config.users:
  - bob:
      conditionals:
        - Cleartext-Password := "hello"
      attributes:
        - Framed-Protocol = PPP
        - Framed-Compression = Van-Jacobson-TCP-IP

# users it's a list of users or default settings, each of these must have conditionals and attributes sections.
```

Example III. Configure a templates section.
This and all other sections have the same structure, the variable can be a list (if you need an ordered sequence of settings) or it could be a dictionary.
Subsections also can be lists or dictionaries. String variables will be added to a configuration file 'as is'.
```
freeradius__config.templates:
  - "# Comment: since it's a list all variables will be put in a file in the same order."
  - home_server:
      "# This subsection is a dictionary, the order will no be preserved.": ''
      response_window:               20
      zombie_period:                 40
      revive_interval:               120

  - example_com:
      type:                          auth
      port:                          1812
      secret:                        testing123
      response_window:               20

  - snmptrap: "${snmp.trap.cmd} -c ${snmp.trap.community} ${snmp.trap.agent} FREERADIUS-NOTIFICATION-MIB"

  - snmp:
      trap:
        cmd:                         "/usr/bin/snmptrap -v2c"
        community:                   "public"
        agent:                       "localhost ''"

```


License
-------

GPLv2

Author Information
------------------

Dmitrii Ageev <d.ageev@gmail.com>

