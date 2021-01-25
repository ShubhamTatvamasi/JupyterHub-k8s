# JupyterHub-k8s

Create new namespace
```bash
kubectl create ns jupyterhub
```

Install jupyterhub:
```bash
helm upgrade -i jupyterhub jupyterhub/jupyterhub \
  -n jupyterhub \
  --set proxy.secretToken=password \
  --set proxy.service.type=ClusterIP \
  --set proxy.chp.resources=null \
  --set hub.resources=null \
  --set singleuser.memory.guarantee=null
```

Uninstall jupyterhub:
```bash
helm un jupyterhub
```

Run the follwoing command on the node for giving volume permission to the pod:
```bash
chown 1000:1000 /opt/jupyterhub
```

Create a volume:
```bash
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
    path: "/opt/jupyterhub"
EOF
```

Setup Ingress:
```bash
kubectl apply -f - << EOF
apiVersion: networking.k8s.io/v1beta1
kind: Ingress
metadata:
  name: jupyterhub
  annotations:
    nginx.org/websocket-services: proxy-public
spec:
  tls:
    - hosts:
      - jupyterhub.k8s.shubhamtatvamasi.com
      secretName: letsencrypt
  rules:
    - host: jupyterhub.k8s.shubhamtatvamasi.com
      http:
        paths:
        - path: /
          backend:
            serviceName: proxy-public
            servicePort: 80
EOF
```

Setup a volume for admin user:
```bash
kubectl apply -f - << EOF
apiVersion: v1
kind: PersistentVolume
metadata:
  name: claim-admin
spec:
  storageClassName: manual
  accessModes:
  - ReadWriteOnce
  capacity:
    storage: 10Gi
  hostPath:
    path: "/opt/claim-admin"
EOF
```


---

### Old

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



