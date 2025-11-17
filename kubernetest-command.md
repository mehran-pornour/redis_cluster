kubectl apply -f redis-config.yaml
kubectl apply -f redis-headless-svc.yaml
kubectl apply -f redis-client-svc.yaml

kubectl apply -f redis-statefulset.yaml


kubectl apply -f redis-cluster-init-job.yaml
