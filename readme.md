# Docker Fail2ban for Synology NAS

A docker-compose ready package to run [Fail2ban](https://github.com/crazy-max/docker-fail2ban) on Synology NAS. This setup is made to manage the Synology's DSM contraints and protect another container : Bitwarden_RS. However, adding your own actions, filters and jails may allow use for other purposes.

The goal is to keep the Synology NAS system untouched to be upgrade-proof. This the reason why we did not try to modify the system and improve the embedded banIP. The best deal has to be able to adapt the embedded `iptbles`.

Despite this has been made to run on Synology NAS, this should run on other systems with / without minor adaptations.


## Documentation

- [Crazy-Max/Docker-Fail2ban](https://github.com/crazy-max/docker-fail2ban/blob/master/README.md)

## Solved issues on Synology
The main issues on Synology are the following:

- The embedded ban IP system cannot work on running Docker containers by design
- `REJECT` blocktype is not supported and must be switched to `DROP`
- Modiying DSM system is not upgrade-proof

## Pre-requisite
- A Docker compatible Synology NAS
- An up and running Docker package
- A SSH client

### Conventions
As convention, we will use as example the following
- Folder used : `/volumeX/docker/` to be personnalized to your DSM setup

## Installation

1. Download this repo
2. Unzip and review `docker-compose_fail2ban.yml` settings
3. Copy this repo content to `/volumeX/docker/`

This is almost done. The file `action.d/iptables-common.local` switch the `REJECT` blocktype by `DROP`

## Setup

To finish the setup, you need to add your filters and jails. The provided ones relies on a [bitwarden_rs instance](https://githib.com/sosandroid/docker-bitwarden_rs-caddy-synology) and looks for the `bitwarden.log` file. If not available, you'll have an error at startup.

Ready for a first run : `docker-compose -f docker-compose_fail2ban.yml up`

If everything goes well, the prompt will let you know the container is started and wait until a `ctrl + C` is triggered to stop it. Have a look in log file and test your filters and rules. A usefull command to unban IP after testing :

`sudo docker exec -t fail2ban fail2ban-client set bitwarden unbanip XX.XX.XX.XX`

Shutdown the servers issuing a `ctrl + C` in the terminal

## Startup and Maintenance

### Startup
Once setup is finished, you're ready to launch your "_production_" server. Review all the settings and  environment variables in the `.yml` file. Test it using the same `docker-compose -f docker-compose_fail2ban.yml up` as previously. If everything goes well, stop them and run as `detached` with the following command.

	`docker-compose -f docker-compose_fail2ban.yml up -d`

### Maintenance
Upgrade on a regular basis the servers as they continue to evolve on a daily/weekly basis. Run from a terminal the following commands, as `root`, from time to time.

````sh
cd /volumeX/docker/
docker-compose -f docker-compose_fail2ban.yml down
docker-compose -f docker-compose_fail2ban.yml pull
docker-compose -f docker-compose_fail2ban.yml up -d
````

In order to keep a clean system, from time to time, use [this tutoriel](https://www.digitalocean.com/community/tutorials/how-to-remove-docker-images-containers-and-volumes).

## Use with Bitwarden_RS

This setup has been made for [Bitwarden_RS proxied](https://github.com/sosandroid/docker-bitwarden_rs-caddy-synology) runing as Docker container on Synology NAS

## Collaboration
Feel free to propose any optimization through pull requests
