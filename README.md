# Playbooks for Bene IT Web Infrastructure 
Runs and manages all of the servers for the Bene IT web hosting infrastructure. Tuned to serve Wordpress Websites. Can also handling some droplet creation and new Wordpress site creation.

## Features
 - HAProxy Loadbalancer. Can replace it via `NewNode.yml`
 - Nginx and PHP-FPM web server nodes, horizonal scaling is possible via `NewNode.yml`
 - Shared Storage provided by an NFS server. Backs up to DigitalOcean Spaces via rclone. Can restore those files if missing.
 - H/A database provided by an Read/Write and Read only pair
 - A dedicated node for WP-Admin tasks & WP-Cron. HAProxy handles sending traffic to the correct place. Scalable via `NewNode.yml`
 - Everything is done via private networks, the only servers with internet are the loadbalancer and control servers. 

## Requirements
 - All Websites need to use Cloudflare. SSL is terminated at the loadbalancer and uses a Cloudflare origin certificate. 
 - You need to have a private network setup for all of your droplets to communicate
 - Firewall is currently handled via DigitalOcean firewalls. Each droplet type has a firewall applied to it via DigitalOcean tags. More info on this below.
 - You need a SSH key setup with digitalocean. This ssh key will be added to new droplets and ansible will use this for initial provisioning. More info below.

## Tags
Firewalls and groups are handled via Digital Ocean tags, they are:

- `web_cluster` All nodes have this tag
- `wc_backend` All backend servers have this tag. Everything besides control and loadbalancer
- `control` Control servers, Runs ansible, Semaphore, and Zabbix
- `loadbalancers` Loadbalancer servers, HAProxy
- `web` Web servers. Nginx and PHP-FPM
- `wpadmin` WP-Admin servers. Installs, updates, and wp-cron tasks
- `file` File servers, export NFS share
- `database` All database servers, MariaDB
- `database_master` Master DB servers
- `database_slave` Slave DB servers

## Setup
1. This repo requires some Galaxy Modules, install them with:

     `ansible-galaxy install -r collections/requirements.yml`

     `ansible-galaxy install -r roles/requirements.yml`

2. Copy extra_vars_TEMPLATE.yml , name it extra_vars.yml and fill it out. All values are required.

3. Run checklist.yml. It will export API keys and make sure the system is ready for the other playbooks. Be sure to include extra vars, more info below.
     `ansible-playbook --extra-vars "@extra_vars.yml" checklist.yml`

4. Reload your shell
     `exec bash`

## Running Commands
You will need to specify the dynamic inventory and extra variables for all commands. Example below. 

`ansible-playbook --extra-vars "@extra_vars.yml" -i digitalocean.yml site.yml`

## Playbooks

- `checklist.yml` Makes sure localhost is ready to run these playbooks
- `site.yml` Deploys the entire cluster. 
- `deploy.yml` A smaller and faster version of `site.yml`, meant to be run after a new node is created
- `NewWordpressSite.yml` Creates a database user and database, creates nginx configs, downloads wordpress, gives it info, and all other new website creation tasks
- `NewNode.yml` Creates new nodes on DigitalOcean. uses playbooks in **Creation** folder


## Need to Know
Initial provisioning is not yet coded. 
