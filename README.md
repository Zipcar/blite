# Blite
Blite is a CLI tool meant to make it easier to use Bosh locally with Bosh2 and VirtualBox, typically called bosh-lite. 
It's goal is to fill in the gaps left from moving off the bosh-lite Vagrant workflow and make it relatively painless for developers
to get a functional Bosh director locally using VirtualBox that works with Bosh2.

It is still a work in progress.

```
blite v0.1.0
Supported commands are: create, destroy, pause, resume, update, purge
```

## Dependencies
  - [bosh2 cli](https://bosh.io/docs/cli-v2.html#install)
  - [VirtualBox](https://virtualbox.org)
  - [jq](https://stedolan.github.io/jq/)
  - [curl](https://curl.haxx.se/)

## create
Creates a new local Bosh director. Pass in `BLITE_CIDR`, `BLITE_DIRECTOR_IP`, `BLITE_GATEWAY_IP` to set a custom network configuration.
Blite creates a unique identifier for your  bosh director by hashing together your hostname, and the values of `BLITE_CIDR`, 
`BLITE_DIRECTOR_IP`, `BLITE_GATEWAY_IP`.

## env
Outputs environment info you might need to set in order to connect to the director.

## pause
Uses VBoxManage to pause the director.

## resume
Uses VBoxManage to resume the director

## destroy
Deletes the bosh director and purges any cached state and credential data for that specific director.

## update
Fetches the latest version of the base manifest and operations files necessary for bosh-lite from GitHub per the current bosh documentation.

## purge
Deletes all cached information about directors, manifests, operations files, state, credentials, configuration, etc.
