
Prerequisites

1. Install Java: Ubuntu 22.04 does not provide a recent enough version of Java to run the newest releases of Minecraft. You can install OpenJDK 17 or later using the following command:
```bash
sudo apt-get update
sudo apt-get install openjdk-17-jdk
```
2. Create a new user and directory for the Minecraft server:
```bash
sudo useradd mcserver
sudo mkdir /home/mcserver/minecraftbedrock
```
OR
```bash
sudo useradd -m -d /home/mcserver mcserver
sudo mkdir /home/mcserver/minecraftbedrock
```

Download and Extract the Minecraft Bedrock Server

1. Download the Minecraft Bedrock server software from the official website: <https://minecraft.net/en-us/download/server/bedrock/>
2. Extract the ZIP file to the `/home/mcserver/minecraftbedrock` directory:
```bash
sudo unzip minecraftserverbedrock.zip -d /home/mcserver/minecraftbedrock
```
Configure the Server

1. Edit the `server.properties` file to set the server settings (e.g., port, RAM allocation):
```bash
sudo nano /home/mcserver/minecraftbedrock/server.properties
```
Example settings:
```properties
server-port=25565
max-players=20
ram=1024M
```
2. Create a systemd service file to manage the server:
```bash
sudo nano /etc/systemd/system/minecraftbedrock.service
```
Example service file:
```systemd
[Unit]
Description=Minecraft Bedrock Server
After=network.target

[Service]
User=mcserver
ExecStart=/home/mcserver/minecraftbedrock/bedrockserver
Restart=always

[Install]
WantedBy=multi-user.target
```
Start and Enable the Service

1. Reload the systemd daemon:
```bash
sudo systemctl daemon-reload
```
2. Start the Minecraft Bedrock server:
```bash
sudo systemctl start minecraftbedrock
```
3. Enable the service to start automatically on boot:
```bash
sudo systemctl enable minecraftbedrock
```
Access the Server

1. Connect to the server using the Minecraft client: `localhost:25565` (or the IP address of your Ubuntu machine)
2. Configure the server settings as needed (e.g., add operators, configure game modes)

Additional Tips

 Use `screen` or `tmux` to detach and reattach to the server console, allowing you to leave the terminal session and come back to it later.
 Monitor the server logs for errors and issues.
 Consider setting up port forwarding on your router to allow external access to the server.

By following these steps, you should have a functional Minecraft Bedrock server up and running on your Ubuntu 22.04 machine.
