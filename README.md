# Playbooks for Bene IT Web Infrastructure 
Runs and manages all of the servers for the Bene IT web hosting infrastructure. Tuned to serve Wordpress Websites. Can also handling some droplet creation and new Wordpress site creation.

## Features
 - HAProxy Loadbalancer. Can replace it via `NewNode.yml`
 - Nginx and PHP-FPM web server nodes, horizonal scaling is possible via `NewNode.yml`
 - Shared Storage provided by an NFS server
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

Initial provisioning is not yet coded. Setup your default inventory file at `/etc/ansible/hosts` to look like the below example. Public IP under loadbalancers is your floating IP address. Replace the IPs below with the scheme of your private network:
```
[loadbalancers]
BeneNYCLB01v ansible_host=10.123.0.x public_ip=1.1.1.1

[webservers]
Bene-NYC-Web70v ansible_host=10.123.0.x id=329628893
Bene-NYC-Web48v ansible_host=10.123.0.x id=329625730

[wp_admin]
Bene-NYC-WPAdmin71v ansible_host=10.123.0.x id=329621615

[storage]
BeneNYCFile01v ansible_host=10.123.0.x

[database_master]
BeneNYCDB01v ansible_host=10.123.0.x

[database_slave]
BeneNYCDB02v ansible_host=10.123.0.x

[control]
BeneNYCCC01v ansible_host=10.123.0.x

[provisioning]

[all:vars]
ansible_python_interpreter=/usr/bin/python3
ansible_user=ansible
ANSIBLE_VAULT_PASSWORD_FILE=~/.vault_pass.txt
```