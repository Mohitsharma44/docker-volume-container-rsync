# my_rsyncd

A volume container using rsync.

## Usage

- Spin up my_rsyncd container:
```shell
docker run --name my_rsyncd --volume <path_where_it_should_mount_in_other_container> -d -e ALLOW='172.17.0.0/16' -e VOLUME=<path_where_it_should_mount_in_other_container> -p 10873:873 mohitsharma44/my_rsyncd
```

- To sync the directory:
```shell
rsync avP <directory_to_be_synced> rsync://<ip_of_host_where_my_rsyncd_is_running>:10873/
```

- To use the volume in other container:
  - Using `docker-compose`:
    ```shell
    version: "2"
    services:
      ...
      ...
      volumes_from:
        - container: my_rsyncd
        ...
      ...

    ```
  - Using docker cli:
    ```shell
    docker run -it --volumes-from my_rsyncd <container_name> /bin/sh
    ```

--------
--------

> Old author's comments for reference
>First, you can launch a volume container exposing a volume with rsync.
>
>```sh
>CID=$(docker run -d -p 10873:873 nabeken/docker-volume-container-rsync:latest)
>```
>
>You can connect to rsync server inside a container like this:
>
>```sh
>rsync rsync://<docker>:10873/
>volume          docker volume
>```
>
>To sync:
>
>```sh
>rsync -avP /path/to/dir rsync://<docker>:10873/volume/
>```
>
>Next, you can launch a container connected with the volume under `/docker`.
>
>```sh
>docker run -it --volumes-from $CID ubuntu /bin/sh
>```
>
>## Advanced
>
>In default, rsync server accepts a connection only from `192.168.0.0/16` and `172.16.0.0/12` for security reasons.
>You can override via an environment variable like this:
>
>```sh
>docker run -d -p 10873:873 -e ALLOW='10.0.0.0/8 x.x.x.x/y' nabeken/docker-volume-container-rsync
>```
>
>## Using with different directory
>
>Let's say you want to use `/data` rather than `/docker`.
>
>First, you must launch a volume container exposing `/data` directory:
>
>```sh
>CID=$(docker run --volume /data -d -e VOLUME=/data -p 10873:873 nabeken/docker-volume-container-rsync)
>```
>
>Finally, you can mount the volumes from the container:
>
>```sh
>docker run -it --volumes-from $CID ubuntu /bin/sh
>```
