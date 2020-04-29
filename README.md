mod\_passenger: configure Phusion Passenger with apache
================================================

This role configures usage of Phusion Passenger.

It requires `geerlingguy.apache` role to execute.

For CentOS systems this role will install RPMs provided by Phusion, using the
steps outlined on [Passenger Site](https://www.phusionpassenger.com/library/install/apache/install/oss/el7/).

When `zzet.rbenv` role is detected, `rbenv rehash` command would be issued
after gem installation (assuming system-wide rbenv install).

Role Variables
--------------

These variables are used when Passenger is built from scratch. When using RPMs
provided by Phusion, configuration module paths are adjusted to match the
layout of the RPM.

Variable                        | Description
--------------------------------|-------------
apache\_service                 | Name of `apache` package in distribution (`httpd` or `apache2`)
passenger\_build\_deps          | List of packages required for passenger build to complete
passenger\_gem\_command         | Name of `gem` script (`/usr/bin/gem`)
passenger\_gem\_versions\_rack  | `rack` gem version requirement (`~> 1.0`)
passenger\_gem\_versions\_rake  | `rake` gem version requirement (`~> 10.0`)
passenger\_version              | Version of passenger gem to install (`> 0`)
passenger\_languages            | Languages to enable, e.g `ruby,nodejs` (`ruby`)
passenger\_mod\_configs\_dir    | The directory with apache's modules' configuration
passenger\_mod\_conf\_path      | Path to module configuration snippet
passenger\_mod\_load\_path      | Path to module load snippet (could be the same as `passenger_mod_conf_path`)
passenger\_registry\_dir        | Path to passenger instance registry (`/var/run/passenger-instreg`)

Since CentOS 7+ uses feature known as 'PrivateTmp', default passenger
installation won't be able to query passenger router about its status.
To mitigate the issue, starting with version 5.0.8 passenger allows setting
instance registry directory as part of mod\_passenger configuration, with
`/var/run/passenger-instreg` being the default value (besides `/tmp`).


OS Support
----------

The mod\_passenger role supports Debian 9 and CentOS 7.

Debian systems have `/etc/apache2/mods-available` which holds all modules'
configuration files and `/etc/apache2/mods-enabled` for actually enabled
modules. Additionally, module loading is separated from module configuration.

CentOS uses `/etc/httpd/conf.modules.d` folder for modules configuration
and groups everything related to a single module into one file, however,
packages provided by Phusion still have separate files for including the
module and configuring it.

This role follows the rule `when in Rome, do as the Romans do`,
and supports both styles, checking if the `passenger_mod_conf_path` variable
has the same value as the `passenger_mod_load_path` to determine
which style to use.

Example Playbook
----------------

```yml
  - hosts: servers
    roles:
       - zzet.rbenv
       - mod_passenger
    vars:
      rbenv:
        # This requires hash_behaviour=merge in [defaults] section
        # of ansible.cfg to work
        default_ruby: 2.3.1
        rubies:
          - version: 2.3.1
      passenger_gem_command: /usr/local/rbenv/shims/gem
```
