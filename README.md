---
comments: true
feedback: true
description: 'Learn how to use and configure the Apache2 role.'
repositories:
  - domain=Ansible Role uWSGi, repository=https://github.com/lordoftheflies/apache2
  - domain=Ansible Role uWSGi, repository=https://gitlab.cherubits.hu/oss/ansible-galaxy-roles/ansible-role-apache2
  - domain=Ansible Role uWSGi, repository=https://gitlab.com/lordoftheflies/ansible-role-apache2
---

[![Build Status](https://travis-ci.org/lordoftheflies/ansible-apache2-role.svg?branch=master)](https://travis-ci.org/lordoftheflies/ansible-apache2-role)

# Ansible Galaxy Role: Apache 2.x

An Ansible Role that installs Apache 2.x on RHEL/CentOS, Debian/Ubuntu, SLES and Solaris.

[[_TOC_]]

## Requirements

If you are using SSL/TLS, you will need to provide your own certificate and key files. You can generate a self-signed certificate with a command like 

```yml
openssl req -x509 -nodes -days 365 -newkey rsa:2048 -keyout example.key -out example.crt
```

## Role Variables

Available variables are listed below, along with default values (see `defaults/main.yml`):

<p>

<details>

<summary>Enable EPEL packages</summary>

### Enable EPEL packages

The repository to use when installing Apache (only used on RHEL/CentOS systems). If you'd like later versions of Apache than are available in the OS's core repositories, use a repository like EPEL (which can be installed with the `lordoftheflies.repo-epel` role).

<pre>
    <code>apache_enablerepo: ""</code>
</pre>

</details>
</p>

<p>

<details>

<summary>Listening address</summary>

### Listening address

The IP address and ports on which apache should be listening. Useful if you have another service (like a reverse proxy) listening on port 80 or 443 and need to change the defaults.

```yml
    apache_listen_ip: "*"
    apache_listen_port: 80
    apache_listen_port_ssl: 443
```

</details>

</p>

<p>

<details>

<summary>Virtual host settings</summary>

### Virtual host settings

If set to true, a vhosts file, managed by this role's variables (see below), will be created and placed in the Apache configuration folder. If set to false, you can place your own vhosts file into Apache's configuration folder and skip the convenient (but more basic) one added by this role. You can also override the template used and set a path to your own template, if you need to further customize the layout of your VirtualHosts.

```yml
    apache_create_vhosts: true
    apache_vhosts_filename: "vhosts.conf"
    apache_vhosts_template: "vhosts.conf.j2"
```

</details>

</p>


<p>

<details>

<summary>Remove virtualhost</summary>

### Remove virtualhost

On Debian/Ubuntu, a default virtualhost is included in Apache's configuration. Set this to `true` to remove that default virtualhost configuration file.

```yml
    apache_remove_default_vhost: false
```
</details>

</p>

<p>

<details>

<summary>Global virtualhost settings</summary>

### Global virtualhost settings

You can add or override global Apache configuration settings in the role-provided vhosts file (assuming `apache_create_vhosts` is true) using this variable. By default it only sets the DirectoryIndex configuration.

```yml
    apache_global_vhost_settings: |
      DirectoryIndex index.php index.html
      # Add other global settings on subsequent lines.
```
</details>

</p>

<p>

<details>

<summary>Additional virtualhost settings</summary>

### Additional virtualhost settings

Add a set of properties per virtualhost, including `servername` (required), `documentroot` (required), `allow_override` (optional: defaults to the value of `apache_allow_override`), `options` (optional: defaults to the value of `apache_options`), `serveradmin` (optional), `serveralias` (optional) and `extra_parameters` (optional: you can add whatever additional configuration lines you'd like in here).

```yml
    apache_vhosts:
      # Additional optional properties: 'serveradmin, serveralias, extra_parameters'.
      - servername: "local.dev"
        documentroot: "/var/www/html"
```

Here's an example using `extra_parameters` to add a RewriteRule to redirect all requests to the `www.` site:

```yml
      - servername: "www.local.dev"
        serveralias: "local.dev"
        documentroot: "/var/www/html"
        extra_parameters: |
          RewriteCond %{HTTP_HOST} !^www\. [NC]
          RewriteRule ^(.*)$ http://www.%{HTTP_HOST}%{REQUEST_URI} [R=301,L]
```

</details>

</p>

<p>

<details>

<summary>SSL settings</summary>

### SSL settings

The `|` denotes a multiline scalar block in YAML, so newlines are preserved in the resulting configuration file output.

```yml
    apache_vhosts_ssl: []
```

No SSL vhosts are configured by default, but you can add them using the same pattern as `apache_vhosts`, with a few additional directives, like the following example:

```yml
    apache_vhosts_ssl:
      - servername: "local.dev"
        documentroot: "/var/www/html"
        certificate_file: "/home/vagrant/example.crt"
        certificate_key_file: "/home/vagrant/example.key"
        certificate_chain_file: "/path/to/certificate_chain.crt"
        extra_parameters: |
          RewriteCond %{HTTP_HOST} !^www\. [NC]
          RewriteRule ^(.*)$ http://www.%{HTTP_HOST}%{REQUEST_URI} [R=301,L]
```

Other SSL directives can be managed with other SSL-related role variables.

```yml
    apache_ssl_protocol: "All -SSLv2 -SSLv3"
    apache_ssl_cipher_suite: "AES256+EECDH:AES256+EDH"
```

The SSL protocols and cipher suites that are used/allowed when clients make secure connections to your server. These are secure/sane defaults, but for maximum security, performand, and/or compatibility, you may need to adjust these settings.

```yml
    apache_allow_override: "All"
    apache_options: "-Indexes +FollowSymLinks"
```

The default values for the `AllowOverride` and `Options` directives for the `documentroot` directory of each vhost.  A vhost can overwrite these values by specifying `allow_override` or `options`.

```yml
    apache_mods_enabled:
      - rewrite.load
      - ssl.load
    apache_mods_disabled: []
```

(Debian/Ubuntu ONLY) Which Apache mods to enable or disable (these will be symlinked into the appropriate location). See the `mods-available` directory inside the apache configuration directory (`/etc/apache2/mods-available` by default) for all the available mods.

</details>

</p>

<p>

<details>

<summary>Platform specific variables</summary>

### Platform specific variables

The list of packages to be installed. This defaults to a set of platform-specific packages for RedHat or Debian-based systems (see `vars/RedHat.yml` and `vars/Debian.yml` for the default values).

```yml
    apache_packages:
      - [platform-specific]
```

</details>

</p>

<p>

<details>

<summary>Platform specific variables</summary>

### Platform specific variables

Set initial Apache daemon state to be enforced when this role is run. This should generally remain `started`, but you can set it to `stopped` if you need to fix the Apache config during a playbook run or otherwise would not like Apache started at the time this role is run.

```yml
    apache_state: started
```

</details>

</p>

<p>

<details>

<summary>Apache packages</summary>

### Apache packages

If you have enabled any additional repositories such as _ondrej/apache2_, [geerlingguy.repo-epel](https://github.com/geerlingguy/ansible-role-repo-epel), or [geerlingguy.repo-remi](https://github.com/geerlingguy/ansible-role-repo-remi), you may want an easy way to upgrade versions. You can set this to `latest` (combined with `apache_enablerepo` on RHEL) and can directly upgrade to a different Apache version from a different repo (instead of uninstalling and reinstalling Apache).

```yml
    apache_packages_state: present
```

</details>

</p>

<p>

<details>

<summary>Ignore missing SSL</summary>

### Ignore missing SSL

If you would like to only create SSL vhosts when the vhost certificate is present (e.g. when using Let’s Encrypt), set `apache_ignore_missing_ssl_certificate` to `false`. When doing this, you might need to run your playbook more than once so all the vhosts are configured (if another part of the playbook generates the SSL certificates).

```yml
    apache_ignore_missing_ssl_certificate: true
```

</details>

</p>

## .htaccess-based Basic Authorization

If you require Basic Auth support, you can add it either through a custom template, or by adding `extra_parameters` to a VirtualHost configuration, like so:

```yml
    extra_parameters: |
      <Directory "/var/www/password-protected-directory">
        Require valid-user
        AuthType Basic
        AuthName "Please authenticate"
        AuthUserFile /var/www/password-protected-directory/.htpasswd
      </Directory>
```

To password protect everything within a VirtualHost directive, use the `Location` block instead of `Directory`:

```yml
    <Location "/">
      Require valid-user
      ....
    </Location>
```

You would need to generate/upload your own `.htpasswd` file in your own playbook. There may be other roles that support this functionality in a more integrated way.

## Dependencies

None.

## Usage

As a spearate role:

```yml
    - hosts: webservers
      vars_files:
        - vars/main.yml
      roles:
        - { role: geerlingguy.apache }
```

Inside `vars/main.yml`:

```yml
    apache_listen_port: 8080
    apache_vhosts:
      - {servername: "example.com", documentroot: "/var/www/vhosts/example_com"}
```

## Author Information

* This role was created in 2014 by [Jeff Geerling](https://www.jeffgeerling.com/), author of [Ansible for DevOps](https://www.ansiblefordevops.com/).
* Role upgraded in 2019 by [László Hegedűs](mailto:laszlo.hegedus@cherubits.hu), founder of [Cherubits LLC](https://portal.cherubits.hu)

## Roadmap

* Support for Windows
* Support for MacOS
* Extend to ```httpd``` and finalize RedHat platform.
* Automatic certificate generation from cacert.
