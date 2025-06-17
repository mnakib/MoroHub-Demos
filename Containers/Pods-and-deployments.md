# Lab Preparation

- Login to the OpenShift cluster on `https://api.ocp4.example.com:6443` using user `admin` and password `redhatocp`

```bash
$ oc login -u admin -p redhatocp httpd](https://api.ocp4.example.com:6443
```

- Create a namespace called `moro-web`

```bash
$ oc new-project moro-web
```

# Live Demonstration 1: Create a Pod 

- Create a pod with the following settings:
- 
    - Name: moro-web-1
    - Use the Apache httpd image from Docker Hub

```bash
$ oc run moro-web-1 --image=docker.io/httpd
```

- Check the created pod
  
```bash
$ oc get pods
```


# Live Demonstration 2: Create a Deployment

- Create a Deployment with the following settings:

-- name: moro-web-1
-- Use the Apache httpd image from quay.io/fedora/httpd-24 

```bash
$ oc create deployment moro-web-1 --quay.io/fedora/httpd-24
```

- Check the created deployments and pods
  
```bash
$ oc get deployment,pods
```

- Run commands against the deployment's pod
  
```bash
$ oc exec <PodName> -- ls
```

```bash
$ oc exec <PodName> -- hostname
```

- Run an interactive Bash shell terminal inside the deployment's pod
  
```bash
$ oc exec -it <PodName> -- /bin/bash
```

