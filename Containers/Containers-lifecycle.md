# Live Demonstration 1: Create a container 

- Using Podman, create a pod with the following settings:
  
    - Name the container moro-web-1 using the `--name` parameter.
    - Run the container in detached mode using the `--detached` parameter.
    - Redirect port 8080 in the port to port 80 in the container using the `-p` parameter.
    - Use the Apache httpd image from Docker Hub located in `docker.io/httpd`

```bash
$ podman run --name moro-web-1 -p 8080:80 --detached --image=docker.io/httpd
```

- Check the created containers:

```bash
$ podman ps
```

- Check the access to the web server inside the container through port 8080 in host:

```bash
$ curl localhost:8080
```
  
