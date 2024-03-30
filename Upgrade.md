# Upgrading gitlab version
Upgrading gitlab version is not straight forward when we have a major upgrade from outdated version to the latest one. We have to go through a series of version called "version stations". Please use this [upgrade_path_tools](https://gitlab-com.gitlab.io/support/toolbox/upgrade-path/) to find a series of versions we need to go through to upgrade from the current version to the latest ones.  

# Reference:
[upgrade gitlab](https://docs.gitlab.com/ee/update/index.html#upgrade-paths)

# Things to note before starting:
- Identify a supported upgrade path. The last minor release of the previous major version is always a required stop due to the background migrations being introduced in the last minor version. Use [upgrade_path_tools](https://gitlab-com.gitlab.io/support/toolbox/upgrade-path/).
- Ensure that any background migrations have been fully completed before upgrading to a new major version. Especially, when we start upgrading from version 14.x.x, in which background migration might take a day to complete.
- If you have enabled the Elasticsearch integration, then before proceeding with the major version upgrade, ensure that all advanced search migrations are completed. [link](https://docs.gitlab.com/ee/update/index.html#checking-for-pending-advanced-search-migrations)
- If your GitLab instance has any runners associated with it, it is very important to upgrade them to match the current GitLab version. This ensures compatibility with GitLab versions. [link](https://docs.gitlab.com/runner/#gitlab-runner-versions)

# Procedure:
- Back up the current version [backup_and_restore](https://github.com/nguyendinh1987/gitlab-management-experience/blob/main/backup_and_restore.md)
- Check background migration processes:
- Upgrade:
  - Loop: upgrade -> wait for backgroung migration process finished -> verify -> upgrade to another version station:
  - Upgrade:
    - If following gitlab package install:
      >> sudo apt install gitlab-ce=\<version\>- e.0
    - If following docker container install: Use docker container script in [setup](https://github.com/nguyendinh1987/gitlab-management-experience/blob/main/setup.md), and change docker image version to the targeted version
  - Verify:
    - Run reconfigure one time:
      >> sudo gitlab-ctl reconfigure
    - 

# Note:
- Things to do when you upgrade gitlab from version of 12.x.x to 13.x.x
  - Need to move legacy storages to hashed-stored. In version of 14.x.x, legacy storages will be removed completely.
  - How?
    - General check gitlab health status: gitlab-rake gitlab:check SANITIZE=true. Alls should be fine
    - Check pending task:
    - For background migration: gitlab-rails runner -e production 'puts Gitlab::BackgroundMigration.remaining'. The returned message should be 0.
    - Moving all storages to hashed storages: [ref](https://gitlab.com/gitlab-org/gitlab-foss/-/blob/5cb94fc486b25f14d160a7a584dd9a9f23d1ccc9/doc/administration/repository_storage_types.md). Using one of below two opts.
      - In gitlab UI (web browser): Go to Admin > Settings > Repository and expand the Repository Storage section. Then, select the Use hashed storage paths for newly created and renamed projects checkbox.
      - run: gitlab-rake gitlab:storage:migrate_to_hashed
- Things to note when you upgrade gitlab from versions of 14.x.x and later
  - Before upgrading, please wait for all process of background migration finished, [ref](https://docs.gitlab.com/ee/update/background_migrations.html). Some installations may need to run GitLab 14.0 for at least a day to complete the database changes introduced by that upgrade.
  - How?. Using one of two below opts
    - Using gitlab UI (web browser): Menu->adminArea->Monitoring->Background Migrations
    - Using console: gitlab-rails runner -e production 'puts Gitlab::Database::BackgroundMigration::BatchedMigration.queued.count'. The returned message should be 0.
