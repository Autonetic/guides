# How to create a mail server on your Linux system!
### including DNS settings and Postfix installation

Setting up a mail server in linux can be an involved process and manually doing it yourself can seem impossible, so I have compiled these instructions, sourced from around the web as a reference for both myself and others when creating a fully functioning mail server.


This guide assumes that you have set up a dedicated or VPS with linux Ubuntu 22.04, however the guide may help you if you are using a different distro too.

## Step 1 - DNS
- Firstly, we need to decide on a domain that will act on behalf of the mail server, lets choose `mail.yourdomain.com` for this guide.
- We now need to set the DNS records for this domain to tell queries to and from the server which domain/ip is responsible for the request.
- Depending on your domain registrar, there are a couple of options for setting DNS records, Look for "DNS Hosting" which should usually be free from your registrar. Failing this, you could create a free Cloudflare account and use their service to route your DNS using their nameservers OR install Bind9 on your linux system and host your own DNS server. This guide will only cover the first option at the moment.
- Log into your registrar and go to your DNS records, we need to create two new records for this domain.
```
Type	Hostname		Value			Prio	TTl
A	mail.yourdomain.com	1xx.1xx.1xx.1xx		0 	14400
MX	yourdomain.com 		mail.dreadnet.au 	5 	14400
```

## Step 2 - Installing and configuring Postfix
- Now we need to install Postfix and then configure it correctly.
```
sudo apt update -y && sudo apt upgrade -y
sudo apt install postfix
```
- You can accept default values for now or if you have the required information on hand, fill in your details.
- To edit these values in the future run `sudo dpkg-reconfigure postfix`
- Here are some values incase you get lost in reconfig: (from https://ubuntu.com/server/docs/install-and-configure-postfix)
```
Internet Site
mail.yourdomain.com
steve
mail.yourdomain.com, localhost.localdomain, localhost
No
127.0.0.0/8 \[::ffff:127.0.0.0\]/104 \[::1\]/128 192.168.0.0/24
0
+
all
```
- Lets configure the mailbox format like seen on the ubantu docs: `sudo postconf -e 'home_mailbox = Maildir/'`
> [!TIP]
> If you need your network ip and class range, you can find them by running `ip a` or `ifconfig`

## Step 3 - Generating an SSL/TLS certificate
- There are a couple of ways of doing this, either with a self-signed certificate which should be sufficient for most use cases, a paid certificate, or a certificate from a trusted CA (certificate authority) such as LetsEncrypt. This guide will cover both self-signed and LetsEncrypt using Certbot.
### Lets Encrypt / Certbot 
- First ensure all previous installations that you didnt make are removed, some servers come with pre loaded features and applications instlled, so if this is the case, run these commands first as suggested by Certbot themselves.
```
sudo apt remove certbot 
```
- Next update Snap package manager, if you haven't already.
```
sudo snap install core; sudo snap refresh core
```
- Next lets install Certbot from snap.
```
sudo snap install --classic certbot
```
- Now we can link Certbot to a command we can call from the terminal:
```
sudo ln -s /snap/bin/certbot /usr/bin/certbot
```
- You will need to stop your server whilst performing the certificate request:
```
sudo systemctl stop apache2
```
- We can now actually run the Certbot command to request a new certificate for our mail server.
```
sudo certbot certonly --standalone -d mail.yourdomain.com
```
- You should see a bunch of output like this:
```
Saving debug log to /var/log/letsencrypt/letsencrypt.log
Requesting a certificate for mail.dreadnet.au

Successfully received certificate.
Certificate is saved at: /etc/letsencrypt/live/mail.yourdomain.com/fullchain.pem
Key is saved at:         /etc/letsencrypt/live/mail.yourdomain.com/privkey.pem
This certificate expires on 2024-07-19.
These files will be updated when the certificate renews.
Certbot has set up a scheduled task to automatically renew this certificate in the background.

- - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - -
If you like Certbot, please consider supporting our work by:
 * Donating to ISRG / Let's Encrypt:   https://letsencrypt.org/donate
 * Donating to EFF:                    https://eff.org/donate-le
- - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - -
```
- You can see the location of the saved certificates here, we will need this for configuring Postfix to provide TLS encryption for both in and out requests.
- You can start your server again using:
```
sudo systemctl start apache2
```
- If you wish, you can copy/move the Certificates to your ssl directory. `sudo cp /etc/letsencrypt/live/mail.yourdomain.com/privkey.pem /etc/ssl/private/mail.privkey.pem && sudo cp /etc/letsencrypt/live/mail.yourdomain.com/fullchain.pem /etc/ssl/certs/mail.fullchain.pem`
- Lets run the following commands now to apply our certificates:
```
sudo postconf -e 'smtp_tls_security_level = may'
sudo postconf -e 'smtpd_tls_security_level = may'
sudo postconf -e 'smtp_tls_note_starttls_offer = yes'
sudo postconf -e 'smtpd_tls_key_file = /etc/ssl/private/mail.privkey.pem'
sudo postconf -e 'smtpd_tls_cert_file = /etc/ssl/certs/mail.fullchain.pem'
sudo postconf -e 'smtpd_tls_loglevel = 1'
sudo postconf -e 'smtpd_tls_received_header = yes'
sudo postconf -e 'myhostname = mail.yourdomain.com'
```
- Because we are using a CA, we will need to also run:
```
sudo postconf -e 'smtpd_tls_CAfile = /etc/ssl/certs/mail.fullchain.pem'
```
- After all of this, we should have now enabled SMTP-AUTH correctly, you can double check your values, and/or edit them in the `/etc/postfix/main.cf` file.
- Restart Postfix service:
```
sudo systemctl restart postfix.service
```
### Self-Signed certificate
- Essentially the same as aboce, however we need to generate a certificate.. this way should work for most cases, however a self-signed certificate is considered less secure as we are declaring that we are real ourselves rather than using a CA to provide this service for us. Considering this, lets continue.
