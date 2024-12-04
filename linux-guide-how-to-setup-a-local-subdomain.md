# Linux Ubuntu Local Subdomain Setup Example.

To set up a local subdomain on Ubuntu server / desktop with Apache2, follow these steps:

1. **Edit the `/etc/hosts` file**:
Add the subdomain to the `/etc/hosts` file by running the following command:
```bash
sudo nano /etc/hosts
```
Add the following lines at the end of the file:
```markdown
127.0.0.1   autonetix.yourdomain.local
```
Replace `autonetix` with your desired subdomain name and `yourdomain.local` with your local domain name.

2. **Create a new Apache virtual host**:
- Create a new directory to store your web files in:
```bash
sudo mkdir /var/www/autonetix.yourdomain.local
```
- Create a new file in the `/etc/apache2/sites-available` directory:
```bash
sudo nano /etc/apache2/sites-available/autonetix.yourdomain.local.conf
```
- Add the following configuration:
```apache
<VirtualHost *:80>
    ServerAdmin [your_email@example.com](mailto:your_email@example.com)
    ServerName autonetix.yourdomain.local
    DocumentRoot /var/www/autonetix.yourdomain.local
</VirtualHost>
```
- Replace `your_email@example.com` with your email address and `/var/www/autonetix.yourdomain.local` with the directory containing your subdomain's files.

3. **Enable the new virtual host**:
Enable the new configuration by running:
```bash
sudo a2ensite autonetix.yourdomain.local
```
4. **Restart Apache**:
Restart Apache to apply the changes:
```bash
sudo service apache2 restart
```
5. **Verify the setup**:
Open a web browser and navigate to `http://autonetix.yourdomain.local`. If everything is set up correctly, you should see your subdomain's files.


Remember to replace `yourdomain.local` with your actual local domain name and `autonetix` with your desired subdomain name throughout the process.
