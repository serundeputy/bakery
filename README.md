Bakery module
=============

Provides single sign-on (SSO) functionality for two or more sites.

Deploy this module on the authoritative "master" Backdrop server and the secondary
"slave" or subsite server. The master and slave must be on the same domain*.


Installation
------------

1. Install this module using the official Backdrop CMS instructions at
  https://backdropcms.org/guide/modules

2. Visit the configuration page under Administration > Configuration > System >
  Bakery (admin/config/system/bakery) and configure, as described below.


Set-Up
------

Enable and configure Bakery on the master server first. It is recommended that
you use the UID 1 Backdrop account for this configuration.

This is the master site:

  3. Check the box for "Is this the master site?"
  4. Enter the full URL of this site, including ending forward slash
    - Example: http://example.org/

For SSO to work, Bakery must know the slave, or subsites, to use.

  5. Enter the full URLs of each slave site, separated by newlines
    - Example:  http://store.example.org/
                http://api.example.org/

Two other required fields for Bakery to work are the private key and the cookie
domain.

  6. Enter a long and secure private key
  7. Enter the domain to use for Bakery cookies. These cookies are shared so
      the domain should be the top level, with leading period.
    - Example: .example.org
  8. Save configuration (we'll come back to the other fields)

Now to enable and configure Bakery on the slave or subsite. If possible, you
should log in and use the UID 1 Backdrop account for this configuration.

  9. Enable Bakery at admin/modules
  10. Visit admin/config/system/bakery to configure

This is a subsite site.

  11. Do not check the master site box
  12. Enter the full URL of the master site set in step #4
  13. The slave sites textarea can be left blank
  14. Enter the exact same private key set in step #6
  15. Enter the exact same domain set in step #7
  16. Save configuration (we'll come back to the other fields)

Bakery should now be set to work for the master and this slave site. Open a
different browser than the one you are currently using and visit the master
site. Log in with a standard account. Once successful visit the slave site and
confirm that you are also logged in. If you encountered problems at any point
please consult the section here labeled "Problems and support".

You can now enable and configure Bakery for sites in your network if required,
or read the section labeled "Sharing account information using Bakery".

* Master and slave must be on the same domain, but are not required to be at
certain levels. For example, you can have the master be sub.example.com and a
slave be example.com.

How Bakery works
----------------
Bakery provides single sign-on between Backdrop sites on the same domain using a
shared cookie. When a user authenticates on a site they are sent a cookie by
Backdrop, containing a unique identifier for that user. Sub-sequent requests by
that user will contain the identifier, allowing Backdrop to recognize that the
request is coming from a specific user, an authenticated user. This process is
handled by Backdrop core. Bakery augments the login process and sends an
additional cookie (referred internally to as the CHOCOLATECHIP cookie). Should
the user now visit a sub-site (on the same domain) their browser will send this
Bakery-created cookie. On the sub-site Bakery will recognize the cookie and if
it is valid will authenticate the user (via Backdrop core's processes). The user
is now authenticated on both sites while only have to log on to one.

Notes on terminology
--------------------
Bakery documentation and discussion makes repeated reference to the words
"master" and "slave". The terms stem from the common communication model between
devices or processes in computer systems where one has unidirectional control
over another. In Bakery's case, the master is the site with authoritative
account and authentication information. The master is occasionaly referred to as
the "main" site. The slave site can be referred to as the "subsite", but since
Bakery does not enforce top-level and sub-domains the term "subsite" may be
incorrect for some instances.

Common problems and support
---------------------------
  * Cannot log in, how do I disable Bakery?
    - If you do not have access to disable the Bakery module you'll need
      access to the Backdrop database for the site. To disable Bakery run this
      query: UPDATE system SET status - 0 WHERE name - 'bakery';
      After that you'll also need to clear caches.

Sharing account information using Bakery
----------------------------------------
A modest amount of account data sharing between Bakery-enabled sites is
supported. The core account fields 'name' and 'mail' are always synchronized.

The following fields from user accounts are optionally synchronized:
  * status
  * user picture
  * language
  * signature
  * timezone

Notes on registration/sign-in on subsites
-----------------------------------------
This feature is important for usability. It's also really easy to configure your
site so that this feature is horrible for usability. A few examples:

Registration:
This feature does not support saving any data other than the username, e-mail
address, and fields created with the core profile module. If you have other
modules that modify the registration process to add fields or make the form
behave differently they are unlikely to work properly from the subsite.

You should keep the "allow registration" and "Require e-mail verification when
a visitor creates an account" settings the same on all sites. If your
master site disallows registration then no subsites will be allowed to create
accounts either and users will be confused why they see a form but it doesn't
work.

Notes on migrating to Bakery
----------------------------
Migrating to using Bakery can be a fairly simple process. This process will work
for separate sites and for a shared users table, though the latter can require a
few additional steps.

Pre-migration data synchronization is recommended to alleviate potential
account mis-matches. According to Bakery two accounts are in sync when the
username and email are identical and the slave account's init property contains
the URL of the user edit page on the master
(e.g. http://example.org/user/9/edit -- where example.org is the master). The
UID of the accounts do not need to be identical. It is recommended you at least
synchronize usernames and email address for accounts that belong to the same
person across sites.

For people that do not have joint accounts you could ask them via email to
create an account on the site where there account does not exist, to assist the
process.

For accounts that have the same username but different email addresses Bakery
provides a self-service mechanism for synchronizing accounts, but in the event
this is inadequate or confusing you should provide a site administrator contact
mechanism (form or email address) that is easily distinguishable.

If you have a shared users table solution between sites you will need to remove
that connection before migrating to Bakery. First, populate the other users
tables by synchronizing accounts. Modify settings.php on the shared sites to
remove the table information from the database connection details. If the cookie
domain begins with a leading dot you probably want to remove it, as it could
cause issues when users move between sites.

Once synchronization is complete you can follow the steps listed above on
installation and setup of Bakery.

Known issues
------------

  * Bakery is currently incompatible with a configuration that requires
    administrator approval of accounts. An account registered on the slave will
    not be set to blocked on the master.
  * Bakery is not compatible with modules that takeover core Backdrop's login
    and registration forms, e.g. ldapprov.

Documentation
-------------

Additional documentation is located in the Wiki:
https://github.com/backdrop-contrib/bakery/wiki/Documentation

Issues
------

Bugs and Feature requests should be reported in the Issue Queue:
https://github.com/backdrop-contrib/bakery/issues

Current Maintainers
-------------------

- docwilmot (https://github.com/docwilmot)
- Jen Lampton (https://github.com/jenlampton)
- seeking additional maintainers

Credits
-------

- Ported to Backdrop CMS by [docwilmot](https://github.com/docwilmot).
- Originally written for Drupal by [chx](https://www.drupal.org/u/chx)
- Maintained for Drupal by [David Strauss](https://www.drupal.org/u/david-strauss).
- Maintained for Drupal by [drumm](https://www.drupal.org/u/drumm)
- Maintained for Drupal by [pwolanin](https://www.drupal.org/u/pwolanin)
- Maintained for Drupal by [douggreen](https://www.drupal.org/u/douggreen)
- Maintained for Drupal by [coltrane](https://www.drupal.org/u/coltrane)

License
-------

This project is GPL v2 software. See the LICENSE.txt file in this directory for
complete text.
