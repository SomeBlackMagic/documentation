#Docker 

## Remove unused images
``` bash
#!/bin/bash
set -eu

# Функция для удаления неиспользуемых образов
cleanup_unused_images() {
    echo "[$(date)] Start removing unused images"

    # Список образов, которые используются в контейнерах
    used_images=$(docker ps --format '{{.Image}}' | sort | uniq)

    # Список всех образов
    all_images=$(docker images --format '{{.Repository}}:{{.Tag}}' | sort | uniq)

    # Проход по всем образам
    for image in $all_images; do
        if echo "$used_images" | grep -q "$image"; then
            echo "[$(date)] Image $image is in use, skipping"
        else
            echo "[$(date)] Image $image is not in use, deleting"
            docker rmi "$image"
        fi
    done

    echo "[$(date)] Cleanup finished"
}

echo "[$(date)] Start docker cleanup"
cleanup_unused_images
echo "[$(date)] done"
```
## Remove all docker cache

```bash
#!/bin/bash
set -eu
echo "[$(date)] Start image prune"
/usr/bin/docker image prune --all --force > /dev/null 2>&1
echo "[$(date)] Start docker system prune"
/usr/bin/docker system prune --force > /dev/null 2>&1
echo "[$(date)] Remove layers2"
systemctl stop docker.socket
service docker stop
rm -rf /var/lib/docker
systemctl start docker.socket
service docker start
echo "[$(date)] done"
```

