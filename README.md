# redis_cluster

## Docker file 
```bash
    FROM redis:7.2
    # Add redis.conf for cluster mode
    COPY redis.conf /usr/local/etc/redis/redis.conf
    
    # Expose Redis and Cluster ports
    EXPOSE 6379 16379
    
    CMD ["redis-server", "/usr/local/etc/redis/redis.conf"]
```
## redis.conf
```bash
    port 6379
    cluster-enabled yes
    cluster-config-file nodes.conf
    cluster-node-timeout 5000
    appendonly yes
    appendfsync everysec
    protected-mode no
    bind 0.0.0.0
```
## redis cluster compose file
``` bash
    version: "3.9"
    services:
      redis-node-0:
        build: .
        container_name: redis-node-0
        ports:
          - "7000:6379"
        networks:
          redis-net:
            ipv4_address: 172.20.0.10
    
      redis-node-1:
        build: .
        container_name: redis-node-1
        ports:
          - "7001:6379"
        networks:
          redis-net:
            ipv4_address: 172.20.0.11
    
      redis-node-2:
        build: .
        container_name: redis-node-2
        ports:
          - "7002:6379"
        networks:
          redis-net:
            ipv4_address: 172.20.0.12
    
      redis-node-3:
        build: .
        container_name: redis-node-3
        ports:
          - "7003:6379"
        networks:
          redis-net:
            ipv4_address: 172.20.0.13
    
      redis-node-4:
        build: .
        container_name: redis-node-4
        ports:
          - "7004:6379"
        networks:
          redis-net:
            ipv4_address: 172.20.0.14
    
      redis-node-5:
        build: .
        container_name: redis-node-5
        ports:
          - "7005:6379"
        networks:
          redis-net:
            ipv4_address: 172.20.0.15
    
      cluster-init:
        image: redis:7.2
        depends_on:
          - redis-node-0
          - redis-node-1
          - redis-node-2
          - redis-node-3
          - redis-node-4
          - redis-node-5
        command: >
          sh -c "
          sleep 5 &&
          redis-cli --cluster create
          172.20.0.10:6379
          172.20.0.11:6379
          172.20.0.12:6379
          172.20.0.13:6379
          172.20.0.14:6379
          172.20.0.15:6379
          --cluster-replicas 1 --cluster-yes
          "
        networks:
          redis-net:
            ipv4_address: 172.20.0.20
    
    networks:
      redis-net:
        driver: bridge
        ipam:
          config:
            - subnet: 172.20.0.0/24

```
## redis cluster command
```bash
    docker-compose up -d --build

    docker exec -it redis-node-0 redis-cli cluster nodes

    docker exec -it redis-node-0 redis-cli set foo bar
    docker exec -it redis-node-0 redis-cli get foo
```
