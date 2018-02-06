# ansible-role-ssl

[![Build Status](https://travis-ci.org/CMcDonald82/ansible-role-ssl.svg?branch=master)](https://travis-ci.org/CMcDonald82/ansible-role-ssl)

Ansible role for installing and configuring SSL (using Certbot) on an Ubuntu server

## Requirements

This role requires that a domain name be setup (either purchased or setup for free through a service like [Freenom](http://www.freenom.com/en/index.html?lang=en)). This domain name will need to map to an actual IP address.

Also, this role should be run prior to the role that sets up Nginx (recommended to use this [Nginx role](https://github.com/CMcDonald82/ansible-role-nginx) to setup Nginx)

The first time we generate the certs, our webserver (Nginx) will not yet be running (since we are running this role prior to the nginx role). Therefore, we will need to use the 'standalone' mode the first time we generate certs and we can use 'webroot' after that
See the 'Standalone' and 'Webroot' sections of [this link](https://certbot.eff.org/docs/using.html#getting-certificates-and-choosing-plugins)

## Role Variables

The following variables with their default values are listed below.

```
ssl_package: certbot
```

This specifies the package and script name to use. The default values here shouldn't need to be changed unless using another CA that supports the ACME protocol.

```
ssl_root_path: /var/www/html
```

The root path of the static page to be served. This should match the root directive in the nginx serverblock file that the cert is being created for.

```
ssl_create_cert: false
```

This determines whether a cert will be created automatically (via the 'standalone' method provided by the certbot package).

```
ssl_email: me@example.com
```

The email address used in the commands that generate the cert. See [docs](https://certbot.eff.org/docs/intro.html#installation) for more info.

```
ssl_domain_name: example.com
```

The domain name of the site that the cert will be created for

```
ssl_domains: 
  - "{{ ssl_domain_name }}"
  - "www.{{ ssl_domain_name }}"
```

The actual domains that the certs will be generated for. 

NOTE: The ssl_email and ssl_domains variables should be set at the playbook level for each individual playbook that is running this role. If generating certs, the default values for the ssl_email and ssl_domains variables will not be valid and will need to be set to an actual email address (for ssl_email) and actual domain names (for ssl_domains) that map to the server that this role is being run on 

```
ssl_auto_renew_user: "{{ ansible_user }}"
ssl_auto_renew_hour: 3
ssl_auto_renew_minute: 30
```

These variables determine the user/hour/minute to run the cron job to auto-renew the certs. You should set these so that the auto-renewal is done at a low-traffic time by a non-root user.

## Example Playbook

```
- name: Setup Ubuntu server with SSL using Certbot
  hosts: all
  remote_user: "{{ remote_username }}"
  become: yes

  roles:
    - role: ssl_role
      ssl_domain_name: mywebsite.com
      ssl_email: me@mywebsite.com
      ssl_auto_renew_hour: 1
      ssl_auto_renew_minute: 0 
```

## Dependencies

None