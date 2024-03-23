# How to create a new user and add them to www-data group
### This guide ensures correct user permissions, allowing the new user to log in and edit files located in /var/www directories


## Step 1
- Firstly - Log in to a new shell window or open the terminal.
- Now, lets set an enviroment variable in the current shell window: 
```
NAME=ops
```

## Step 2
- These next set of commands can be run co-currently but first lets see what each command does.
- This next command creates a new user and adds them to the groups `adm` & `sudo`, followed by the `passwd` command that allows us to set a password for this new user:
```
sudo useradd -m -s /bin/bash -G adm,sudo $NAME && sudo passwd $NAME
```

## Step 3
- Now we want to change the primary group of this new user to `www-data`:
```
sudo usermod -g www-data $NAME
```

## Step 4
- Next we can check that our current changes have worked:
```
groups $NAME
```

## Step 5
- And finaly, we want to change the group permissions of the /var/www folder to ensure we can freely edit files contained in these locations at will, either by logging in with the new user, or switrching to them using the `su $NAME` command, where `$NAME` is the new username you created.
```
sudo chown -R www-data:www-data /var/www/html
sudo chmod -R g+w /var/www/html
```

