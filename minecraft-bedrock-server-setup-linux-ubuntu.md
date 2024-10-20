**Step 1: Install Required Packages**

1. Update the package list:
```
sudo apt update -y
```
2. Install essential packages:
```
sudo apt install -y curl wget unzip screen openssl libssl1.1
```
3. Create a user to run the server:
```
sudo useradd -m -d /home/mcserver mcserver
sudo mkdir /home/mcserver/minecraft_bedrock/
```
3. Install Minecraft Bedrock Server: `https://minecraft.net/en/download/server/bedrock/`
```
sudo wget https://minecraft.net/en/download/server/bedrock/ -O bedrock-server-1.21.31.04.zip && sudo unzip bedrock-server-1.21.31.04.zip -d /home/mcserver/minecraft_bedrock/
```


**Step 2: Configure and Launch the Server**

1. Change ownership of the directory:
```
sudo chown -R mcserver: /home/mcserver/
```
2. Edit the `server.properties` file:
```
sudo nano /home/mcserver/minecraft_bedrock/server.properties
```
3. Launch the server in a detached screen session:
```
screen -S mc_server -dm java -Xmx1024M -Xms1024M -jar /home/mcserver/minecraft_bedrock/bedrock_server.jar nogui
```

**Step 3: Configure Server Properties**


2. Set desired settings, such as game mode, difficulty, and server name.
4. Save and exit the editor. `ctrl + x` then `y` 

**Step 4: Start and Monitor the Server**

1. Resume the screen session: `screen -r mc_Java_server`
2. Monitor the server output for any errors or issues
3. To stop the server, press `Ctrl + A + K` and then `y`

**Additional Tips**

* You can adjust the memory parameters `Xmx` and `Xms` to suit your server's needs
* Consider setting up automatic updates and backups for your server
* You can use a firewall to restrict incoming traffic to port 25565 (default Minecraft port)

