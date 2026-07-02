# Step 1 - Yaml files in region:

<!-- wp:image {"id":2952,"sizeSlug":"large","linkDestination":"none"} -->
<figure class="wp-block-image size-large"><img src="https://imyourhuckleberry4.wordpress.com/wp-content/uploads/2026/02/t2.png?w=896" alt="" class="wp-image-2952"/></figure>
<!-- /wp:image -->

# Step 2 - Sidecar:
a. Example code below to do for all 3
- docker run -d --net=host --rm \
    -v $(pwd)/prometheus1_us1.yml:/etc/prometheus/prometheus.yml \
    --name prometheus-1-sidecar-us1 \
    -u root \
    quay.io/thanos/thanos:v0.28.0 \
    sidecar \
    --http-address 0.0.0.0:19092 \
    --grpc-address 0.0.0.0:19192 \
    --reloader.config-file /etc/prometheus/prometheus.yml \
    --prometheus.url http://172.17.0.1:9092 && echo "Started sidecar for Prometheus 1 US1"

b. Add code below to your existing code

  - job_name: 'sidecar'
    static_configs:
      - targets: ['172.17.0.1:19091','172.17.0.1:19092']

# Step 3: Thanos Querier to use sidecars to query metrics for future API aka Grafana

- docker run -d --net=host --rm \
    --name querier \
    quay.io/thanos/thanos:v0.28.0 \
    query \
    --http-address 0.0.0.0:29090 \
    --query.replica-label replica \
    --store 172.17.0.1:19190 \
    --store 172.17.0.1:19191 \
    --store 172.17.0.1:19192 && echo "Started Thanos Querier"