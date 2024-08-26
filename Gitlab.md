# Gitlab

## Create backup in docker-compose
```
#!/usr/bin/env bash
set -eu

export BACKUP_FOLDER=/opt/gitlab/data/backups
export CONTAINER_NAME=gitlab

echo "[$(date)] remove old backup"
find ${BACKUP_FOLDER} -name '*_gitlab_backup.tar' -delete -print
rm -v ${BACKUP_FOLDER}/gitlab_backup.tar.sha512sum || true
rm -v ${BACKUP_FOLDER}/gitlab_backup.log || true
echo "[$(date)] create new backup"
docker-compose exec -it ${CONTAINER_NAME} gitlab-backup create SKIP=registry > ${BACKUP_FOLDER}/gitlab_backup.log
echo "[$(date)] create sha512sum "
find ${BACKUP_FOLDER} -name '*_gitlab_backup.tar' -exec sha512sum {} + > ${BACKUP_FOLDER}/gitlab_backup.tar.sha512sum
echo "[$(date)] done"

```
## Registry garbage collect
```bash
#!/usr/bin/env bash
set -eu

export CONTAINER_NAME=gitlab

echo "[$(date)] Start cleaning registry"
echo "[$(date)] Remove unreferenced layers"
docker-compose exec -it ${CONTAINER_NAME} gitlab-ctl registry-garbage-collect > /dev/null
echo "[$(date)] Remove untagged manifests and unreferenced layers"
docker-compose exec -it ${CONTAINER_NAME} gitlab-ctl registry-garbage-collect -m > /dev/null
echo "[$(date)] Done"
```
