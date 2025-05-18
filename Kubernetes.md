# Kubernetes Comprehensive Cheat Sheet

## 1. Introduction
- **What is Kubernetes?**: A platform to manage containerized apps (e.g., Docker) across multiple computers, automating deployment, scaling, and operations.
- **Analogy**: A restaurant manager overseeing cooks (containers) across kitchens (nodes) to serve customers (users).

## 2. Key Concepts and Hierarchy
- **Cluster**: A group of computers (nodes) working together.
- **Contexts**: Configuration settings (e.g., `minikube`, `kind-k8s-training`).
- **Namespaces**: Organizational divisions (e.g., `default`, `student47`, `kube-system`).
- **Nodes**: Individual computers (e.g., `minikube`).
- **Pods**: Smallest units with containers, init containers, or sidecars.
- **Containers**: Apps inside pods (e.g., Nginx, busybox).
- **Deployments**: Manage pod replicas and updates.
- **Services**: Expose pods with stable IPs/ports (e.g., `ClusterIP`, `NodePort`, `LoadBalancer`).
- **Init Containers**: Run prep tasks once when the pod starts and exit.
- **Multi-Container Pods/Sidecars**: Pods with supporting containers (e.g., content generators).
- **Secrets/ConfigMaps**: Store sensitive (secrets) or non-sensitive (configmaps) data.
- **Ingress**: Routes external traffic to services based on rules.
- **Hierarchy**: Cluster → Namespaces → Nodes → Pods (with Containers, Init Containers, Sidecars) → Managed by Deployments → Exposed by Services → Routed by Ingress.

## 3. Setting Up kubectl and Clusters
### Configure kubectl Context
- **Check Contexts**: `kubectl config get-contexts`
- **Set Context**: `kubectl config use-context minikube`
- **Using Config File**: Place `training-cluster.config` in `~/.kube/`, backup `~/.kube/config`, set `export KUBECONFIG=$HOME/.kube/config:$HOME/.kube/training-cluster.config`.

### Install Local Cluster
- **Minikube**:
  - Install: https://minikube.sigs.k8s.io/docs/start/
  - Start: `minikube start`
  - Check Nodes: `kubectl get nodes`
- **Kind**:
  - Install: https://kind.sigs.k8s.io/docs/user/quick-start/
  - Create: `kind create cluster --name k8s-training`
  - Check: `kubectl cluster-info --context kind-k8s-training`
  - Delete: `kind delete cluster --name k8s-training`

### Setup Namespace
- **Create**: `kubectl create namespace student47`
- **Scope**: `kubectl get pods -n student47`
- **Set Default**: `kubectl config set-context $(kubectl config current-context) --namespace=student47`

## 4. Working with Pods, Deployments, and Containers
- **Create Pod**: `kubectl run multitool --image=wbitt/network-multitool`
- **Create Init Pod**: `kubectl create -f init-container-pod.yaml` (watches: `kubectl get pods -w`)
- **Create Multi-Container Pod**: `kubectl create -f multi-container-pod.yaml`
- **Create Deployment**: `kubectl create deployment multitool --image=wbitt/network-multitool`
- **Scale**: `kubectl scale deployment multitool --replicas=4`
- **Delete**: `kubectl delete pod multitool` or `kubectl delete deployment nginx`
- **Describe**: `kubectl describe deployment multitool`
- **Exec**: `kubectl exec -it multi-container-demo -c content-generator -- /bin/sh`
- **Logs**: `kubectl logs multi-container-demo -c nginx`
- **Replicas**: Replicas × Containers per Pod = Total Containers (e.g., 4 × 2 = 8).

## 5. CPU and Memory Limits
- **Why Use Limits?**:
  - Small nodes (e.g., 1 CPU, 2GB RAM) have system pods (~60% CPU, 600m). Nginx defaults to 100m CPU.
  - Prevents abuse (e.g., faulty apps, malicious intent) and aids scheduler node placement.
- **Setup**:
  - Define in `resources`:
    - `requests`: Minimum (e.g., `cpu: "1m"`, `memory: "5Mi"`).
    - `limits`: Maximum (e.g., `cpu: "5m"`, `memory: "10Mi"`).
  - QoS Classes: `BestEffort` (no limits), `Burstable` (requests < limits), `Guaranteed` (requests = limits).
