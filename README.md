# minecraft-systemd

### Start a server in the cloud
* Google Compute: cheapest 2-CPU, few 100s MB free disk
  * add firewall rule to all inbound tcp 25565
  * set static IP address

### Install required packages
```bash
sudo apt install default-jre vim screen
```


### Set up the Server

[Download the server](https://www.minecraft.net/en-us/download/server/)
  * [1.15.2](https://launcher.mojang.com/v1/objects/bb2b6b1aefcd70dfd1892149ac3a215f6c636b07/server.jar)

```bash
cd $HOME
mkdir -p minecraft
echo "eula=true" >> minecraft/eula.txt
mkdir -p ~/.config/systemd/user
```

Put the following systemd unit file in `/lib/systemd/system/server.service`.
This server runs as the `trekinator` user.

```systemd
[Unit]
Description=Minecraft Server
Wants=network.target
After=network.target

[Service]
WorkingDirectory=/home/trekinator/minecraft
Type=forking
Nice=5
User=trekinator
Group=trekinator
ExecStart=/usr/bin/screen -dmS mc /usr/bin/java -Xmx4096M -jar server.jar nogui 
ExecStop=/usr/bin/screen -S mc -X eval 'stuff "say SERVER SHUTTING DOWN. Saving map..."\\015'
ExecStop=/bin/sleep 1
ExecStop=/usr/bin/screen -S mc -X eval 'stuff "save-all"\\015'
ExecStop=/bin/sleep 10
ExecStop=/usr/bin/screen -S mc -X eval 'stuff "stop"\\015'
ExecStop=/bin/sleep 10
Restart=on-failure
RestartSec=5

[Install]
WantedBy=multi-user.target
```

Enable and start the service

```bash
systemctl enable server.service
```

## Managing

* `sudo systemctl daemon-reload` after changing unit file
* `sudo systemctl daemon-reload` to restart
* `sudo systemctl status server` to status
* `sudo systemctl start server` to start
* `screen -rS mc` to reattach
* `ctrl-a d` to detach

