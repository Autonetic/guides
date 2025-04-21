To add another SSH key pair to a Linux server when plain text passwords are disabled, you can follow these steps:

1. Generate a new SSH key pair on your local machine using the `ssh-keygen` command. Ensure you specify a file name for the key, for example, `id_rsa_new`:
   ```
   ssh-keygen -t rsa -b 4096 -f ~/.ssh/id_rsa_new -C "your_email@example.com"
   ```

2. Copy the public key to the server using the `ssh-copy-id` command. Replace `username` and `hostname` with your actual username and server hostname:
   ```
   ssh-copy-id -i ~/.ssh/id_rsa_new.pub username@hostname
   ```
   If `ssh-copy-id` is not available, you can manually append the public key to the `~/.ssh/authorized_keys` file on the server:
   ```
   cat ~/.ssh/id_rsa_new.pub | ssh username@hostname 'cat >> ~/.ssh/authorized_keys'
   ```

3. Ensure the permissions on the `~/.ssh` directory and `~/.ssh/authorized_keys` file are set correctly:
   ```
   chmod 700 ~/.ssh
   chmod 600 ~/.ssh/authorized_keys
   ```

4. If you have multiple keys and want to specify which key to use for a particular server, you can configure your SSH client by editing the `~/.ssh/config` file. Add a section for the specific server and specify the key to use:
   ```
   Host hostname
       IdentityFile ~/.ssh/id_rsa_new
   ```



