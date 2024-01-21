# Setting up a Horizen Node
 
 ### update libs

`apt update && apt upgrade`



### get release sig

`echo 'deb https://zencashofficial.github.io/repo/ '$(lsb_release -cs)' main' | sudo tee /etc/apt/sources.list.d/zen.list`



### add ssh keys
```
gpg --keyserver keyserver.ubuntu.com --recv 219F55740BBF7A1CE368BA45FB7053CE4991B669
gpg --export 219F55740BBF7A1CE368BA45FB7053CE4991B669 | sudo apt-key add
sudo add-apt-repository -y ppa:certbot/certbot
```


### update libs again

`apt update`



### Once all of the files are gained from the list of the available software, you can download what is actually required by using this command:

`sudo apt -y install zen certbot`



### You can then run a Zen script that downloads required crypto-related files:

`zen-fetch-params`



### Once that completes, you need to start the zencash daemon or wallet, by using this command:

`zend`



### You can check if the daemon has started by using this command:

`zen-cli getinfo`


### navigate to ssl cert

`sudo certbot certonly -n --agree-tos --register-unsafely-without-email --standalone -d zensupernode.example.com`



### Make sure that you replace the example with the domain name you previously made.

**Then, run this command:**

`sudo cp /etc/letsencrypt/live/supernode1.voskcoin.io/chain.pem /usr/local/share/ca-certificates/chain.crt`



### OS now has access, next run

`sudo update-ca-certificates`



### replace supernode with your url

`echo "tlscertpath=/etc/letsencrypt/live/supernode1.voskcoin.io/cert.pem" >> ~/.zen/zen.conf`



### change perms

`sudo chown -R root:sudo /etc/letsencrypt/`



### Then chown the changes the owner from name to root:

`sudo chmod -R 750 /etc/letsencrypt/`



### Here, you need to use the zen-cli command to stop the daemon, wait 5 seconds, start the daemon, wait 30 seconds:

`zen-cli stop && sleep 5 && zend && sleep 30`



### next run

zen-cli getnetworkinfo

**If it works, you will be able to see the message:**
`tls_cert_verified”: true`



### Thats it, node is set up!



### Step 3: Generating a T Address for the Supernode
