rethinkdb-download
==================

This repository contains some scripts to manage download.rethinkdb.com, currently hosted on Linode.

Files are managed by `git-annex`, which means that git stores only broken symlinks to the files.
Run `git annex get <path>` to download a file.

## Contents

* `version/` contains a list of files for each version of RethinkDB
* `download.rethinkdb.com/` is a mirror of http://download.rethinkdb.com
* `apt-repo/` contains scripts and a package cache to generate `download.rethinkdb.com/apt/`
* `nginx.conf` contains the nginx config for download.rethinkdb.com

## Actions

* `apt-repo/add-version <version>` - adds the packages from `version/<version>/deb/` to a cache in `apt-repo/lib`
* `apt-repo/build-repo` - build the apt repo in `download.rethinkdb.com/apt`
* `apt-repo/freight` - a git submodule: the freight scripts are used by `add-version` and `build-repo`
* `rake update-nginx` - upload the nginx config to the server
* `rake publish` - pretend to publish the files to download.rethinkdb.com using `rsync --dry-run`
* `rake publish[force]` - actually publish the files.
