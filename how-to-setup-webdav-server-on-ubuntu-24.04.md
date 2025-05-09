Set Up WebDAV Server on Ubuntu 24.04

To set up a WebDAV server on Ubuntu 24.04, you need to follow these steps:

1. Install Apache2 and enable the WebDAV module:
   ```
   sudo apt-get update
   sudo apt-get install apache2
   sudo a2enmod dav
   sudo a2enmod dav_fs
   sudo systemctl restart apache2
   ```
   These commands update the package list, install Apache2, enable the WebDAV modules, and restart Apache2 to apply the changes

2. Create a WebDAV root directory:
   ```
   sudo mkdir /var/www/webdav
   sudo chown -R www-data:www-data /var/www/webdav
   ```
   This creates a directory for WebDAV content and sets the correct ownership for Apache2

3. Configure Apache2 to serve the WebDAV directory:
   Edit the Apache2 configuration file, typically located at `/etc/apache2/sites-available/000-default.conf`, and add the following inside the `<VirtualHost>` block:
   ```
   Alias /webdav /var/www/webdav
   <Directory /var/www/webdav>
       Options Indexes FollowSymLinks MultiViews
       AllowOverride None
       Require all granted
       Dav On
   </Directory>
   ```
   This configuration maps the URL `/webdav` to the `/var/www/webdav` directory and enables WebDAV functionality

4. Secure the WebDAV directory with password authentication:
   Create a password file using `htpasswd`:
   ```
   sudo htpasswd -c /etc/apache2/webdav.password username
   ```
   Then modify the configuration file to include the password file:
   ```
   <Directory /var/www/webdav>
       AuthType Basic
       AuthName "WebDAV"
       AuthUserFile /etc/apache2/webdav.password
       Require valid-user
       Dav On
   </Directory>
   ```
   This step adds basic authentication to protect the WebDAV directory

5. Restart Apache2 to apply the changes:
   ```
   sudo systemctl restart apache2
   ```
   This ensures that all configuration changes take effect

By following these steps, you can set up a secure WebDAV server on Ubuntu 24.04