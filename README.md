
## The scenario:

It is the weekend; you are feeling under the weather.  Your friend from Bangor is visting.  In a theraflu and banana liquor fueled adventure, you end up buying a your friend a funny domain on dreamhost.  You've to deployed a funny application to the cluster in the basement (the until recently pretty secure one, the one behind the virtualized openwrt router using the finnish exit nodes).  You remember the virtualized router in front of that cluster is equipt to do tailscale subnetting. 

**The controller and services:**
- You are on a mac that is on your tailscale network, this'll be the ansible controller and tofu controller.  
- You have ssh keys already registered with a personal **DigitalOcean** account.
  - This'll host the caddy reverse proxy to the service running on your tailnet.  
- You have an API token from your Dreamhost account. You'll use this to configure ACME and DNS.
- You have a tailscale auth key.


## The result:
A single playbook that'll both provision the caddy VM on digitalocean and deploy the caddy proxy that'll expose the local tailnet service.  


***Some special playbook considerations:***
- We poll the newly provisioned DO droplet, which adds itself to the tailnet using the runcmd spec.  this can take up to 15 minutes.  
- We use xcaddy tooling to build a caddy binary that supports dreamhost DNS directly on the droplet.    


***Next steps:***
- We can use this playbook to provision and deploy mTLS both from clients to the reverse proxy and from the reverse proxy to the host. 


**On darwin controllers, you'll need dees:**
```shell
# on mac:
brew install doctl opentofu
```

**Setup local env:**
```shell
sudo chmod +x scripts/setup_venv.sh
./scripts/setup_venv.sh && source darwin_venv/bin/activate
```

**Provision everything**
```shell
ansible-playbook -i inventory_dev -K deploy-proxy.yml
```
