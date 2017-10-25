ansible-role-letsencrypt
========================

An Ansible role to create, renew and manage Letsencrypt certificates.

This role uses the Ansible `letsencrypt` task to perform a two stage cert creation/renewal.
Additionally, it can optionally place a script (called certman) on the machine to conditionally
renew certs via a cron job (which requires acme_tiny.py). The script really only
checks existing certs before calling acme_tiny.py.

Requirements
------------

You'll need a copy of acme_tiny.py from https://github.com/diafygi/acme-tiny

You may also may want a role of your own that `include_tasks` this one in a loop
to run through a list of domains you'd like to work on (see below).

Role Variables
--------------

If this role is called without any variables set, then it'll just prepare the
remote system for future certificates. It won't put a copy of certman onto the
machine, but it will make a few directories.

Setting a variable called `domain` and calling this role will skip any preparatory
steps, and instead will try to perform a certificate creation/renewal. It will
save out a domain control verification challenge/response file, which you'll
need to arrange is visible on the webserver for the domain. Assuming Letsencrypt
can verify the challenge, then the cert will be created/renewed.

The variables available are:

  * `domain` (optional)
    The domain for the certificate to be created/renewed, or leave undefined to
    perform preparatory steps.
  * `docroot` (default: `/var/www/letsencrypt`)
    The webserver's docroot (but also see `acme_challenge_dir`)
  * `acme_challenge_dir` (default: `.well-known/acme-challenge`)
    This directory goes inside the docroot, and is mostly set by Letsencrypt
  * `remote_cert_store` (default: `/etc/ssl/letsencrypt`)
    The directory on the remote server to store the certs in
  * `acme_url` (default: `https://acme-staging.api.letsencrypt.org/directory`)
    The URL of the Acme Directory. By default we use the staging URL because it won't
    trip any of the limits that the production Directory has. Once you've got things
    working, then change this to the production URL.
  * `letsencrypt_days_before_expiry` (default: 10)
    The number of days left on a certificate before either the Ansible role, or certman
    attempt renewal.
  * `certman_enabled` (default: no)
    If enabled, a small script (called certman) will be installed onto the remote server
    and a daily (root) crontab will be added to run it
  * `letsencrypt_install_acme_tiny` (default: no)
    If set to yes, then the acme_tiny Git repo will be cloned into `letsencrypt_acme_tiny_dir`
  * `letsencrypt_acme_tiny_dir` (default: `/opt/acme_tiny`)
    The directory acme_tiny will be installed into (note: installation is by Git clone, so it may
    not be wise to put this in /usr/local/bin or similar)
  * `letsencrypt_acme_tiny_path` (default: `letsencrypt_acme_tiny_dir/acme_tiny.py`)
    The location of the acme_tiny script. If `letsencrypt_install_acme_tiny` is set, then this
    path is used by certman to run acme_tiny. If it's not set, then the system
    PATH is used by certman.


Example Playbook
----------------

    - hosts: servers
      roles:
         # Prepare the remote server
         - { role: ansible-role-letsencrypt }
         - { role: ansible-role-letsencrypt, domain: example.com }
         - { role: ansible-role-letsencrypt, domain: anotherexample.com }

License
-------

BSD

You're welcome to do what you want with this, but if you find it useful, we'd love to hear about it.

Author Information
------------------

coofercat at github.com
