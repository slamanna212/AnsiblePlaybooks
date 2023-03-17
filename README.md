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

## Setup
1. This repo requires some Galaxy Modules, install them with:

`ansible-galaxy install -r collections/requirements.yml`

`ansible-galaxy install -r roles/requirements.yml`

2. Copy extra_vars_TEMPLATE.yml , name it extra_vars.yml and fill it out. All values are required.

3. Run checklist.yml. It will export API keys and make sure the system is ready for the other playbooks 
`ansible-playbook --extra-vars "@extra_vars.yml" checklist.yml`







## Need to Know
Initial provisioning is not yet coded. 

Inventory is handled by a dynamic DigitalOcean inventory source. Include -i digitalocean.yml in all commands