# minecraft-systemd

    [Unit]
    Description=Minecraft Server
    Wants=network.target
    After=network.target

    [Service]
    WorkingDirectory=/home/minecraft/minecraft
    Type=forking
    Nice=5
    User=minecraft
    Group=minecraft
    ExecStart=/usr/bin/screen -dmS mc /usr/bin/java -Xmx1024M -jar minecraft_server.jar nogui 

    ExecStop=/usr/bin/screen -S mc -X eval 'stuff "say SERVER SHUTTING DOWN. Saving map..."\\015'
    ExecStop=/usr/bin/screen -S mc -X eval 'stuff "save-all"\\015'
    ExecStop=/usr/bin/screen -S mc -X eval 'stuff "stop"\\015'
    ExecStop=/bin/sleep 2

    [Install]
    WantedBy=multi-user.target

# How-to
* Create a `minecraft` user and group
* Put put the mincraft server in that user's `$HOME/minecraft`
* Make sure `screen` is installed.
* put this file in /lib/systemd/system/minecraft.service
* run `systemctl enable mincraft` to have it start on boot.
