# Medic Watchdog Hosting Files

To set up a self hosted instance of Watchdog, please see [set up docs](https://docs.communityhealthtoolkit.org/hosting/monitoring/setup/).

Medic teammates administrating `watchdog.app.medicmobile.org`, this is for you!
 This repo has helper files and scripts that power Medic's internal instance of [Watchdog](https://github.com/medic/cht-watchdog/). 

## Restarting Watchdog

If you need to restart, be sure to use `/root/down-up.sh` as this ensures all compose files are referenced

## Changing docker compose files

Feel free to test in production (!!) and then backport them to this repo.  But this repo must always stay up to date.  Changes not committed to this repo may be lost as this repo is canonical.

## Initial Setup

1. Create an EC2 instance with a static IP
2. Follow normal setup steps to deploy watchdog.  Note that install was done as `root` instead of `ubuntu` user.  As well, instead of `cht-watchdog` being the directory name, it's `cht-monitoring` .
3. Clone this repo in `/root/medic-watchdog-hosting-files`
4. In 1password, find the `SLACK-WATCHDOG-SECRET` string. This is the bearer token appended to the URL so the `curl` call works without authentication
5. As root, create a cronjob replace `SLACK-WATCHDOG-SECRET` with the value from prior step:
   ```shell
   */5  * * * * /root/medic-watchdog-hosting-files/continious-deployment.sh SLACK-WATCHDOG-SECRET
   ```
6. Symlink all the config files into place:
   ```shell
   ln -s ~medic-watchdog-hosting-files/cht-instances.yml ~/cht-monitoring/cht-instances.yml
   ln -s ~medic-watchdog-hosting-files/node-exporter/ ~/cht-monitoring/node-exporter
   ln -s ~medic-watchdog-hosting-files/Caddyfile ~/Caddyfile
   ln -s ~medic-watchdog-hosting-files/caddy-compose.yml ~/caddy-compose.yml
   ln -s ~medic-watchdog-hosting-files/continious-deployment.sh ~/continious-deployment.sh
   ln -s ~medic-watchdog-hosting-files/down-up.sh ~/down-up.sh
   ```
7. Install [LastVersion](https://github.com/dvershinin/lastversion) in `/root/lastversion/` with a virtual environment. The net result should be that this works: `/root/lastversion/venv/bin/lastversion https://github.com/medic/cht-watchdog`. This will enable the cronjob above to work
