## Kubernetes Manifests
### Deployment
**`k8s/deployment.yaml`**
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
**`k8s/service.yaml`**
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
**`k8s/ingress.yaml`**
```yaml
apiVersion: networking.k8s.io/v1
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
### Kustomize
**`k8s/kustomization.yaml`**
```yaml
resources:
- deployment.yaml
- service.yaml
- ingress.yaml
```
---
## Helm Chart
### Chart.yaml
```yaml
apiVersion: v2
name: simple-http-server
version: 0.1.0
```
### values.yaml
```yaml
replicaCount: 2
image:
repository: <your-docker-image>
tag: latest
pullPolicy: IfNotPresent
service:
type: LoadBalancer
port: 80
ingress:
enabled: true
hosts:
- host: <your-domain>
paths:
- path: /
pathType: Prefix
```
### Templates
#### Deployment
**`helm/templates/deployment.yaml`**
```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
name: {{ .Chart.Name }}
labels:
app: {{ .Chart.Name }}
spec:
replicas: {{ .Values.replicaCount }}
selector:
matchLabels:
app: {{ .Chart.Name }}
template:
metadata:
labels:
app: {{ .Chart.Name }}
spec:
containers:
- name: {{ .Chart.Name }}
image: {{ .Values.image.repository }}:{{ .Values.image.tag }}
ports:
- containerPort: 8080
```
#### Service
**`helm/templates/service.yaml`**
```yaml
apiVersion: v1
kind: Service
metadata:
name: {{ .Chart.Name }}
spec:
type: {{ .Values.service.type }}
ports:
- port: {{ .Values.service.port }}
targetPort: 8080
selector:
app: {{ .Chart.Name }}
```
#### Ingress
**`helm/templates/ingress.yaml`**
```yaml
apiVersion: networking.k8s.io/v1
kind: Ingress
metadata:
name: {{ .Chart.Name }}
spec:
rules:
- host: {{ .Values.ingress.hosts[0].host }}
http:
paths:
- path: {{ .Values.ingress.hosts[0].paths[0].path }}
pathType: {{ .Values.ingress.hosts[0].paths[0].pathType }}
backend:
service:
name: {{ .Chart.Name }}
port:
number: {{ .Values.service.port }}
```
---
## Deployment Guide
### 1. Using `kubectl`
```bash
kubectl apply -f k8s/
```
### 2. Using Helm
```bash
helm install simple-http-server helm/
```
### 3. Using Kustomize
```bash
kubectl apply -k k8s/
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
