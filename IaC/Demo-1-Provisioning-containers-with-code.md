# Live Demonstration 1: Provisioning Containers with Code

```bash
$ cat apache-deployment.yaml
```
```yaml
apiVersion: apps.openshift.io/v1
kind: Deployment
metadata:
  name: apache-app
spec:
  replicas: 1
  selector:
    app: apache-app
  template:
    metadata:
      labels:
        app: apache-app
    spec:
      containers:
      - name: apache
        image: quay.io/fedora/httpd-24
        ports:
        - containerPort: 8080
---
apiVersion: v1
kind: Service
metadata:
  name: apache-service
spec:
  selector:
    app: apache-app
  ports:
    - protocol: TCP
      port: 8080
      targetPort: 8080
```

```bash
$ cat apache-route.yaml
```
```yaml
apiVersion: route.openshift.io/v1
kind: Route
metadata:
  name: apache-route
spec:
  to:
    kind: Service
    name: apache-service
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
$ oc apply -f apache-deployment.yaml
```

Publish the deployment using an Edge route

```bash
$ oc apply -f apache-route.yaml
```

Show `oc get all` to confirm resources.




