# Cardano Node and DB Sync Kubernetes Deployment

This repository contains Kubernetes manifests to deploy a Cardano node and db-sync on a Kubernetes cluster. The deployment is configured for the `testnet-preprod` network and uses Mithril for fast bootstrapping.

---

## Prerequisites

1. **Kubernetes Cluster**: Ensure you have a running Kubernetes cluster (e.g., Openshift, Minikube, GKE, EKS, AKS).
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

### 6. Deploy PostgreSQL
```bash
kubectl apply -f manifests/deployments/postgresql.yaml
kubectl apply -f manifests/services/postgresql.yaml
```

### 7. Deploy Cardano App
```bash
kubectl apply -f manifests/statefulsets/cardano-app.yaml
kubectl apply -f manifests/services/cardano-app.yaml
```

### 8. Enable Auto-scaling (Optional)
```bash
kubectl apply -f manifests/hpa/cardano-hpa.yaml
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
kubectl logs -f -n cardano pod/cardano-app-<order_num> -c cardano-node
# (e.g, kubectl logs -f -n cardano pod/cardano-app-0 -c cardano-node)
```
- DB Sync:
```bash
kubectl logs -f -n cardano pod/cardano-app-<order_num> -c db-sync
# (e.g, kubectl logs -f -n cardano pod/cardano-app-0 -c db-sync)
```

## PostgreSQL
### Database name of each node
```text
cardano_app_<order_numm> (e.g, cardano_app_0, cardano_app_1,...).
```
### Username & Password
```text
POSTGRES_USER, POSTGRES_PASSWORD as secret config.
```