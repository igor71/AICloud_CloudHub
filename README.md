## Cloud-Hub
### Running docker image on the host:
`nvidia-docker run -d -p 37001:22 --name cloud-hub -v /media:/media cloud_hub:0.0`
`nvidia-docker run -d -p 37001:22 --name tflow_build -v /media:/media gm-tf-2.7:0.0`
### Enter to the docker image:
`docker-enter tflow_build`
`docker exec -it tflow_build /bin/bash`
### Running application inside docker container:
`docker exec -it tflow_build python /gpu_tf_check.py`
`docker exec -it tflow_build python -c "import tensorflow; print(tensorflow.__version__)" > tflow.txt`
### Enter to the running container (for administrative purposes, cannot run tensorflow in this mode)
```
docker ps
PID=$(docker inspect --format '{{.State.Pid}}' tflow_build)
sudo nsenter --target $PID --mount --uts --ipc --net --pid
```
