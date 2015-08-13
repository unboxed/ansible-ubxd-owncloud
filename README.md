ownCloud
======

Installs [ownCloud](http://owncloud.org/) from packages following ownCloud's installation instructions.

Acknowledgments
-------------------------

This role borrows heavily from the following:
https://github.com/Dhar/ansible-owncloud
https://github.com/debops/ansible-owncloud

Thanks to both http://debops.org and https://github.com/Dhar

Requirements
-------------------------

This role was created for and tested on Ubuntu Trusty.  Please add other distributions as you see fit.

Dependencies
-------------------------

None.

Usage
-------------------------
  add the following to your requirements.yml

  src: https://github.com/unboxedconsulting/ansible-ubxd-owncloud
  name: ubxd.owncloud

  ansible-galaxy install ubxd.owncloud

Also check the [Ansible Galaxy](https://galaxy.ansibleworks.com/intro) about page.

Example Playbook
-------------------------

Super simple:

    - hosts: owncloud
      roles:
      - { role: ubxd.owncloud }

Vault variable
-------------------------
```
---

# --- ownCloud admin login / password ---
owncloud_admin_username: 'admin'

# Default admin password, will be randomly generated if 'secret' role is enabled
owncloud_admin_password: 'password'

# --- ownCloud database ---
# ownCloud recommends MySQL database as the default. Set to False to use SQLite
owncloud_database: 'mysql'

owncloud_database_map:
  # MySQL database on localhost
  mysql:
    dbtype: 'mysql'
    dbname: 'owncloud'
    dbuser: 'owncloud'
    dbpass: 'db_password'
    dbhost: 'hostname'
    dbtableprefix: ''
```

Role variable
-------------------------
```
---

# --- Basic options ---

# Should ownCloud role manage it's own dependencies (nginx, php5, postgresql, mysql)?
# If you want to setup them differently, you should change this to False
owncloud_dependencies: True

# Domain that will be configured for ownCloud instance
owncloud_domain: [ 'owncloud.{{ ansible_domain }}' ]


# --- ownCloud source and deployment ---

# User and group that will be used for ownCloud instance
owncloud_user: 'owncloud'
owncloud_group: 'owncloud'
owncloud_home: '/srv/users/{{ owncloud_user }}'

# Path where ownCloud data/ directory and files are stored
owncloud_data_path: '/srv/lib/{{ owncloud_user }}/{{ owncloud_domain[0] }}/data'

# Where ownCloud instance will be deployed (web root)
owncloud_deploy_path: '/srv/www/{{ owncloud_user }}/sites/{{ owncloud_domain[0] }}/public'

# What source should be used to get ownCloud files (upstream, githost, github, gitlab)
owncloud_source: 'upstream'

# Default URL and git branch for ownCloud upstream repository
owncloud_upstream_url: 'https://github.com/owncloud/core.git'
owncloud_upstream_branch: 'stable6'

# Default settings for ownCloud sources other than "upstream"
# If you want to use that with github, you can create your ownCloud repository
# as '<your username>/owncloud' with branch 'master'
owncloud_deploy_user: 'git'
owncloud_deploy_server: 'github.com'
owncloud_deploy_repo: '{{ lookup("env","USER") }}/owncloud.git'
owncloud_deploy_branch: 'master'

# OAuth token for GitHub / GitLab access, used to setup SSH deploy key
owncloud_deploy_token: False

# Hash of different ownCloud sources
owncloud_source_map:

  # Official ownCloud upstream repository on GitHub (basic installation)
  upstream:
    url:    '{{ owncloud_upstream_url }}'
    branch: '{{ owncloud_upstream_branch }}'

  # A git repository configured on a host in the Ansible cluster, for example
  # with 'githost' role
  # Ansible will try and setup ssh public key of the 'owncloud' user as deploy
  # key on the server with git repository, using authorized_key module
  githost:
    url:    '{{ owncloud_deploy_user }}@{{ owncloud_deploy_server }}:{{ owncloud_deploy_repo }}'
    branch: '{{ owncloud_deploy_branch }}'

  # A git repository set up on GitHub, with deploy key configured through API
  # using OAuth token
  github:
    url:    'git@github.com:{{ owncloud_deploy_repo }}'
    branch: '{{ owncloud_deploy_branch }}'

  # A git repository set up on a GitLab instance, with deploy key configured
  # through API using OAuth token
  gitlab:
    url:    '{{ owncloud_deploy_user }}@{{ owncloud_deploy_server }}:{{ owncloud_deploy_repo }}'
    branch: '{{ owncloud_deploy_branch }}'


# --- ownCloud database ---

# ownCloud recommends MySQL database as the default. Set to False to use SQLite
owncloud_database: 'mysql'

owncloud_database_map:

  # MySQL database on localhost (random password will be generated when using 'secret' role)
  mysql:
    dbtype: 'mysql'
    dbname: '{{ owncloud_user }}'
    dbuser: '{{ owncloud_user }}'
    dbpass: '{{ owncloud_dbpass | default("password") }}'
    dbhost: 'localhost'
    dbtableprefix: ''

  # PostgreSQL database on localhost, connection through Unix socket, no default password
  postgresql:
    dbtype: 'pgsql'
    dbname: '{{ owncloud_user }}'
    dbuser: '{{ owncloud_user }}'
    dbpass: ''
    dbhost: '/var/run/postgresql'
    dbtableprefix: ''


# --- ownCloud admin login / password ---

# Default admin username, in the form 'admin-$USER'
# Set to False to disable automatic username and password
owncloud_admin_username: 'admin-{{ lookup("env","USER") }}'

# Default admin password, will be randomly generated if 'secret' role is enabled
owncloud_admin_password: 'password'

# Length of randomly generated admin password
owncloud_password_length: '20'

# Should Ansible automatically open ownCloud page to finish setup on it's own?
# Disabled if admin username is set to False
owncloud_autosetup: True


# --- ownCloud configuration ---

# Max upload size set in nginx and php5, with amount as M or G
owncloud_upload_size: '128M'

# Output buffering set in php5, with amount set in megabytes
owncloud_php5_output_buffering: '128'

# Max children processes to run in php5-fpm
owncloud_php5_max_children: '50'

# At what time cron should execute background jobs
owncloud_cron_minute: '*/15'
```

License
-------

GPLv3

Author Information
------------------

Kips Davenport

[GitHub project page](https://github.com/unboxedconsulting/ansible-ubxd-owncloud)
