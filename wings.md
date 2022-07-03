# Installing Wings

### Installing Docker

For a quick install of Docker CE, you can execute the command below:

```bash
curl -sSL https://get.docker.com/ | CHANNEL=stable bash
```

#### Start Docker on Boot

If you are on an operating system with systemd (Ubuntu 16+, Debian 8+, CentOS 7+) run the command below to have Docker start when you boot your machine.

```bash
systemctl enable --now docker
```
## Installing Wings

The first step for installing Wings is to ensure we have the required directory structure setup. To do so,
run the commands below, which will create the base directory and download the wings executable.

```bash
mkdir -p /etc/pterodactyl
curl -L -o /usr/local/bin/wings "https://github.com/pterodactyl/wings/releases/latest/download/wings_linux_$([[ "$(uname -m)" == "x86_64" ]] && echo "amd64" || echo "arm64")"
chmod u+x /usr/local/bin/wings
```

### Starting Wings

To start Wings, simply run the command below, which will start it in a debug mode. Once you confirmed that it is running without errors, use `CTRL+C` to terminate the process and daemonize it by following the instructions below. Depending on your server's internet connection pulling and starting Wings for the first time may take a few minutes.

```bash
sudo wings --debug
```

Running Wings in the background is a simple task, just make sure that it runs without errors before doing
this. Place the contents below in a file called `wings.service` in the `/etc/systemd/system` directory.

```text
[Unit]
Description=Pterodactyl Wings Daemon
After=docker.service
Requires=docker.service
PartOf=docker.service

[Service]
User=root
WorkingDirectory=/etc/pterodactyl
LimitNOFILE=4096
PIDFile=/var/run/wings/daemon.pid
ExecStart=/usr/local/bin/wings
Restart=on-failure
StartLimitInterval=180
StartLimitBurst=30
RestartSec=5s

[Install]
WantedBy=multi-user.target
```

Then, run the commands below to reload systemd and start Wings.

```bash
systemctl enable --now wings
```
