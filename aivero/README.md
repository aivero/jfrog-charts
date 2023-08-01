```sh
export MASTER_KEY=$(openssl rand -hex 32)
echo ${MASTER_KEY}
 
# Create a secret containing the key. The key in the secret must be named master-key
kubectl create secret generic my-masterkey-secret -n artifactory-cpp-ce --from-literal=master-key=${MASTER_KEY}
```

```sh
# Create a key
export JOIN_KEY=$(openssl rand -hex 32)
echo ${JOIN_KEY}
 
# Create a secret containing the key. The key in the secret must be named join-key
kubectl create secret generic my-joinkey-secret -n artifactory-cpp-ce --from-literal=join-key=${JOIN_KEY}
```

```sh
helm repo add traefik https://traefik.github.io/charts
helm repo update
helm install traefik traefik/traefik --create-namespace --namespace artifactory-cpp-ce -f aivero/custom-traefik-config.yml 
kubectl port-forward $(kubectl get pods --selector "app.kubernetes.io/name=traefik" --output=name) 9000:9000

# This might time out first time, then just run it again. The cluster does some black magic. Maybe to get the right containers?! Second time its much faster.
helm upgrade --atomic --install artifactory-cpp-ce --create-namespace --namespace artifactory-cpp-ce jfrog/artifactory-cpp-ce -f aivero/aivero-artifactory.yaml
```
