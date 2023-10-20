# fourgames-detailed
this repo is a more completed version  to fourgames docs and apis : https://github.com/h0cean/fourgames

( this guide is mainly for  ubuntu v18+ )

# the goal 
  is to remove all typos and add missed steps of 
  https://github.com/h0cean/fourgames  to run this project

# requirements:
A. a domain with SSL/TLS

B. linux-based-host with sudo access 

C. nginx 

D.node.js

# zero step

A. buy a domain and point it to your host's ip by using Cloudflare DNS service
(you can use your preferred ones). ssh into host and make your host's traffic secure by configuring ufw, firewalld, IPTABLES...etc.
here i use iptables: 
the result would be only port 22, 80 and 443 are accepting traffic 
##### be carefull if your host's ssh-port is not 22, change the commands below for this specific port to the one you already use:
execute:

 `iptables -A INPUT -i lo -j ACCEPT`
 
 `iptables -A INPUT -m conntrack --ctstate ESTABLISHED,RELATED -j ACCEPT`
 
 `iptables -A INPUT -p icmp -j ACCEPT`
 
 `iptables -A INPUT -p tcp --dport 22 -j ACCEPT `

 `iptables -A INPUT -p tcp --dport 80 -j ACCEPT`
 
 `iptables -A INPUT -p tcp --dport 443 -j ACCEPT`
 
 `iptables -P INPUT DROP`

 `ip6tables -A INPUT -i lo -j ACCEPT`
 
`ip6tables -A INPUT -m state --state RELATED,ESTABLISHED -j ACCEPT`
 
 `ip6tables -A INPUT -p ipv6-icmp -j ACCEPT`
 
 `ip6tables -P INPUT DROP`

make them persistant:

`apt install iptables-persistent`



# install nginx 

execute

`$ sudo apt update`

`$ sudo apt install nginx`

# install nginx from source
//copied from nginx official website for Ubuntu:

The available NGINX Ubuntu release support is listed at this [distribution](https://nginx.org/packages/ubuntu/dists/) page. For a mapping of Ubuntu versions to release names, please visit the [Official Ubuntu Releases page](https://wiki.ubuntu.com/Releases).

Append the appropriate stanza to /etc/apt/sources.list. If there is concern about persistence of repository additions (i.e. DigitalOcean Droplets), the appropriate stanza may instead be added to a different list file under /etc/apt/sources.list.d/, such as /etc/apt/sources.list.d/nginx.list:
## Replace $release with your corresponding Ubuntu release:

`deb https://nginx.org/packages/ubuntu/ $release nginx`

`deb-src https://nginx.org/packages/ubuntu/ $release nginx`

e.g. Ubuntu 20.04 (Focal Fossa):
  
  `deb https://nginx.org/packages/ubuntu/ focal nginx`
  
   `deb-src https://nginx.org/packages/ubuntu/ focal nginx`
## To install the packages, execute in your shell:

`$ sudo apt update`

`$ sudo apt install nginx`

//If a W: GPG error: https://nginx.org/packages/ubuntu focal InRelease: The following signatures couldn't be verified because the public key is not available: NO_PUBKEY $key is encountered during the NGINX repository update, execute the following:
### Replace $key with the corresponding $key from your GPG error:

`$ sudo apt-key adv --keyserver keyserver.ubuntu.com --recv-keys $key`

`$ sudo apt update`

`$ sudo apt install nginx`


You have now nginx installed on your server but not ready to serve web pages. you have to start the nginx. You can do this by using this command:

`$ sudo systemctl start nginx`


# SSL/TLS and nginx configurations

## step 1 install acmc.sh
`$ cd /tmp/`

`$ git clone https://github.com/Neilpang/acme.sh.git`
 
 Login as Root User ( `sudo-i` ) and execute:

`$ touch /root/.bashrc`

`$ cd /tmp/acme.sh/`

`$ acme.sh --install --accountemail your-email-id@domain-here`

`$ acme.sh --set-default-ca --server letsencrypt`

## step 2 set CF_Token
excute: 

`export CF_Token="Your_Cloudflare_DNS_API_Key"`


## step 3 set Issuing Let’s Encrypt wildcard certificate
execute

`acme.sh --issue --dns dns_cf --ocsp-must-staple --keylength ec-384 -d domain-name.abc -d '*.domain-name.abc'`
 
Your Cloudflare DNS API key is sotred in /root/.acme.sh/


## step 4 installing let's encrypt wildcard certificate
make directory for certs:

`mkdir command -pv /etc/nginx/ssl/domain-name.abc/`

Next, change the dir and install diffie-hellman key-exchange-file

`cd /etc/nginx/ssl/domain-name.abc/`

`openssl dhparam -out /etc/nginx/ssl/damain-name/dhparams.pem -dsaparam 4096`

replace DOMAIN and  execute:

`acme.sh -d "$DOMAIN" --ecc --install-cert --fullchain-file "/etc/nginx/ssl/$DOMAIN/$DOMAIN.fullchain.cer.ecc" --key-file "/etc/nginx/ssl/$DOMAIN/$DOMAIN.key.ecc" --cert-file "/etc/nginx/ssl/$DOMAIN/$DOMAIN.cer.ecc"`

....to be continued


## install node-js
full credit to : [digital-ocean](https://www.digitalocean.com/community/tutorials/how-to-install-node-js-on-ubuntu-22-04)
#### intro
There are quite a few ways to get up and running with Node.js on your Ubuntu 22.04 server. Your circumstances will dictate which of the above methods is best for your needs. While using the packaged version in Ubuntu’s repository is the easiest method, using nvm or a NodeSource PPA offers additional flexibility.


##### Warning: 
The version of Node.js included with Ubuntu 22.04, version 12.22.9, is an LTS, or “long-term support” release. It is technically outdated, but should be supported until the release of Ubuntu 24.04. ... so... its better to NOT use the easy way(packaged version in Ubuntu’s repository)

### Installing Node Using the Node Version Manager
that is particularly flexible is to use nvm, the Node Version Manager. This piece of software allows you to install and maintain many different independent versions of Node.js, and their associated Node packages, at the same time

To install NVM on your Ubuntu 22.04 machine, visit the project’s [GitHub page](https://github.com/nvm-sh/nvm). Copy the `curl` command from the README file that displays on the main page. This will get you the most recent version of the installation script.

Before piping the command through to `bash`, it is always a good idea to audit the script to make sure it isn’t doing anything you don’t agree with. You can do that by removing the `| bash` segment at the end of the `curl` command:

`curl -o- https://raw.githubusercontent.com/nvm-sh/nvm/v0.39.1/install.sh`

Take a look and make sure you are comfortable with the changes it is making. When you are satisfied, run the command again with `| bash` appended at the end. The URL you use will change depending on the latest version of nvm, but as of right now, the script can be downloaded and executed by typing:

`curl -o- https://raw.githubusercontent.com/nvm-sh/nvm/v0.39.1/install.sh | bash`

This will install the `nvm` script to your user account. To use it, you must first source your `.bashrc` file:

`source ~/.bashrc`

Now, you can ask NVM which versions of Node are available:

`nvm list-remote`

You can install a version of Node by typing any of the release versions you see. For instance, to get version v16.14.0 (another LTS release), you can type:

`nvm install v16.14.0`

You can see the different versions you have installed by typing:

`nvm list`

This shows the currently active version on the first line (`-> v16.14.0`), followed by some named aliases and the versions that those aliases point to.

you can also `node -v` to see node version installed


##### Note: 
if you also have a version of Node.js installed through `apt`, you may see a `system` entry here. You can always activate the system-installed version of Node using `nvm use system`.



