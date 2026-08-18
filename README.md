# Homelab

### Secrets
```
kubectl create secret generic pihole-secret \
  --namespace=pihole \
  --from-literal=WEBPASSWORD='PIGHOLE_PASSWORD'

kubectl create secret generic pihole-secret \
  --namespace=external-dns \
  --from-literal=WEBPASSWORD='PIGHOLE_PASSWORD'
```
