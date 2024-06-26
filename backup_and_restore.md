# Notes: 
- You can only backup and restore within a single version of gitlab server. For example, the backup file generated by version 12.0.0 can only be used to retore on the exact same version, as 12.0.0. If you use for different versions, something might get wrong.
- In this summary, I only backup very minimum items from configuration directory, as /etc/gitlab/gitlab.rb and /etc/gitlab/gitlab-secrets.json. You might consider to backup other files, following information in [gitlab guide](https://docs.gitlab.com/ee/administration/backup_restore/).
- Remove "sudo" if you work interactively with docker container
# References:
[gitlab guide](https://docs.gitlab.com/ee/administration/backup_restore/)
# Backup: 
- Use command: using STRATEGY to avoid backup fail due to backing up while files are being changed. There are a lot of other options for this command, such as backup file name, type of compression. Please see the reference. By default, gitlab will backup to tar file. The output file will be in /etc/gitlab/backups
  >> sudo gitlab-backup create STRATEGY=copy
- Copy three files: /etc/gitlab/backups, /etc/gitlab/gitlab.rb and /etc/gitlab/gitlab-secrets.json to your backup folder. 
# Restore:
- Can be used on the current executing gitlab server to retrive back to working stage when something gets wrong, or can be use to migrate to other machines. Note that destination gitlab server in the other machines should be exact the same version with the backup files.
- Procedure:
  - Preparation: make sure that:
    - The destination gitlab server has the same version with the backup file
    - The destination gitlab server is running. If not, execute: "sudo gitlab-ctl start"
  - Copy the backed up configuration files (very minimum opt has gitlab.rb and gitlab-secrets.json) to the /etc/gitlab of the destination gitlab server
  - Reconfigure the gitlab server with the updated configuration files
    >> sudo gitlab-ctl reconfigure
  - Copy the backuped compressed file to /var/opt/gitlab/backups of the destination gitlab server, and change the owner of the file. For example, below works with the backup file "11493107454_2018_04_25_10.6.4-ce_gitlab_backup.tar"
    >> sudo chown git:git /var/opt/gitlab/backups/11493107454_2018_04_25_10.6.4-ce_gitlab_backup.tar
  - Stop the processes that are connected to the database. Leave the rest of GitLab running:
    >> sudo gitlab-ctl stop puma  
    >> sudo gitlab-ctl stop sidekiq  
    >> \# Verify by    
    >> sudo gitlab-ctl status
  - Restore the backup file using backup file ID
    >> \#This command will overwrite the contents of your GitLab database!  
    >> \#NOTE: "_gitlab_backup.tar" is omitted from the name  
    >> sudo gitlab-backup restore BACKUP=11493107454_2018_04_25_10.6.4-ce
  - Restart and verify the restored process:
    >> sudo gitlab-ctl restart  
    >> sudo gitlab-rake gitlab:check SANITIZE=true  
    >> \# In GitLab 13.1 and later, check database values can be decrypted especially if /etc/gitlab/gitlab-secrets.json was restored, or if a different server is the target for the restore.  
    >> sudo gitlab-rake gitlab:doctor:secrets  
    >> \# For added assurance, you can perform an integrity check on the uploaded files:  
    >> sudo gitlab-rake gitlab:artifacts:check  
    >> sudo gitlab-rake gitlab:lfs:check  
    >> sudo gitlab-rake gitlab:uploads:check  
