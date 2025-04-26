# XivTech Kubernetes DevOps Assignment

## 📌 Prerequisites

Before starting, please ensure the following software are installed on your system:

- **Docker Desktop** (for building and running containers)
- **Kind** (for creating a local Kubernetes cluster inside Docker)
- **kubectl** (for managing Kubernetes cluster and deployments)
- **Visual Studio Code** (optional, for editing files easily)

Make sure Docker and Kubernetes are working properly before proceeding.


## Project Overview
In this project, we deploy a simple web application on a local Kubernetes cluster using Kind (Kubernetes IN Docker). The application is a basic HTML page served via an NGINX container.

## Steps Performed

### 1. Create Project Structure
- Created a folder named `hello-world`.
- Inside `hello-world`, created folders:
  - `app/` containing `index.html`
  - `docker/` containing `Dockerfile`
  - `k8s/` containing `deployment.yaml` or `service.yaml`

### 2. Application Files
- **index.html** (inside app folder):
```html
<h1>Hello, World from Kubernetes!</h1>
```

- **Dockerfile** (inside docker folder):
```Dockerfile
FROM nginx:alpine
COPY app/index.html /usr/share/nginx/html/index.html
```

### 3. Build Docker Image
```bash
docker build -t hello-world-nginx -f docker/Dockerfile .
docker images
```

### 4. Create Kind Cluster
```bash
kind create cluster --name hello-world-cluster
kind get clusters
kubectl get nodes
```

### 5. Load Docker Image to Kind Cluster
```bash
kind load docker-image hello-world-nginx --name hello-world-cluster
kubectl get nodes
```
Now, the cluster nodes show `Ready` status.

### 6. Create Kubernetes YAML Files
- **deployment.yaml** (inside k8s folder): [deployment.yaml file](k8s/deployment.yaml)
```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: hello-world-deployment
spec:
  replicas: 1
  selector:
    matchLabels:
      app: hello-world
  template:
    metadata:
      labels:
        app: hello-world
    spec:
      containers:
      - name: nginx
        image: hello-world-nginx
        imagePullPolicy: Never
        ports:
        - containerPort: 80
```

- **service.yaml** (inside k8s folder): [service.yaml file](k8s/service.yaml)
```yaml
apiVersion: v1
kind: Service
metadata:
  name: hello-world-service
spec:
  type: NodePort
  selector:
    app: hello-world
  ports:
  - port: 80
    targetPort: 80
    nodePort: 31000
```

### 7. Apply Kubernetes Manifests
```bash
kubectl apply -f k8s/deployment.yaml
kubectl apply -f k8s/service.yaml
kubectl get pods
kubectl get svc
```

### 8. Test NodePort Access (Not Working in Kind)
```bash
curl http://localhost:31000
```
- Browser access on `http://localhost:31000` also did not work.
- Reason: Kind does not support direct NodePort access from localhost.

### 9. Port Forwarding
```bash
kubectl port-forward service/hello-world-service 8080:80
```
Now, access:
```bash
curl http://localhost:8080
```
Or open in browser:
```
http://localhost:8080
```
Output:
```
Hello, World from Kubernetes!
```

### 10. Final Result
The app is successfully running on the local Kubernetes cluster using Kind.

## Demo Video
> 🔗 [Click here to watch the demo video](https://drive.google.com/file/d/1vQH-65PybjjTA1tQlxL4kqTCOFaJA5ML/view?usp=drive_link)


## Project Structure
```bash
hello-world/
├── app/
│   └── index.html
├── docker/
│   └── Dockerfile
├── k8s/
│   ├── deployment.yaml
│   └── service.yaml
└── demo/
    └── video_link.txt (Google Drive link to video)
```
---
## ✍️ Author

**Vishwaraj Kumar**  
🔗 [GitHub Profile](https://github.com/vishwaraj-kumar)  
🔗 [LinkedIn Profile](https://www.linkedin.com/in/vishwaraj-kumar/)
