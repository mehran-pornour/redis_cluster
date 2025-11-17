helm repo add bitnami https://charts.bitnami.com/bitnami

helm repo update

kubectl create namespace redis-prod

helm install my-prod-redis bitnami/redis-cluster \
  --namespace redis-prod \
  -f values.yaml

kubectl get pods -n redis-prod

SECRET_NAME=$(kubectl get secret -n redis-prod -l app.kubernetes.io/instance=my-prod-redis -o jsonpath="{.items[0].metadata.name}")
REDIS_PASSWORD=$(kubectl get secret ${SECRET_NAME} -n redis-prod -o jsonpath="{.data.redis-password}" | base64 --decode)

echo "Redis Password: ${REDIS_PASSWORD}"

SERVICE_NAME="my-prod-redis-redis-cluster" 

kubectl run --namespace redis-prod redis-client-test --rm -i --tty \
  --image=redis:7-alpine -- bash -c \
  "redis-cli -h ${SERVICE_NAME} -a ${REDIS_PASSWORD} cluster info"

output: 
cluster_state: ok
cluster_slots_assigned: 16384
cluster_known_nodes: 6


for monitoring:
    in values.yml
                metrics: enabled: true
recovery:

    persistence: enabled

helm upgrade my-prod-redis bitnami/redis-cluster -n redis-prod -f values.yaml
