# Kubernetes Deployment Guide

## Prerequisites

- Kubernetes cluster (v1.19 or later)
- `kubectl` CLI configured to access your cluster
- Docker image pushed to a registry accessible by your cluster
- Sufficient cluster resources (minimum 3 nodes or single node with resource allowance)

## Deployment Components

The `k8s-deployment.yaml` includes:

1. **Namespace** - Isolated `migration-calc` namespace
2. **Deployment** - Manages 3 replicas of the calculator
3. **Service** - Exposes the application (LoadBalancer type)
4. **HorizontalPodAutoscaler** - Auto-scales based on CPU/memory metrics

## Quick Start

### 1. Build and Push Docker Image

```bash
# Build the image
docker build -t vmw-migration-calc:latest .

# Tag for your registry (example: Azure Container Registry)
docker tag vmw-migration-calc:latest myregistry.azurecr.io/vmw-migration-calc:latest

# Push to registry
docker push myregistry.azurecr.io/vmw-migration-calc:latest
```

### 2. Update Image Reference (if using private registry)

Edit `k8s-deployment.yaml` and update the image field:

```yaml
containers:
- name: migration-calculator
  image: myregistry.azurecr.io/vmw-migration-calc:latest
  imagePullPolicy: Always
```

### 3. Apply the Deployment

```bash
kubectl apply -f k8s-deployment.yaml
```

### 4. Verify Deployment

```bash
# Check if namespace was created
kubectl get namespace migration-calc

# Check deployment status
kubectl get deployment -n migration-calc

# Check pods
kubectl get pods -n migration-calc

# Check service
kubectl get svc -n migration-calc

# Watch deployment progress
kubectl rollout status deployment/migration-calculator -n migration-calc
```

### 5. Access the Application

#### Option A: LoadBalancer Service (if supported by your cluster)

```bash
# Get the external IP
kubectl get svc migration-calculator -n migration-calc

# Access via: http://<EXTERNAL-IP>
```

#### Option B: Port Forward

```bash
kubectl port-forward svc/migration-calculator 8080:80 -n migration-calc
# Access via: http://localhost:8080
```

#### Option C: NodePort

Edit the service type in `k8s-deployment.yaml`:

```yaml
kind: Service
spec:
  type: NodePort
  ports:
  - port: 80
    targetPort: http
    nodePort: 30080  # High-numbered port
```

Apply and access via: `http://<node-ip>:30080`

## Managing the Deployment

### View Deployment Details

```bash
kubectl describe deployment migration-calculator -n migration-calc
kubectl describe pods -n migration-calc
kubectl describe svc migration-calculator -n migration-calc
```

### View Logs

```bash
# Current logs from one pod
kubectl logs -n migration-calc deployment/migration-calculator

# Follow logs in real-time
kubectl logs -f -n migration-calc deployment/migration-calculator

# Previous logs (if pod restarted)
kubectl logs -p -n migration-calc deployment/migration-calculator

# Logs from specific pod
kubectl logs -n migration-calc <pod-name>
```

### Scale Manually

```bash
# Scale to 5 replicas
kubectl scale deployment migration-calculator --replicas=5 -n migration-calc

# Verify
kubectl get pods -n migration-calc
```

### Update the Image

```bash
# Update deployment with new image
kubectl set image deployment/migration-calculator \
  migration-calculator=myregistry.azurecr.io/vmw-migration-calc:v1.1.0 \
  -n migration-calc

# Check rollout status
kubectl rollout status deployment/migration-calculator -n migration-calc

# Rollback if needed
kubectl rollout undo deployment/migration-calculator -n migration-calc
```

### Restart Pods

```bash
# Rolling restart
kubectl rollout restart deployment/migration-calculator -n migration-calc
```

## Monitoring and Autoscaling

### Check HPA Status

```bash
kubectl get hpa -n migration-calc
kubectl describe hpa migration-calculator-hpa -n migration-calc
```

### View Pod Metrics

```bash
# Install metrics-server if not present (most managed clusters have it)
kubectl apply -f https://github.com/kubernetes-sigs/metrics-server/releases/latest/download/components.yaml

# View current metrics
kubectl top pods -n migration-calc
kubectl top nodes
```

### Watch Autoscaling in Action

