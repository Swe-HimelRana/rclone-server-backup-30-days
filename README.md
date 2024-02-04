rclone website https://rclone.org/

# rclone-server-backup-30-days
Backup your server using rclone for 30 days easy configuration

Features
  - It's backup your server specific folder and also deletes 1 months old files/folders form your cloud storage automatically
  - It's will upload only changed/new files in your server (no duplicates)
  - It's a simple bash script. You can easily change any option as you like

copy and save below code in your server `/usr/bin/rclone-backup`

change those two variable with your preferences 
```bash
local_dir="/home/dev/rclonetest" # your server folder that you want to backup
google_drive_parent_folder="gdrive:himosoft-minio-backup" # just change `himosoft-minio-backup` with your naming (anything) like `google_drive_parent_folder="gdrive:anything`
```


```bash
#!/bin/bash

# configurations
local_dir="/home/dev/rclonetest"
google_drive_parent_folder="gdrive:himosoft-minio-backup"

# copy new files or modify changed files
rclone copy --progress "$local_dir" "$google_drive_parent_folder/$(date +'%d-%m-%Y')"

# Find subfolders with names older than 30 days
old_subfolders=$(rclone lsf "$google_drive_parent_folder" | grep -E '^[0-9]{2}-[0-9]{2}-[0-9]{4}/' | awk -F '-' 'BEGIN { cm=strftime("%m") } {if (cm > $2) print $0}')

# Print old_subfolders
echo "Old Subfolders:"
echo "$old_subfolders"

# Delete each old subfolder
for subfolder in $old_subfolders; do
  echo "Deleting.. $subfolder"
  rclone purge "$google_drive_parent_folder/$subfolder"
  echo "Done"
done
```

for testing you can use below command for test upload files/folders on your server
```bash
rclone copy --progress /home/dev/rclonetest gdrive:himosoft-minio-backup/04-01-2024

used date-month-year (%d-%m-%Y) in script configuration. follow this folder naming.
```

make sure `rclone-backup` has executable permission run `chmod +x /usr/bin/rclone-backup`

# Corn Job
The cron job frequency determines the frequency of data backups. Since the script uploads files to a cloud storage provider and issues requests via an API, excessive requests may lead to key blocking by the storage provider. Hence, it is recommended to set the cron job for every hour. However, if you closely monitor the system and aim to minimize data loss in the event of a server crash, you can opt for more frequent cron jobs, such as every minute or every five minutes. Rest assured, the script is designed to prevent duplicate uploads, ensuring that the same file is not repeatedly uploaded. Feel free to configure the cron job according to your API request permissions from the storage provider. Let's proceed with setting up the cron job.

## Run one of those following command

# Once in a day ( Once in 24 hours) 
- Will run on every day at 02:30 PM (at your server time) 
```bash
(crontab -l ; echo "30 14 * * * /usr/bin/rclone-backup") | crontab -
```
For verifying it run `crontab -l` if you see `/usr/bin/rclone-backup` in list it's successfull
Done

# Every 1 hour backup (recomended)
```bash
(crontab -l ; echo "0 * * * * /usr/bin/rclone-backup") | crontab -
```
For verifying it run `crontab -l` if you see `/usr/bin/rclone-backup` in list it's successfull
Done

# Every 30 min backup 
```bash
(crontab -l ; echo "*/30 * * * * /usr/bin/rclone-backup") | crontab -
```
For verifying it run `crontab -l` if you see `/usr/bin/rclone-backup` in list it's successfull
Done
# Every 10 min backup
```bash
(crontab -l ; echo "*/10 * * * * /usr/bin/rclone-backup") | crontab -
```
For verifying it run `crontab -l` if you see `/usr/bin/rclone-backup` in list it's successfull
Done

# Every 5 min backup 
```bash
(crontab -l ; echo "*/5 * * * * /usr/bin/rclone-backup") | crontab -
```
For verifying it run `crontab -l` if you see `/usr/bin/rclone-backup` in list it's successfull
Done

# In Every 1 min backup (not recomended)

```bash
(crontab -l ; echo "* * * * * /usr/bin/rclone-backup") | crontab -
```
For verifying it run `crontab -l` if you see `/usr/bin/rclone-backup` in list it's successfull
Done
