# How to setup ZoneMinder to use a webcam on linux Ubuntu
**A quick guide on how to install and configure a webcam as a CCTV camera using ZoneMinder on Linux Ubuntu 22.04 LTS.**

1. **Update Libs**
- First run a simple update:
```
sudo apt-get update && sudo apt upgrade -y
```

2. **Add repo for ZoneMinder**
- Add the official repo to ensure most up-to-date software:
```
sudo apt install -y software-properties-common
sudo add-apt-repository ppa:iconnor/zoneminder-1.36
sudo apt update
```

3. **Install ZoneMinder**
```
sudo apt install -y zoneminder
```

4. **Enable Required Mods**
- Enable Apache mods, `rewrite`, `headers`, `cgi` and restart Apache:
```
sudo a2enmod rewrite headers cgi
sudo a2enconf zoneminder
sudo systemctl restart apache2
```

5. **Enable and Start ZoneMinder**
- Now we want to enable ZoneMinder, so it starts on a system reboot or shutdown, and then start it for us now too:
```
sudo systemctl enable zoneminder
sudo systemctl start zoneminder
```

6. **Install Video4Linux**
- This software is used by ZoneMinder to display the video from the attached webcam:
```
sudo apt install v4l-utils
```
- you can also find out device info using:
```
v4l2-ctl --list-devices
```
- You will need your device location, eg `/dev/video0`
- Now we can check the camera is working:
```
ffmpeg -f v4l2 -i /dev/video0 -vf "format=yuv420p" -f sdl -
```

7. **Setup ZoneMinder GUI**
- Open a browser and type [localhost/zm](localhost/zm)
- You will be taken to the web GUI, where we need to set up a few things, more detail can be found from [ZoneMinder's Website:](https://zoneminder.readthedocs.io/en/latest/userguide/gettingstarted.html#getting-started).
- Open Settings, setup your timezone first.
- Enable Authentication - setting title: `OPT_USE_AUTH`
- Add Hashing secret - setting title: `AUTH_HASH_SECRET`
_It's recommended by ZoneMinder to set up a user login to ensure camera's are properly protected._
- Login and change your password, default is username: `admin` password: `admin`.


8. **Ensure Correct Permissions**
- We have to make sure that the camera is accessible locally by our "video" user which was created during installation using the ZoneMinder repository:
```
sudo usermod -a -G video www-data
sudo chmod 777 /dev/video2
```

9. **Restart ZoneMinder**
- loginto the web portal and restart Zoneminder by clicking the top right icon that should say "RUNNING" and selecting restart, and apply.
