## Docker Registry Operations

* **`docker login`** $\rightarrow$ Login to Docker Hub.
* **`docker logout`** $\rightarrow$ Logout from Docker Hub.
* **`docker push username/repository`**
* **`docker tag bike-showroom:latest balu98578/bike-showroom:latest`** $\rightarrow$ This creates a new image name: `balu98578/bike-showroom:latest`.
* **`docker push balu98578/bike-showroom:latest`**
* **Without tagging, `docker push` does not work.**
* **`docker pull balu98578/bike-showroom:latest`**
* **`docker search nginx`**
* **`docker rmi balu98578/bike-showroom:latest`** $\rightarrow$ Removes the local tagged image only. We have a copy of this in Docker Hub and the original copy locally.