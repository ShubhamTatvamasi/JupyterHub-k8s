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

install jupyterhub
```bash
helm upgrade -i jupyterhub jupyterhub/jupyterhub \
  --values config.yaml
```

