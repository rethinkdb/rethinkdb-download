rethinkdb-download
==================

RethinkDB download service configuration on Linode.

This repository contains both the nginx configuration and a Rakefile that
manages several download tasks. The Rakefile is intended to be used on any
machine. It can:
  - Sync the nginx configuration on Linode and restart the download service with `rake update_nginx`
  - Deploy the latest downloads from an authoritative mirror (currently
  	existing on `dr-doom`) to Linode with `rake release`
