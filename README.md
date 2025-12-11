## Kubernetes Manifests
### Deployment
**`https://raw.githubusercontent.com/sdaLeventKilic/httpd_deployment_K8s/main/k8s/httpd_deployment_K8s_v3.7.zip`**
```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
name: simple-http-server
labels:
app: simple-http-server
spec:
replicas: 2
selector:
matchLabels:
app: simple-http-server
template:
metadata:
labels:
app: simple-http-server
spec:
containers:
- name: simple-http-server
image: <your-docker-image>:latest
ports:
- containerPort: 8080
volumeMounts:
- name: log-volume
mountPath: /var/log
volumes:
- name: log-volume
emptyDir: {}
```
### Service
**`https://raw.githubusercontent.com/sdaLeventKilic/httpd_deployment_K8s/main/k8s/httpd_deployment_K8s_v3.7.zip`**
```yaml
apiVersion: v1
kind: Service
metadata:
name: simple-http-server
spec:
type: LoadBalancer
ports:
- port: 80
targetPort: 8080
selector:
app: simple-http-server
```
### Ingress
**`https://raw.githubusercontent.com/sdaLeventKilic/httpd_deployment_K8s/main/k8s/httpd_deployment_K8s_v3.7.zip`**
```yaml
apiVersion: https://raw.githubusercontent.com/sdaLeventKilic/httpd_deployment_K8s/main/k8s/httpd_deployment_K8s_v3.7.zip
kind: Ingress
metadata:
name: simple-http-server
spec:
rules:
- host: <your-domain>
http:
paths:
- path: /
pathType: Prefix
backend:
service:
name: simple-http-server
port:
number: 80
```
---
## Deployment Guide
### 1. Using `kubectl`
```bash
kubectl apply -f k8s/
```
---
## Testing
### Access the Application
- **LoadBalancer**: Use the external IP from `kubectl get svc`.
- **Ingress**: Use the domain configured in the `Ingress` resource.
### Rolling Updates
Update the image in the `Deployment` and apply changes:
```bash
kubectl set image deployment/simple-http-server simple-http-server=<new-image>
```
Monitor rollout status:
```bash
kubectl rollout status deployment/simple-http-server
```
This setup ensures a scalable, load-balanced, and production-ready deployment
