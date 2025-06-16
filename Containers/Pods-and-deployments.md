# Live Demonstration 1: Create a Pod 


- Login to the OpenShift cluster on https://api.ocp4.example.com:6443 using user `admin` and password `redhatocp`
- Create a pod with the following settings
-- name: moro-web-1
-- Use the Apache httpd image from Docker Hub 

```bash
$ oc run moro-web-1 --image=docker.io/httpd
```


# Live Demonstration 1: Create a Deployment

- Login to the OpenShift cluster on https://api.ocp4.example.com:6443 using user `admin` and password `redhatocp`

- Create a namespace called `moro-web`

```bash
$ oc new-project moro-web
```

- Create a Deployment with the following settings:

-- name: moro-web-1
-- Use the Apache httpd image from quay.io/fedora/httpd-24 

```bash
$ oc create deployment moro-web-1 --quay.io/fedora/httpd-24
```