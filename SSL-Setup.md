# How to setup a SSL certificate

As initial conditions, you must have a domain name. It's DNS A-record must contain the public address of your server. If the firewall is enabled, open access for HTTP and HTTPS traffic.
```nginx
sudo ufw allow 80
sudo ufw allow 443
```

## Installing the "Let's Encrypt" package

```nginx
sudo apt install letsencrypt
```

## Standalone server for getting the "Let's Encrypt" SSL certificate
The easiest way to get an ssl certificate is to use a standalone option in Certbot. Replace domain-name.com with your domain name, run the command, and follow the instructions:

```nginx
sudo certbot certonly --standalone --agree-tos --preferred-challenges http -d domain-name.com
```


## If the Standalone option doesn't work use this!

```nginx
apt install python3-certbot-nginx
sudo certbot --nginx --agree-tos --preferred-challenges http -d domain-name.com
```
replace ``domain-name.com`` with your own domain.
