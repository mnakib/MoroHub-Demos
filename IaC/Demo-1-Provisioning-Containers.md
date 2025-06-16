# Live Demonstration 1: Provisioning Containers with Code

```bash
$ cat nginx-deployment.yaml
```
```yaml
apiVersion: apps.openshift.io/v1
kind: Deployment
metadata:
  name: nginx-app
spec:
  replicas: 1
  selector:
    app: nginx-app
  template:
    metadata:
      labels:
        app: nginx-app
    spec:
      containers:
      - name: nginx
        image: nginx:latest
        ports:
        - containerPort: 8080
---
apiVersion: v1
kind: Service
metadata:
  name: nginx-service
spec:
  selector:
    app: nginx-app
  ports:
    - protocol: TCP
      port: 8080
      targetPort: 8080
```

```bash
$ cat nginx-route.yaml
```
```yaml
apiVersion: route.openshift.io/v1
kind: Route
metadata:
  name: nginx-route
spec:
  to:
    kind: Service
    name: nginx-service
  port:
    targetPort: 8080
  tls:
    termination: edge # Optional: for HTTPS
```

Commands to run

Login to OpenShift

```bash
$ oc login --token=<your-token> --server=<your-openshift-url>

Create a working project, if not already existing
```
```bash
$ oc new-project my-iac-project
```

Create the deployment

```bash
$ oc apply -f nginx-deployment.yaml
```

Publish the deployment using an Edge route

```bash
$ oc apply -f nginx-route.yaml
```

Show `oc get all` to confirm resources.




