# gitlabCE_management_experience
This repository is to summarise my experience working on managing gitlab ce server in UbuntuOS, known as Omnibus gitlab. It includes tasks:
- [x] [Setup](https://github.com/nguyendinh1987/gitlab_management_experiment/blob/main/setup.md)
- [x] [Backup and restore](https://github.com/nguyendinh1987/gitlab_management_experiment/blob/main/backup_and_restore.md)
- [ ] [Upgrade](https://github.com/nguyendinh1987/gitlab_management_experiment/blob/main/Upgrade.md)
- [ ] [Migrate](https://github.com/nguyendinh1987/gitlab_management_experiment/blob/main/Migrate.md)

# Note:
- Things to do when you upgrade gitlab from version of 12.x.x to 13.x.x
  - Need to move legacy storages to hashed-stored. In version of 14.x.x, legacy storages will be removed completely.
  - How?
    - General check gitlab health status: gitlab-rake gitlab:check SANITIZE=true. Alls should be fine
    - Check pending task:
    - For background migration: gitlab-rails runner -e production 'puts Gitlab::BackgroundMigration.remaining'. The return message should be 0.
    - Moving all storages to hashed storages: [ref](https://gitlab.com/gitlab-org/gitlab-foss/-/blob/5cb94fc486b25f14d160a7a584dd9a9f23d1ccc9/doc/administration/repository_storage_types.md)
      - In gitlab UI (web browser): Go to Admin > Settings > Repository and expand the Repository Storage section. Then, select the Use hashed storage paths for newly created and renamed projects checkbox.
      - run: gitlab-rake gitlab:storage:migrate_to_hashed

