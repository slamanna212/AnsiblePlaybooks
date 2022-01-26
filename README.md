# Playbooks for Bene IT Web Infrastructure 
Runs and manages all of the servers for the Bene IT web hosting infrastructure. Can also handling some droplet creation and new Wordpress site creation

## Requirements
It requires some Galaxy Modules, install them with:

    ansible-galaxy install -r requirements.yml
For Droplet creation , a Digital Ocean API token is required. Export it as an env variable for these playbooks to use it. 

    export DO_API_TOKEN="API_TOKEN_HERE" 
