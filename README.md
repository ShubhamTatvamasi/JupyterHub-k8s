# JupyterHub-k8s

add helm repo
```bash
helm repo add jupyterhub https://jupyterhub.github.io/helm-chart/
helm repo update
```

create a token file
```yaml
cat << EOF > config.yaml
proxy:
  secretToken: $(openssl rand -hex 32)
EOF
```

search for jupyterhub repo
```bash
helm search repo jupyterhub/jupyterhub
```

create jupyterhub namespace 
```bash
kubectl create ns jupyterhub
```

install jupyterhub
```bash
helm upgrade -i jupyterhub jupyterhub/jupyterhub \
  --namespace jupyterhub \
  --values config.yaml
```

get list of deployments
```bash
helm ls
```

uninstall jupyterhub
```bash
helm un jupyterhub
```

update current namespace
```bash
kubectl config set-context --current --namespace jupyterhub
```

create a Persistent Volume
```yaml
kubectl apply -f - << EOF
apiVersion: v1
kind: PersistentVolume
metadata:
  name: hub-db-dir
spec:
  storageClassName: manual
  accessModes:
  - ReadWriteOnce
  capacity:
    storage: 1Gi
  hostPath:
    path: "/usr/local/volumes/jupyterhub"
EOF
```



