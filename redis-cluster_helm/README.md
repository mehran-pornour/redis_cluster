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



## ahmad rafiee redis youtube
``` bash
global:
  defaultStorageClass: "local-path"   # kubectl get sc
  storageClass: ""
  security:
    allowInsecureImages: false
  redis:
    password: "mehran"
image:
  registry: registry-1.docker.io
  repository: bitnami/redis
  tag: "7.2.5"
  digest: ""

  pullPolicy: IfNotPresent

  pullSecrets: []

  debug: false

architecture: replication
## Redis(R) Authentication parameters
## ref: https://github.com/bitnami/containers/tree/main/bitnami/redis#setting-the-server-password-on-first-run
##
auth:
  ## @param auth.enabled Enable password authentication
  ##
  enabled: true
  ## @param auth.sentinel Enable authentication on sentinels too
  ##
  sentinel: true
  ## @param auth.password Redis(R) password
  ## Defaults to a random 10-character alphanumeric string if not set
  ##
  password: "mehran"
  ## @param auth.existingSecret The name of an existing secret with Redis(R) credentials
  ## NOTE: When it's set, the previous `auth.password` parameter is ignored
  ##
  existingSecret: ""
  ## @param auth.existingSecretPasswordKey Password key to be retrieved from existing secret
  ## NOTE: ignored unless `auth.existingSecret` parameter is set
  ##
  existingSecretPasswordKey: ""
  ## @param auth.usePasswordFiles Mount credentials as files instead of using an environment variable
  ##
  usePasswordFiles: true
  ## @param auth.usePasswordFileFromSecret Mount password file from secret
  ##
  usePasswordFileFromSecret: true
  ## Redis ACL restricts connections by limiting commands and key access with auth management.
  ## ref: https://redis.io/docs/latest/operate/oss_and_stack/management/security/acl/
  ##
  acl:
    ## @param auth.acl.enabled Enables the support of the Redis ACL system
    ##
    enabled: false
    ## @param auth.acl.sentinel Enables the support of the Redis ACL system for Sentinel Nodes
    ##
    sentinel: false
    ## @param auth.acl.users A list of the configured users in the Redis ACL system
    ##
    ## Example:
    ## users:
    ##   - username: "my-user"
    ##     password: "mypassword"
    ##     enabled: "on"
    ##     commands: "+@all"
    ##     keys: "~*"
    ##     channels: "&*"
    users: []
    ## @param auth.acl.userSecret Name of the Secret, containing user credentials for ACL users. Keys must match usernames.
    userSecret: ""

master:

  count: 1
  persistence:
    enabled: true
    medium: ""
    sizeLimit: ""
    path: /data
    storageClass: "local-path"    # kubectl get sc
    accessModes:
      - ReadWriteOnce
    size: 6Gi
  persistentVolumeClaimRetentionPolicy:
    enabled: false
    whenScaled: Retain
    whenDeleted: Retain
  service:
    type: ClusterIP
    portNames:
      redis: "tcp-redis"
    ports:
      redis: 6379
    nodePorts:
      redis: ""
    externalTrafficPolicy: Cluster
  pdb:
    create: true
    minAvailable: ""
    maxUnavailable: ""
replica:

  kind: StatefulSet
  replicaCount: 3
  revisionHistoryLimit: 10
  configuration: ""
  containerPorts:
    redis: 6379
  persistence:
    enabled: true
    path: /data
    storageClass: ""
    accessModes:
      - ReadWriteOnce
    size: 6Gi

  persistentVolumeClaimRetentionPolicy:
    enabled: false
    whenScaled: Retain
    whenDeleted: Retain
  service:
    type: ClusterIP
    ports:
      redis: 6379
  pdb:
    create: true
    minAvailable: ""
    maxUnavailable: ""

sentinel:
  enabled: true   ## change false to true
  image:
    registry: registry-1.docker.io
    repository: bitnami/redis-sentinel
    tag: "7.2.5"
    digest: ""
    pullPolicy: IfNotPresent
  containerPorts:
    sentinel: 26379
  persistence:
    enabled: false
    storageClass: ""
    accessModes:
      - ReadWriteOnce
    size: 100Mi
  persistentVolumeClaimRetentionPolicy:
    enabled: false
    whenScaled: Retain
    whenDeleted: Retain
  service:
    type: ClusterIP
    ports:
      redis: 6379
      sentinel: 26379
metrics:
  enabled: true  #change it to true
  image:
    registry: registry-1.docker.io
    repository: bitnami/redis-exporter
    tag: "1.59.0"
    digest: ""
    pullPolicy: IfNotPresent  
    pullSecrets: []
  containerPorts:
    http: 9121
  service:
    enabled: true
    ## @param metrics.service.type Redis(R) exporter service type
    ##
    type: ClusterIP
    ## @param metrics.service.ports.http Redis(R) exporter service port
    ##
    ports:
      http: 9121
    externalTrafficPolicy: Cluster

  serviceMonitor:
    port: http-metrics
    enabled: false
    namespace: "monitoring"  # is empty change to monitoring namespaces

    interval: 30s

```
