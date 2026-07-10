## Docker System Maintenance

* **`docker system df`** → Docker disk usage total.
* **`docker system df -v`** → Detailed disk usage like which image, container, and volume.
* `-v` = verbose


* **`docker system prune`** → Removes all stopped containers, unused networks, dangling images, and build cache.
* **`docker system prune -a`** → Removes all unused images, not just dangling ones.
* **Dangling** → Untagged / intermediate layers
* **Unused** → Any image not currently used by containers


* **`docker info`** → Shows detailed Docker engine / system information.
* **`docker volume prune`** → Removes unused volumes.
* **`docker container prune`** → Removes stopped containers.
* **`docker image prune`** → Removes dangling images.
* **`docker network prune`** → Removes unused networks.