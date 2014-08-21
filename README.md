Ganeti Hoogle server installation
=================================

Prerequisites
-------------

A server machine running a recent version of Debian or Ubuntu.

Installation
------------

On the server machine, install `slack`. Copy these files to a suitable
directory, such as `~/slack` and run

    slack -s ~/slack/ ganeti-hoogle

Usage
-----

The slack role installs the Apache web server together with
[Hoogle](https://hackage.haskell.org/package/hoogle) and populates the Hoogle
database with [the most recent (auto-generated) Ganeti
version](http://docs.ganeti.org/ganeti/master/api/hs/ganeti.txt), as well as the
packages in `roles/ganeti-hoogle/files/srv/hoogle/packages.lst`.

It also sets up a cron job for updating the Ganeti Hoogle database every hour.

Maintenance
-----------

The list of packages to be included in the database isn't automatically updated.
So if a new dependency is added, or if Ganeti requires some more recent version
of a library, a manual update of the aforementioned `packages.lst` needs to be
updated.