```bash
# Watch HPA
watch kubectl get hpa -n migration-calc

# Monitor pods scaling
watch kubectl get pods -n migration-calc
```

## Customization

### Adjust Replicas

Edit `k8s-deployment.yaml`:

```yaml
spec:
  replicas: 5  # Change from 3
```

### Modify Resource Limits

```yaml
resources:
  requests:
    cpu: 100m        # Minimum guaranteed
    memory: 128Mi
  limits:
    cpu: 500m        # Maximum allowed
    memory: 256Mi
```

### Change Service Type

For internal-only access, change to `ClusterIP`:

```yaml
kind: Service
spec:
  type: ClusterIP  # Changed from LoadBalancer
  ports:
  - port: 80
    targetPort: http
```

### Add Ingress

Create an `ingress.yaml` for external HTTP(S) routing:

```yaml
apiVersion: networking.k8s.io/v1
kind: Ingress
metadata:
  name: migration-calculator-ingress
  namespace: migration-calc
spec:
  rules:
  - host: migration-calc.example.com
    http:
      paths:
      - path: /
        pathType: Prefix
        backend:
          service:
            name: migration-calculator
            port:
              number: 80
```

Apply with:

```bash
kubectl apply -f ingress.yaml
```

## Cleanup

### Delete Specific Resources

```bash
# Delete deployment only (keeps namespace)
kubectl delete deployment migration-calculator -n migration-calc

# Delete service only
kubectl delete svc migration-calculator -n migration-calc

# Delete HPA only
kubectl delete hpa migration-calculator-hpa -n migration-calc
```

### Delete Everything

```bash
# Delete entire namespace (removes all resources)
kubectl delete namespace migration-calc
```

## Troubleshooting

### Pods Not Starting

```bash
# Check pod status
kubectl describe pod <pod-name> -n migration-calc

# Check events
kubectl get events -n migration-calc --sort-by='.lastTimestamp'

# Check image pull issues
kubectl describe pod <pod-name> -n migration-calc | grep -A 10 "Events:"
```

### Service Not Accessible

```bash
# Verify service exists
kubectl get svc -n migration-calc

# Test connectivity within cluster
kubectl run -it --rm debug --image=busybox --restart=Never -- \
  wget -O- http://migration-calculator.migration-calc.svc.cluster.local

# Check endpoints
kubectl get endpoints migration-calculator -n migration-calc
```

### High Memory/CPU Usage

```bash
# Check current usage
kubectl top pod <pod-name> -n migration-calc

# Adjust resource limits in deployment:
# Edit the resources.limits section in k8s-deployment.yaml
kubectl apply -f k8s-deployment.yaml

# Restart deployment
kubectl rollout restart deployment/migration-calculator -n migration-calc
```

### HPA Not Working

```bash
# Check if metrics-server is running
kubectl get deployment metrics-server -n kube-system

# Check HPA status
kubectl describe hpa migration-calculator-hpa -n migration-calc

# Manual metrics check (if metrics-server installed)
kubectl get metrics pod -n migration-calc
```

## Production Deployment Checklist

- [ ] Image pushed to production registry
- [ ] Resource requests/limits adjusted for your cluster
- [ ] Ingress configured for domain routing
- [ ] TLS/HTTPS enabled (add cert to ingress)
- [ ] Network policies configured (if needed)
- [ ] Pod Disruption Budgets set (PDB)
- [ ] Monitoring and alerting configured
- [ ] Logging aggregation setup
- [ ] Backup strategy in place
- [ ] Rollback procedure tested
- [ ] Security scanning on image
- [ ] RBAC policies configured

## Advanced: Multiple Environments

For dev/staging/prod deployments:

```bash
# Development
kubectl apply -f k8s-deployment.yaml -n dev

# Staging  
kubectl apply -f k8s-deployment.yaml -n staging

# Production (with custom values)
kubectl apply -f k8s-deployment.yaml -n prod
```

Or use Kustomize:

```bash
# Deploy different overlays
kubectl apply -k overlays/prod/
```

## References

- [Kubernetes Documentation](https://kubernetes.io/docs/)
- [Kubernetes Deployment API](https://kubernetes.io/docs/reference/kubernetes-api/workload-resources/deployment-v1/)
- [kubectl Cheat Sheet](https://kubernetes.io/docs/reference/kubectl/cheatsheet/)
