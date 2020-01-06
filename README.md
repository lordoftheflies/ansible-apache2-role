[![Build Status](https://travis-ci.org/lordoftheflies/ansible-apache2-role.svg?branch=master)](https://travis-ci.org/lordoftheflies/ansible-apache2-role)

# Ansible Galaxy Role: Apache 2.x

An Ansible Role that installs Apache 2.x on RHEL/CentOS, Debian/Ubuntu, SLES and Solaris.

----

## On this page
{:.no_toc}

- TOC
{:toc}

----

## Requirements

If you are using SSL/TLS, you will need to provide your own certificate and key files. You can generate a self-signed certificate with a command like 

```yml
openssl req -x509 -nodes -days 365 -newkey rsa:2048 -keyout example.key -out example.crt
```

<script src="https://gitlab.com/gitlab-org/gitlab-ce/snippets/1717978.js"></script>

<div class="video-fallback">
  See the video: <a href="https://www.youtube.com/watch?v=MqL6BMOySIQ">Video title</a>.
</div>
<figure class="video-container">
  <iframe src="https://www.youtube.com/embed/MqL6BMOySIQ" frameborder="0" allowfullscreen="true"> </iframe>
</figure>

<figure class="video_container">
<iframe IFRAME CONTENT></iframe>
</figure>

<figure class="video_container">
<iframe src="https://docs.google.com/spreadsheets/d/1jAnvYpRmNu8BISIrkYGTLolOTmlCoKLbuHVWzCXJSY4/pubhtml?widget=true&amp;headers=false"></iframe>
</figure>

<figure class="video_container">
<iframe src="https://docs.google.com/presentation/d/16FZd01-zCj_1jApDQI7TzQMFnW2EGwtQoRuX82dkOVs/embed?start=false&loop=false&delayms=3000" frameborder="0" width="1280" height="749" allowfullscreen="true" mozallowfullscreen="true" webkitallowfullscreen="true"></iframe>
</figure>

<figure class="video_container">
<iframe src="https://docs.google.com/document/d/1mHhOhvvrz7xgUPyn5VWCNuKgew5MRRGZp761B9prPqs/pub?embedded=true"></iframe>
</figure>

<figure>
<iframe src="//www.slideshare.net/slideshow/embed_code/key/EixD8OUMBX65Jy" width="595" height="485" frameborder="0" marginwidth="0" marginheight="0" scrolling="no" style="border:1px solid #CCC; border-width:1px; margin-bottom:5px; max-width: 100%;" allowfullscreen> </iframe>
</figure>

TIP: **Tip:**
This is a tip.

CAUTION: **Caution:**
This is something to be cautious about.

DANGER: **Danger:**
This is a breaking change, a bug, or something very important to note.

> This is a blockquote.


> This is the first paragraph.
>
> This is the second paragraph.
>
> - This is a list item
> - Second item in the list

curl --header "PRIVATE-TOKEN: zPL1wTepjFKk_aGxW4jc" https://gitlab.cherubits.hu/api/v4/groups/oss

### <i class="fab fa-gitlab fa-fw" style="color:rgb(107,79,187); font-size:.85em" aria-hidden="true"></i> Purple GitLab Tanuki
{: #tanuki-purple}

NOTE: **Note:** 
If you are using Apache with PHP, I recommend using the `geerlingguy.php` role to install PHP, and you
 can either
 use mod_php (by adding the proper package, e.g. `libapache2-mod-php5` for Ubuntu, to `php_packages`), or by also
  using `geerlingguy.apache-php-fpm` to connect Apache to PHP via FPM. See that role's README for more info. 
{: .note .alert .alert-info .text-center}

{::options parse_block_html="true" /}

<i class="fab fa-gitlab" style="color:rgb(107,79,187); font-size:.85em" aria-hidden="true"></i>&nbsp;&nbsp;
The webcast I want to announce - [Register here][webcast-link]!
&nbsp;&nbsp;<i class="fab fa-gitlab" style="color:rgb(107,79,187); font-size:.85em" aria-hidden="true"></i>
{: .alert .alert-webcast}

My text in an purple box.
{: .alert .alert-gitlab-purple}


{::options parse_block_html="true" /}

<p>assddsdsfsf</p>

{::options parse_block_html="false" /}


## Role Variables

Available variables are listed below, along with default values (see `defaults/main.yml`):

```yml
apache_enablerepo: ""
```

The repository to use when installing Apache (only used on RHEL/CentOS systems). If you'd like later versions of Apache than are available in the OS's core repositories, use a repository like EPEL (which can be installed with the `geerlingguy.repo-epel` role).

```yml
    apache_listen_ip: "*"
    apache_listen_port: 80
    apache_listen_port_ssl: 443
```

The IP address and ports on which apache should be listening. Useful if you have another service (like a reverse proxy) listening on port 80 or 443 and need to change the defaults.

```yml
    apache_create_vhosts: true
    apache_vhosts_filename: "vhosts.conf"
    apache_vhosts_template: "vhosts.conf.j2"
```

If set to true, a vhosts file, managed by this role's variables (see below), will be created and placed in the Apache configuration folder. If set to false, you can place your own vhosts file into Apache's configuration folder and skip the convenient (but more basic) one added by this role. You can also override the template used and set a path to your own template, if you need to further customize the layout of your VirtualHosts.

```yml
    apache_remove_default_vhost: false
```

On Debian/Ubuntu, a default virtualhost is included in Apache's configuration. Set this to `true` to remove that default virtualhost configuration file.

```yml
    apache_global_vhost_settings: |
      DirectoryIndex index.php index.html
      # Add other global settings on subsequent lines.
```

You can add or override global Apache configuration settings in the role-provided vhosts file (assuming `apache_create_vhosts` is true) using this variable. By default it only sets the DirectoryIndex configuration.

```yml
    apache_vhosts:
      # Additional optional properties: 'serveradmin, serveralias, extra_parameters'.
      - servername: "local.dev"
        documentroot: "/var/www/html"
```

Add a set of properties per virtualhost, including `servername` (required), `documentroot` (required), `allow_override` (optional: defaults to the value of `apache_allow_override`), `options` (optional: defaults to the value of `apache_options`), `serveradmin` (optional), `serveralias` (optional) and `extra_parameters` (optional: you can add whatever additional configuration lines you'd like in here).

Here's an example using `extra_parameters` to add a RewriteRule to redirect all requests to the `www.` site:

```yml
      - servername: "www.local.dev"
        serveralias: "local.dev"
        documentroot: "/var/www/html"
        extra_parameters: |
          RewriteCond %{HTTP_HOST} !^www\. [NC]
          RewriteRule ^(.*)$ http://www.%{HTTP_HOST}%{REQUEST_URI} [R=301,L]
```

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

```yml
    apache_packages:
      - [platform-specific]
```

The list of packages to be installed. This defaults to a set of platform-specific packages for RedHat or Debian-based systems (see `vars/RedHat.yml` and `vars/Debian.yml` for the default values).

```yml
    apache_state: started
```

Set initial Apache daemon state to be enforced when this role is run. This should generally remain `started`, but you can set it to `stopped` if you need to fix the Apache config during a playbook run or otherwise would not like Apache started at the time this role is run.

```yml
    apache_packages_state: present
```

If you have enabled any additional repositories such as _ondrej/apache2_, [geerlingguy.repo-epel](https://github.com/geerlingguy/ansible-role-repo-epel), or [geerlingguy.repo-remi](https://github.com/geerlingguy/ansible-role-repo-remi), you may want an easy way to upgrade versions. You can set this to `latest` (combined with `apache_enablerepo` on RHEL) and can directly upgrade to a different Apache version from a different repo (instead of uninstalling and reinstalling Apache).

```yml
    apache_ignore_missing_ssl_certificate: true
```

If you would like to only create SSL vhosts when the vhost certificate is present (e.g. when using Let’s Encrypt), set `apache_ignore_missing_ssl_certificate` to `false`. When doing this, you might need to run your playbook more than once so all the vhosts are configured (if another part of the playbook generates the SSL certificates).

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

## Example Playbook

```yml
    - hosts: webservers
      vars_files:
        - vars/main.yml
      roles:
        - { role: geerlingguy.apache }
```

Inside `vars/main.yml`:

    apache_listen_port: 8080
    apache_vhosts:
      - {servername: "example.com", documentroot: "/var/www/vhosts/example_com"}

## License

MIT / BSD

## Author Information

This role was created in 2014 by [Jeff Geerling](https://www.jeffgeerling.com/), author of [Ansible for DevOps](https://www.ansiblefordevops.com/).
Role upgraded in 2019 by [László Hegedűs](mailto:laszlo.hegedus@cherubits.hu), founder of [Cherubits LLC](https://www.cherubits.hu)


<!--Follow the Style Guide when working on this document. https://docs.gitlab.com/ee/development/documentation/styleguide.html
When done, remove all of this commented-out text, except a commented-out Troubleshooting section,
which, if empty, can be left in place to encourage future use.-->
---
description: "Short document description." # Up to ~200 chars long. They will be displayed in Google Search snippets. It may help to write the page intro first, and then reuse it here.
---

# Feature Name or Use Case Name **[TIER]** (1)
<!--If writing about a use case, drop the tier, and start with a verb, e.g. 'Configuring', 'Implementing', + the goal/scenario-->

<!--For pages on newly introduced features, add the following line. If only some aspects of the feature have been introduced, specify what parts of the feature.-->
> [Introduced](link_to_issue_or_mr) in GitLab (Tier) X.Y (2).

An introduction -- without its own additional header -- goes here.
Offer a very short description of the feature or use case, and what to expect on this page.
(You can reuse this content, or part of it, for the front matter's `description` at the top of this file).

## Overview

The feature overview should answer the following questions:

- What is this feature or use case?
- Who is it for?
- What is the context in which it is used and are there any prerequisites/requirements?
- What can the audience do with this? (Be sure to consider all applicable audiences, like GitLab admin and developer-user.)
- What are the benefits to using this over any alternatives?

## Use cases

Describe some use cases, typically in bulleted form. Include real-life examples for each.

If the page itself is dedicated to a use case, this section can usually include more specific scenarios
for use (e.g. variations on the main use case), but if that's not applicable, the section can be omitted.

Examples of use cases on feature pages:
- CE and EE: [Issues](../../user/project/issues/index.md#use-cases)
- CE and EE: [Merge Requests](../../user/project/merge_requests/index.md)
- EE-only: [Geo](../../administration/geo/replication/index.md)
- EE-only: [Jenkins integration](../../integration/jenkins.md)

## Requirements

State any requirements for using the feature and/or following along with the instructions.

These can include both:
- technical requirements (e.g. an account on a third party service, an amount of storage space, prior configuration of another feature)
- prerequisite knowledge (e.g. familiarity with certain GitLab features, cloud technologies)

Link each one to an appropriate place for more information.

## Instructions

"Instructions" is usually not the name of the heading.
This is the part of the document where you can include one or more sets of instructions, each to accomplish a specific task.
Headers should describe the task the reader will achieve by following the instructions within, typically starting with a verb.
Larger instruction sets may have subsections covering specific phases of the process.

- Write a step-by-step guide, with no gaps between the steps.
- Start with an h2 (`##`), break complex steps into small steps using
subheadings h3 > h4 > h5 > h6. _Never skip a hierarchy level, such
as h2 > h4_, as it will break the TOC and may affect the breadcrumbs.
- Use short and descriptive headings (up to ~50 chars). You can use one
single heading like `## Configuring X` for instructions when the feature
is simple and the document is short.

<!-- ## Troubleshooting

Include any troubleshooting steps that you can foresee. If you know beforehand what issues
one might have when setting this up, or when something is changed, or on upgrading, it's
important to describe those, too. Think of things that may go wrong and include them here.
This is important to minimize requests for support, and to avoid doc comments with
questions that you know someone might ask.

Each scenario can be a third-level heading, e.g. `### Getting error message X`.
If you have none to add when creating a doc, leave this section in place
but commented out to help encourage others to add to it in the future. -->

---

Notes:

- (1): Apply the [tier badges](styleguide.md#product-badges) accordingly
- (2): Apply the correct format for the [GitLab version introducing the feature](styleguide.md#gitlab-versions-and-tiers)


---
feedback: true
---

---
comments: true
---

<style>
.alert-info {
    color: rgb(49,112,143) !important;
}
.purple {
    color:inherit;
}
.purple:hover {
    color:rgb(107,79,187);
}
</style>
