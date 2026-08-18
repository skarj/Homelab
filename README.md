# Homelab

### Secrets
```
kubectl create secret generic pihole-secret \
  --namespace=pihole \
  --from-literal=WEBPASSWORD='PASSWORD'
```
