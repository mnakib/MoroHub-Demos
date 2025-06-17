Run the MySQL container and check that it is running

```bash
$ podman run --name db -d -e MYSQL_ROOT_PASSWORD=root -e MYSQL_USER=ilia -e MYSQL_PASSWORD=pass -e MYSQL_DATABASE=wpdb mysql
```

```bash
$ podman exec -it db /bin/bash
# env
```

Get the IP address of the `db` container

```bash
podman inspect db
```

Pull the WordPress image and run a container named `wp` with a port redirection from 8080 on the host to port 80 to container

```bash
podman pull wordpress
podman run --name wp -d -p 8080:80 wordpress

```

## Deploy a containerized WordPress/MySQL environment in OpenShift

Login to the OpenShift cluster

```bash
$ oc login -u admin -p redhat https://api.ocp4.example.com:6443
```
Create a new project named test

```bash
$ oc new-project test
```
Create the MySQL deployment

```bash
$ oc create deployment db --image=docker.io/mysql

```
Inject the necessary environment variables

```bash
$ oc set env deployment/db MYSQL_ROOT_PASSWORD=rootpass MYSQL_USER=ilia MYSQL_PASSWORD=pass MYSQL_DATABASE=wpdb

```

Check that the env variables have been injected

```bash
$ oc exec -it db-7f99578c7b-lj2x2 -- env

```

Create a service for the db deployment

```bash
$ oc expose deployment db --port 3306
```

Create a WordPress deployment using the `S2I` method

```bash
$ oc new-app https://github.com/WordPress/WordPress.git
```

Create a service for the `wordpress` deployment

```bash
$ oc expose deployment wordpress --port 80
```

Create a route by exposing the `wordpress` service

```bash
$ oc expose service wordpress
```

```bash
$ oc get routes
```

Open a browser window and paste the route fqdn to see the wordpress portal

```bash
$ firefox wordpress-test.apps.ocp4.example.com &
```
