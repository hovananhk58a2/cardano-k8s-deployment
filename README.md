# Cardano Node and DB Sync Kubernetes Deployment

This repository contains Kubernetes manifests to deploy a Cardano node and db-sync on a Kubernetes cluster. The deployment is configured for the `testnet-preprod` network and uses Mithril for fast bootstrapping.

---

## Prerequisites

1. **Kubernetes Cluster**: Ensure you have a running Kubernetes cluster (e.g., Minikube, GKE, EKS, AKS).
2. **kubectl**: Install and configure `kubectl` to interact with your cluster.
3. **Storage Class**: Ensure your cluster has a storage class that supports `ReadWriteMany` for the Cardano node PVC.
4. **PostgreSQL**: A PostgreSQL database is required for the db-sync component.

---

## Deployment Steps

### 1. Clone the Repository
```bash
git clone https://github.com/hovananhk58a2/cardano-k8s-deployment.git
cd cardano-k8s-deployment
```

### 2. Create the Namespace
```bash
kubectl apply -f manifests/namespace.yaml
```

### 3. Deploy ConfigMaps
```bash
kubectl apply -f manifests/configmaps/
```

### 4. Create Secrets
Replace the data values `POSTGRES_DB, POSTGRES_USER, POSTGRES_PASSWORD` in `manifests/secrets/postgresql-secrets.yaml` with your base64-encoded PostgreSQL credentials, example:
```bash
echo -n "dbname" | base64 #POSTGRES_DB
echo -n "dbuser" | base64 #POSTGRES_USER
echo -n "dbpassword" | base64 #POSTGRES_PASSWORD
```
Apply the secrets:
```bash
kubectl apply -f manifests/secrets/
```

### 5. Deploy Persistent Volume Claims (PVCs)
```bash
kubectl apply -f manifests/pvc/
```

### 6. Deploy the Cardano Node
```bash
kubectl apply -f manifests/statefulsets/cardano-node.yaml
```

### 7. Deploy PostgreSQL
```bash
kubectl apply -f manifests/deployments/postgresql.yaml
```

### 8. Deploy Cardano DB Sync
```bash
kubectl apply -f manifests/deployments/cardano-db-sync.yaml
```

### 9. Deploy Services
```bash
kubectl apply -f manifests/services/
```

### 10. Enable Auto-scaling for DB Sync
```bash
kubectl apply -f manifests/hpa/db-sync-hpa.yaml
```

---

## Monitoring and Logs

### Check Pod Status
```bash
kubectl get pods -n cardano
``` 

### View Logs
- Cardano Node:
```bash
kubectl logs -n cardano -l app=cardano-node
```
- DB Sync:
```bash
kubectl logs -n cardano -l app=cardano-db-sync
```