- **Metrics**: Enable `metrics-server` (`minikube addons enable metrics-server`), check with `kubectl top nodes`, `kubectl top pods --all-namespaces`.
- **Default Limits**: Set at namespace level (see https://kubernetes.io/docs/tasks/administer-cluster/manage-resources/memory-default-namespace/).

## 6. Networking and Services
- **Pod Network**: Private IPs (e.g., `172.17.0.6`) managed by CNI (e.g., Flannel). Internal access only.
- **Service Types**:
  - `ClusterIP`: Internal IP (e.g., `10.111.187.198`).
  - `NodePort`: External via `node-ip:port` (e.g., `192.168.50.67:30848`).
  - `LoadBalancer`: External IP via cloud (e.g., `35.205.60.1`).
- **Port Fields**:
  - `port`: Service port (e.g., `80`).
  - `targetPort`: Pod port for service traffic (e.g., `80` or `3000`, defaults to `containerPort`).
  - `containerPort`: App listening port in container (e.g., `443`).
- **Create Service**: `kubectl expose deployment nginx --port=80 --type=NodePort`
- **Access**: `kubectl exec -it multitool -- curl <pod-ip>`

## 7. Replicas and Rolling Updates
- **Scale**: `kubectl scale deployment nginx --replicas=4`
- **Update**: `kubectl set image deployment nginx nginx=nginx:1.9.1 --record`
- **Status**: `kubectl rollout status deployment nginx`
- **History**: `kubectl rollout history deployment nginx`
- **Undo**: `kubectl rollout undo deployment nginx`
- **Scenario**: Scale Nginx to 4, update to `nginx:1.9.1`, monitor with `while true; do curl -sI minikube-ip:30900 | grep Server; sleep 1; done`. Undo a bad update (e.g., `nginx:100.200.300`) causing `ImagePullBackOff`.

## 8. Environment Variables, Secrets, and ConfigMaps
- **Env Vars**: Set in `env` (e.g., `name: API_URL, value: https://api.example.com`).
- **Secrets**:
  - Create: `kubectl create secret generic api-key --from-literal=API_KEY=ghi-123-444-555-mno`
  - Use: `valueFrom: secretKeyRef: name: api-key, key: API_KEY`
  - Decode: `echo <base64> | base64 -d`
- **ConfigMaps**:
  - Create: `kubectl create configmap api-url --from-literal=API_URL=https://api.example.com`
  - Use: `valueFrom: configMapKeyRef: name: api-url, key: API_URL`
- **Update**: `kubectl replace -f <file> --dry-run=client | kubectl apply -f -`, then `kubectl delete pod <pod-name>`
- **Multi-Vars Secret**: `kubectl create secret generic demo-app-credentials --from-literal=API_KEY=... --from-literal=API_URL=...`

## 9. SSL with Nginx
- **Create Certs**: `./support-files/generate-self-signed-certs.sh`
- **Create Secret**: `kubectl create secret tls nginx-certs --cert=tls.crt --key=tls.key`
- **Create ConfigMap**: `kubectl create configmap nginx-config --from-file=support-files/nginx-connectors.conf`
- **Deploy**: `kubectl create -f support-files/nginx-ssl.yaml`
- **Access**: `curl <pod-ip>` or expose with service.

## 10. Secrets Management
- **Options**: Encrypted repos (not recommended), External-Secrets (GCP/Vault), Secrets CSI (Vault/GCP).
- **Best Practice**: Avoid storing secrets in files; use secure vaults.

## 11. Ingress
- **What**: Routes external traffic to services based on rules (e.g., paths, hosts).
- **Differs from Service**: Services expose whole services; Ingress enables fine-grained routing.
- **Requires**: Ingress Controller (e.g., NGINX Ingress Controller).
- **Example**: See below.

## 12. Troubleshooting
- **Issues**: YAML errors, image pull failures, resource limits, network issues.
- **Steps**: `kubectl logs <pod> [-c <container>]`, `kubectl describe pod <pod>`, `kubectl get events --sort-by=.metadata.creationTimestamp`.

## 13. Useful Commands
- `kubectl config get-contexts`: List contexts.
- `kubectl config use-context <name>`: Set context.
- `kubectl version`: Check versions.
- `kubectl cluster-info`: Cluster details.
- `kubectl get nodes`: List nodes.
- `kubectl get pods`: List pods.
- `kubectl get all`: List all resources.
- `kubectl describe pod`: Pod details.
- `kubectl get events`: List events.
- `kubectl api-resources`: List API resources.
- `kubectl api-versions`: List API versions.
- `kubectl exec`: Enter pod.
- `kubectl logs`: View logs.
- `kubectl create -f <file>`: Create resource.
- `kubectl apply -f <file>`: Create/update resource.
- `kubectl top nodes`: Node resources.
- `kubectl top pods`: Pod resources.
- `kubectl rollout status`: Rollout status.
- `kubectl rollout history`: Rollout history.
- `kubectl rollout undo`: Undo rollout.

## 14. YAML Distinguishing Elements
- **Pod**: `kind: Pod`, `spec.containers`, `spec.initContainers`.
- **Deployment**: `kind: Deployment`, `spec.replicas`, `spec.selector`, `spec.template`.
- **Service**: `kind: Service`, `spec.ports`, `spec.selector`, `spec.type`.

## 15. Example YAML Files
### Pod Example (with Init and Sidecar)
```yaml
apiVersion: v1
kind: Pod
metadata:
  name: example-pod
  namespace: student47
spec:
  initContainers:
  - name: init-cloner
    image: alpine/git
    command: ["git", "clone", "https://github.com/Praqma/simple-website.git", "/web-content"]
    volumeMounts:
    - name: web-content
      mountPath: /web-content
  containers:
  - name: nginx
    image: nginx:alpine
    ports:
    - containerPort: 80
    resources:
      requests:
        cpu: "1m"
        memory: "5Mi"
      limits:
        cpu: "5m"
        memory: "10Mi"
    volumeMounts:
    - name: web-content
      mountPath: /usr/share/nginx/html
  - name: content-generator
    image: busybox
    command: ["sh", "-c", "while true; do echo $(date) >> /web-content/index.html; sleep 5; done"]
    volumeMounts:
    - name: web-content
      mountPath: /web-content
  volumes:
  - name: web-content
    emptyDir: {}
```
- **Notes**: Init container clones a website, Nginx serves it, sidecar updates content.

### Deployment Example (with Limits and Secrets)
```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: example-deployment
  namespace: student47
spec:
  replicas: 3
  selector:
    matchLabels:
      app: example
  template:
    metadata:
      labels:
        app: example
    spec:
      volumes:
      - name: secret-volume
        secret:
          secretName: app-secret
      containers:
      - name: app
        image: wbitt/k8s-secrets-demo-app
        ports:
        - containerPort: 3000
        resources:
          requests:
            cpu: "2m"
            memory: "10Mi"
          limits:
            cpu: "10m"
            memory: "20Mi"
        env:
        - name: API_URL
          valueFrom:
            configMapKeyRef:
              name: app-config
              key: API_URL
        - name: API_KEY
          valueFrom:
            secretKeyRef:
              name: app-secret
              key: API_KEY
        volumeMounts:
        - name: secret-volume
          mountPath: /etc/secrets
```
- **Notes**: Manages 3 pods, uses secrets/configmaps, sets resource limits.

### Service Example (with NodePort)
```yaml
apiVersion: v1
kind: Service
metadata:
  name: example-service
  namespace: student47
spec:
  selector:
    app: example
  ports:
  - port: 80
    targetPort: 3000
    nodePort: 31000
  type: NodePort
```
- **Notes**: Exposes deployment on port 80, targets pod port 3000, accessible at `node-ip:31000`.

### Ingress Example
```yaml
apiVersion: networking.k8s.io/v1
kind: Ingress
metadata:
  name: example-ingress
  namespace: student47
spec:
  rules:
  - host: example.com
    http:
      paths:
      - path: /
        pathType: Prefix
        backend:
          service:
            name: example-service
            port:
              number: 80
```
- **Notes**: Routes `example.com` traffic to `example-service` on port 80. Requires an Ingress Controller.

## 16. Tips for Exam Preparation
- **Objective**: Memorize commands, port types, limits, and YAML structures.
- **Subjective**: Use analogies (e.g., building for ports) and scenarios.
- **Practice**: Deploy these examples in Minikube, scale, update, and test with `curl`.