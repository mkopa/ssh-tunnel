# Persistent SSH tunnel - Ubuntu 20.04 22.04

## Dependencies

```
sudo apt update
sudo apt install openssh-server
```

Edit file `/etc/ssh/sshd_config`
```
sudo vim /etc/ssh/sshd_config
```
Uncomment or add the lines:
```
Port 22
ListenAddress 0.0.0.0
GatewayPorts clientspecified
```
restart sshd service:
```
sudo systemctl restart sshd.service
```

## Step 1

Create startup file `/etc/systemd/system/tunnel.service`

```
sudo vim /etc/systemd/system/tunnel.service
```
Paste configuration:
```
[Unit]
Description=Persistent SSH Tunnel
After=network.target

[Service]
User=marcin
ExecStart=/usr/bin/ssh -i /home/marcin/.ssh/id_rsa -o ServerAliveInterval=3 -o ExitOnForwardFailure=yes -gnNT -R 0.0.0.0:20222:localhost:22 remotevpsuser@myremotevpshost
RestartSec=5
Restart=always
KillMode=mixed

[Install]
WantedBy=multi-user.target
```
**Modify the `User` and `ExecStart` - path to the RSA private key, bind address (e.g. port 20222, which is accessible from the machine myremotevpshost)**

## Step 2

```
sudo systemctl daemon-reload
sudo systemctl enable tunnel
sudo systemctl start tunnel
```

### Done.
