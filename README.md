# Blite
Blite is a CLI tool meant to make it easier to use Bosh locally with Bosh2 and VirtualBox, typically called bosh-lite. 
It's goal is to fill in the gaps left from moving off the bosh-lite Vagrant workflow and make it relatively painless for developers
to get a functional Bosh director locally using VirtualBox that works with Bosh2.

It is still a work in progress.

## Dependencies
  - [bosh2 cli](https://bosh.io/docs/cli-v2.html#install)
  - [VirtualBox](https://virtualbox.org)
  - [jq](https://stedolan.github.io/jq/)
  - [curl](https://curl.haxx.se/)

## Quick Start
 1. Clone this repo or download the raw blite file.
 1. Add blite to your PATH (optional)
 1. Run `blite help` or just `blite` to see available commands and their usage.
 1. Run `blite create` to get the director started
 1. Run `eval (blite env-eval)` to configure your shell to talk to the newly created bosh director.
 1. Run `blite route-add` to make sure you have a route to communicate with the director and the things it deploys.
 1. Optionally, take a base backup `blite snapshot base` so in future you can avoid the time suck of recreation and instead restore the base snapshot.
 1. Optionally, set the default cloud-config for bosh lite: `blite cloud-config` (pulls from: https://github.com/cloudfoundry/bosh-deployment/blob/master/warden/cloud-config.yml)
  
## Avoiding Network Issues
The default blite settings will create a director at `192.168.50.6` in the `192.168.50.0/24` subnet with a gateway 
at `192.168.50.1`. This director will deploy containers into the `10.244.0.0/16` subnet. This matches the current default
values used in the `bosh-lite` documentation. If you're running blite in a network environment that conflicts with these 
IP spaces you'll override the environment variables specified in the create method _before_ running create. You may also 
consider setting these permanently in `.bashrc`. Doing so will limit the usefulness of shortcut methods like: `blite cloud-config`

It is also possible to manage multiple local Bosh directors with blite by controlling these environment variables.
 
## create
Creates a new local Bosh director. Pass in `BLITE_DIRECTOR_CIDR`, `BLITE_DIRECTOR_IP`, `BLITE_GATEWAY_IP` to set a custom network configuration.
Blite creates a unique identifier for your bosh director by hashing together your hostname, and the values of `BLITE_DIRECTOR_CIDR`, 
`BLITE_DIRECTOR_IP`, `BLITE_GATEWAY_IP`. You can also pass in a `BLITE_OPS_FILE_GLOB` that will append all the files that it matches as operator files 
at compilation time.

## config
Outputs environment and routing configuration info you might need to set in order to connect to the director and the things it deploys.
Note that these are not the same as the environment variables that control the network space used in `create`.

## env-eval
This command is a helper for sourcing the environment variables bosh2 needs to work. It is meant to be run wrapped in an eval like this:

```bash
eval $(blite env-eval)

```

## route-add
A helper command that will attempt to detect your OS and update your routing table so it's possible to connect to the 
things your director deploys. This function working properly for you is heavily dependent on the cloud config you pass 
to the director. Make sure the `BLITE_BOSH_DEPLOYMENTS_CIDR` environment variable is set to the actual CIDR of your 
director's internal network where things are being deployed.

## route-rm
A helper command that will attempt to detect your OS and cleanup your routing table removing the route that would allow 
you to connect to the things your director deploys. This function working properly for you is heavily dependent on the 
cloud config you pass to the director. Make sure the `BLITE_BOSH_DEPLOYMENTS_CIDR` environment variable is set to the 
actual CIDR of your director's internal network where things are being deployed.

## pause
Uses VBoxManage to pause the director.

## resume
Uses VBoxManage to resume the director. Resume will detect if the director was paused or fully powered off and respond 
correctly.

## snapshot
Snapshots the state of a running Bosh director. Can be passed a snapshot name, if none is provided a timestamp will be used.
For example: `blite snapshot base` will create a snapshot called "base"

## snapshots
Lists the available snapshots for the Bosh director.

## restore
Restores the specified snapshot. Must be passed a snapshot name.
For example: `blite restore base` will restore the snapshot called "base"

## destroy
Deletes the bosh director and purges any cached state and credential data for that specific director. Note that in the
current implementation destroying the director will also destroy all snapshots. It's recommended to take a base snapshot
right after initial creation to save time, then instead of destroying, just restore to the clean director state.

## update
Fetches the latest version of the base manifest and operations files necessary for bosh-lite from GitHub per the current bosh documentation.

## purge
Deletes all cached information about directors, manifests, operations files, state, credentials, configuration, etc. This
is a very destructive operation and probably should not be used unless a totally clean and new environment is desired.
