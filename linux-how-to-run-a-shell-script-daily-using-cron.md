# Run A Shell Script Daily with Cron.

To run a shell script daily using crontab on Linux , follow these steps:

1. **Create a new shell script**: Write your desired commands in a new file with a `.sh` extension, e.g., `daily_script.sh`. Make sure the script has execute permissions by running `chmod +x daily_script.sh`.
2. **Open crontab**: As the root user, open the crontab file using `sudo crontab -e`. This will launch your default text editor.
3. **Add a new cron job**: In the crontab file, add a new line with the following format:
```bash
minute hour day month day_of_week command
```
For a daily run at midnight, use:
```bash
0 0 * * * /path/to/daily_script.sh
```
Replace `/path/to/daily_script.sh` with the actual path to your script file.

Here's a breakdown of the fields:

* `minute`: 0 (runs at the beginning of the minute)
* `hour`: 0 (midnight)
* `day`: `*` (any day of the month)
* `month`: `*` (any month)
* `day_of_week`: `*` (any day of the week)
* `command`: the path to your shell script

4. **Save and exit**: Save the changes to the crontab file and exit the editor.
5. **Verify the cron job**: Check the cron logs (usually `/var/log/cron` or `/var/log/syslog`) to ensure the job is running correctly.

Tips:

* Make sure the script has execute permissions (`chmod +x`) and is located in a directory accessible by the cron daemon.
* If your script requires specific environment variables or dependencies, ensure they are set up correctly.
* You can add multiple cron jobs by adding new lines to the crontab file.
* To disable email output for a specific cron job, add `>/dev/null 2>&1` at the end of the command line.

Example crontab entry:
```bash
0 0 * * * /home/user/daily_script.sh > /dev/null 2>&1
```
This will run the `daily_script.sh` script at midnight every day, and redirect any output to `/dev/null` to suppress email notifications.