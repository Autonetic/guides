# How to Secure Linux SSH with additional user

## Intro
  Often on Linux systems such as Ubuntu or Debian you will need to run commands with super user access, or perhaps you commonly use Root access? Then it is a really good idea to secure your system with an additional user that has sudo privileges.  
  
 ### Step 1 - Loggin via SSH  
   Log into your server with ssh:
  ```
 ssh root@ssh.example.com
 ```
   
  
 ### Step 2 - Update Libs and Create a Non-root User  
Update Libs:
```
apt-get install sudo
```

Set a $USERNAME environment variable
```
USERNAME=ops
```   
  
 Add non-root user to relevent groups:('adm', 'systemd-journal' and 'sudo' in this example.) and create a strong password
 
 ```
useradd -m -s /bin/bash -G adm,systemd-journal,sudo $USERNAME && passwd $USERNAME
```
  
 If you fail to enter the same password for the confirmation, use the following command to update your password
```
passwd $USERNAME
```

  Switch to the new user using the `SU` Command
  ```
 su $USERNAME
 ```  
  
 cd to new non-root users home directory:
  ```
 cd ~/
 ```
  
  
### Step 3 - Generate an SSH key-pair
  We need to disable password access via a user using SSH, this provides the ultmate security for your ssh user.
  **Make sure you are still logged in with your first terminal session from Step 2**
  
  in a **Second** terminal on your **Local** machine, check if there is an existing RSA key pair:
  ```
  ls -alh ~/.ssh/
 ```
  
  If your output contains an id_rsa and an id_rsa.pub as shown in the following example, skip the next step.
```
autonetix@localmachine:~$ ls -alh ~/.ssh/

total 80K

drwxr-xr-x  2 user user 4.0K May 28 13:47 .

drwx------ 19 user user 4.0K Jun  4 23:35 ..

-rw-------  1 user user 1.7K Oct 15  2022 id_rsa

-rw-r--r--  1 user user  394 Oct 15  2022 id_rsa.pub
```

 ### 3.1
 Generate an ssh key pair on your local device:
 >[!CAUTION]
 >Skip this step if you had an existing RSA key pair in the previous step
```
ssh-keygen -t rsa -b 2048
```
or using a more up to date algo:
```
ssh-keygen -t ed25519 -a 100
```
>[!INFORMATION]
>The -a option specifies 100 rounds of key deriverations, making your key's password harder to brute force.
 
 You will now be prompted for a file name, simply hit `Enter`
 
 Now enter a new password, different from your local user, the ssh user you created and your root ssh user
 >[!CAUTION]
 >Be carefull not to forget this password!

the new RSA key pair files will be stored in:
```
/home/<USER>/.ssh
```
***You should create an offline backup of these files!***

### 3.2
Copy the SSH public key to your server:
```
ssh-copy-id ops@ssh.example.com
```

***__if prompted for ECDSA fingerprint, answer yes__***

>[!IMPORTANT]
>If prompted to install new keys, do so by entering your non-root user password

### 3.3
Test access to your server via SSH with the key pair:
***Ensure you still have another terminal connected incase a mistake is made.***
```
ssh ops@ssh.example.com
```
```
exit
```
```
ssh ops@ssh.example.com
```

repeat this process several times, confirming you can in fact access the server via the new key pair.
>[!IMPORTANT]
>Use the password you created to generate the keypair

### Step 4 - Secure SSH Config

Edit sshd_config to secure the SSH daemon:
```
sudo sed -i '/PermitRootLogin/d' /etc/ssh/sshd_config && \
sudo sed -i '/PasswordAuthentication/d' /etc/ssh/sshd_config && \
sudo sed -i '/ChallengeResponseAuthentication/d' /etc/ssh/sshd_config && \
echo -e "PermitRootLogin no\nPasswordAuthentication no\nChallengeResponseAuthentication no" | sudo tee -a  /etc/ssh/sshd_config
```
>[!IMPORTANT]
>The RSA Key password is only used to login to your server, all other password prompts will require your non-root user password.

### 4.1
Restart SSH with the new configuration	
```
sudo systemctl restart sshd
```

***You're all done!***

You could now enable a firewall (UFW(Uncomplicated FireWall) is simple and effective for Ubuntu Systems) to gain some extra security, I will be posting about how to do this at a later date.

Thanks for reading!
