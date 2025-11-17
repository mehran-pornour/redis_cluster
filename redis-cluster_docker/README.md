docker-compose up --build -d


docker exec -it stress-client sh

redis-cli --cluster create \
  redis-0:6379 redis-1:6379 redis-2:6379 \
  redis-3:6379 redis-4:6379 redis-5:6379 \
  --cluster-replicas 1 --cluster-yes

exit

docker exec -it stress-client sh

redis-cli --cluster create \
  redis-0:6379 redis-1:6379 redis-2:6379 \
  redis-3:6379 redis-4:6379 redis-5:6379 \
  --cluster-replicas 1 --cluster-yes

exit



monitor : state and failover
        docker exec -it redis-0 redis-cli cluster info
docker stop redis-0
