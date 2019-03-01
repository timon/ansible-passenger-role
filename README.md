mod\_passenger: configure Phusion Passenger with apache
================================================

This role configures usage of Phusion Passenger.

It requires `geerlingguy.apache` role to execute.

When `zzet.rbenv` role is detected, `rbenv rehash` command would be issued
after gem installation (assuming system-wide rbenv install).

Role Variables
--------------

Variable                        | Description
--------------------------------|-------------
passenger\_gem\_command         | Name of `gem` script (`/usr/bin/gem`)
passenger\_gem\_versions\_rack  | `rack` gem version requirement (`~> 1.0`)
passenger\_gem\_versions\_rake  | `rake` gem version requirement (`~> 10.0`)
passenger\_version              | Version of passenger gem to install (`> 0`)
passenger\_languages            | Languages to enable, e.g `ruby,nodejs` (`ruby`)
passenger\_module\_config\_path | Path to module configuration snippet (`/etc/httpd/conf.modules.d/00-passenger.conf`)
passenger\_registry\_dir        | Path to passenger instance regsitry (`/var/run/passenger-instreg`)

Since CentOS 7+ uses feature known as 'PrivateTmp', default passenger
installation won't be able to query passenger router about its status.
To mitigate the issue, starting with version 5.0.8 passenger allows setting
instance registry directory as part of mod\_passenger configuration, with
`/var/run/passenger-instreg` being the default value (besides `/tmp`).

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
