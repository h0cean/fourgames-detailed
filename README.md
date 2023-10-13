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

# zero step

A. buy a domain and point it to your host's ip by using Cloudflare DNS service
(you can use your preferred ones)

B. ssh into host

C. make your host's traffic secure by configuring ufw, firewalld, IPTABLES...etc.



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

`$ sudo apt update

$ sudo apt install nginx`

//If a W: GPG error: https://nginx.org/packages/ubuntu focal InRelease: The following signatures couldn't be verified because the public key is not available: NO_PUBKEY $key is encountered during the NGINX repository update, execute the following:
### Replace $key with the corresponding $key from your GPG error:
`$ sudo apt-key adv --keyserver keyserver.ubuntu.com --recv-keys $key`

`$ sudo apt update`

`$ sudo apt install nginx`


You have now nginx installed on your server but not ready to serve web pages. you have to start the nginx. You can do this by using this command:

`$ sudo systemctl start nginx`


# SSL/TLS and nginx configurations
