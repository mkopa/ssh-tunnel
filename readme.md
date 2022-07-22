# Persistent SSH tunnel - Ubuntu 20.04 22.04

## Step 1

```
sudo vim /etc/systemd/system/tunnel.service
```

```
[Unit]
Description=Maintain Tunnel
After=network.target

[Service]
User=marcin
ExecStart=/usr/bin/ssh -i /home/marcin/.ssh/id_rsa -o ServerAliveInterval=60 -o ExitOnForwardFailure=yes -gnNT -R 0.0.0.0:20222:localhost:22 remotevpsuser@myremotevpshost
RestartSec=15
Restart=always
KillMode=mixed

[Install]
WantedBy=multi-user.target
```

## Step 2

```
sudo systemctl daemon-reload
sudo systemctl enable tunnel
sudo systemctl start tunnel
```

## Troubleshooting

`Connection refused`

If you check the man page for ssh, you'll find that the syntax for -R reads:
```
-R [bind_address:]port:host:hostport
```

so we set the bind_address to 0.0.0.0 which means that the port is accessible on all interfaces via IPv4.

## BUT!


If you use OpenSSH sshd server on your remote vps/machine, the server's **GatewayPorts option needs to be enabled for this to work.**
Otherwise (default value for this option is `no`), the server will always force the port to be bound on the loopback interface only!


So, edit the `/etc/ssh/sshd_config` file (on remote VPS/Server):
```
vim /etc/ssh/sshd_config
```
add (or change) `GatewayPorts` option:
```
GatewayPorts clientspecified
```
save file and restart sshd daemon:
```
sudo systemctl restart ssh
```
or reboot system

### Enjoy!
