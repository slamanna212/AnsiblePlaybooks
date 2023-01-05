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
 - Firewall is currently handled via DigitalOcean firewalls. Each droplet type has a firewall applied to it via DigitalOcean tags

## Setup
It requires some Galaxy Modules, install them with:

    ansible-galaxy install -r collections/requirements.yml
    ansible-galaxy install -r roles/requirements.yml

For Droplet creation , a Digital Ocean API token is also required. Export it as an env variable on the server running the playbook to use it. 

    export DO_API_TOKEN="API_TOKEN_HERE" 

Initial provisioning is not yet coded. 

Inventory is handled by a dynamic DigitalOcean inventory source. Include -i digitalocean.yml in all commands