# Webserver Configuration

::: warning
When using the SSL configuration you MUST create SSL certificates, otherwise your webserver will fail to start. See the [Creating SSL Certificates](https://github.com/Sundeep-deJongh/pterodocs/blob/main/SSL-Setup.md) documentation page to learn how to create these certificates before continuing.
:::

:::: tabs
::: tab "Nginx With SSL"
First, remove the default NGINX configuration.

``` bash
rm /etc/nginx/sites-enabled/default
```

Now, you should paste the contents of the file below, replacing `<domain>` with your domain name being used in a file called
`pterodactyl.conf` and place the file in `/etc/nginx/sites-available/`, or &mdash; if on CentOS, `/etc/nginx/conf.d/`.

### Enabling Configuration

The final step is to enable your NGINX configuration and restart it.

You can find the configuration by clicking [here](http://haste.sundeep.nl:7777/kuqoxozace.nginx).

```bash
# You do not need to symlink this file if you are using CentOS.
sudo ln -s /etc/nginx/sites-available/pterodactyl.conf /etc/nginx/sites-enabled/pterodactyl.conf

# You need to restart nginx regardless of OS.
sudo systemctl restart nginx
```

#### Next Step: [Wings Installation](../../wings/installing.md)
