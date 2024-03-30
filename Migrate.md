# Migrating gitlab server to other machines
It is similar to backup and restore but now the destination gitlab server is in another machine. Before migrating, making sure that the destination gitlab server should be exact the same version with the source gitlab server that will creates backup files.
- Procedure:
  - In source gitlab server: Create backup files [backup_and_restore](https://github.com/nguyendinh1987/gitlab_management_experience/blob/main/backup_and_restore.md)
  - Copy backed up files to the destination gitlab server
  - Following restoring procedure in [backup_and_restore](https://github.com/nguyendinh1987/gitlab_management_experience/blob/main/backup_and_restore.md)
