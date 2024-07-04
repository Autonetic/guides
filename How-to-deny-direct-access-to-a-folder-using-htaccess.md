# How to deny direct access to a folder / directory using .htaccess
**linux**


When you have an Apache web server you may often find that you would like to protect a directory, for example the "/images" directory to prevent unauthorized scrolling and downloading of your images or other important files. there is a simple solution to rectify this situation.

## Step 1
- First, lets open a new terminal window and edit our main apache config, its good practice to backup important files like this before editing:
```
cp /etc/apache2/apache2.conf /etc/apache2/apache2-backup-1.conf
sudo nano /etc/apache2/apache2.conf
```
- Navigate down the file to an appropriate location, I chose just after all the "directory" blocks.
- Now paste the following, editing the directory you would like to protect:
```
<Directory /var/www/html/includes>
        Options -Indexes
</Directory>
```

## Step 2
- After editing the apache2.conf file, we need to reload apache web server for it to enable our changes, lets do a gentle, soft restart:
```
sudo service apache2 restart
```
- Thats it! If you now navigate to yourwebsite.tld/includes or yourwebsite.tld/includes/images etc. you will be met with a 403 Forbidden page!
- The best part about using this method as opposed to slapping an index.php file with a redirect, or an apache redirect is that we can still link to images in our website and display them, whilst protecting the folder in which they reside =)!
