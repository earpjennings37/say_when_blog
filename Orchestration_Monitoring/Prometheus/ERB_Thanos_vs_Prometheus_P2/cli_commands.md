# Step 1 - Initial Prometheus Setup:
a. Generate Artificial Metrics for 1 year:

- mkdir -p /root/prom-eu1 && docker run -i quay.io/thanos/thanosbench:v0.2.0-rc.1 block plan -p continuous-365d-tiny --labels 'cluster="eu1"' --max-time=6h | docker run -v /root/prom-eu1:/prom-eu1 -i quay.io/thanos/thanosbench:v0.2.0-rc.1 block gen --output.dir prom-eu1

- ls -lR /root/prom-eu1

b. Prometheus Configuration File:

- global:
  scrape_interval: 5s
  external_labels:
    cluster: eu1
    replica: 0
    tenant: team-eu # Not needed, but a good practice if you want to grow this to multi-tenant system some day.

- scrape_configs:
  - job_name: 'prometheus'
    static_configs:
      - targets: ['127.0.0.1:9090']
  - job_name: 'sidecar'
    static_configs:
      - targets: ['127.0.0.1:19090']
  - job_name: 'minio'
    metrics_path: /minio/prometheus/metrics
    static_configs:
      - targets: ['127.0.0.1:9000']
  - job_name: 'querier'
    static_configs:
      - targets: ['127.0.0.1:9091']
  - job_name: 'store_gateway'
    static_configs:
      - targets: ['127.0.0.1:19091']

c. Starting Prometheus Instance:

- docker run -d --net=host --rm \
>     -v $(pwd)/prometheus0_eu1.yml:/etc/prometheus/prometheus.yml \
>     -v $(pwd)/prom-eu1:/prometheus \
>     -u root \
>     --name prometheus-0-eu1 \
>     quay.io/prometheus/prometheus:v2.38.0 \
>     --config.file=/etc/prometheus/prometheus.yml \
>     --storage.tsdb.retention.time=1000d \
>     --storage.tsdb.path=/prometheus \
>     --storage.tsdb.max-block-duration=2h \
>     --storage.tsdb.min-block-duration=2h \
>     --web.listen-address=:9090 \
>     --web.external-url=https://ff3dfde6f2b8-10-244-3-76-9090.saci.r.killercoda.com \
>     --web.enable-lifecycle \
>     --web.enable-admin-api
f53a4bbcfe277b15fda28cdb4ec97f09dbbf5e6f805702c1f570df0f1305c5bd

d. Thanos Sidecar & Querier:

- docker run -d --net=host --rm \
>     --name prometheus-0-eu1-sidecar \
>     -u root \
>     quay.io/thanos/thanos:v0.28.0 \
>     sidecar \
>     --http-address 0.0.0.0:19090 \
>     --grpc-address 0.0.0.0:19190 \
>     --prometheus.url http://172.17.0.1:9090
f234067f4baced4dc07ef4abc2310f9b2d9b7229e1334afa3fb2662a9358d354

- docker run -d --net=host --rm \
>     --name querier \
>     quay.io/thanos/thanos:v0.28.0 \
>     query \
>     --http-address 0.0.0.0:9091 \
>     --query.replica-label replica \
>     --store 172.17.0.1:19190
00f55736263da970c818fe0a75299a4640a20f1d96f1103d25cebdbffd3bafc5

# Step 2 - Object Storage Continuous Backup:

a. Starting Object Storage: Minio

- mkdir /root/minio && \
> docker run -d --rm --name minio \
>      -v /root/minio:/data \
>      -p 9000:9000 -e "MINIO_ACCESS_KEY=minio" -e "MINIO_SECRET_KEY=melovethanos" \
>      minio/minio:RELEASE.2019-01-31T00-31-19Z \
>      server /data
bf296a3cffce08e62a316cf143ce08ab9817a44bdc59b05ced27945b705fba20

- mkdir /root/minio/thanos

b. Sidecar block backup:

- type: S3
config:
  bucket: "thanos"
  endpoint: "172.17.0.1:9000"
  insecure: true
  signature_version2: true
  access_key: "minio"
  secret_key: "melovethanos"

- docker stop prometheus-0-eu1-sidecar

- docker run -d --net=host --rm \
>     -v $(pwd)/bucket_storage.yaml:/etc/thanos/minio-bucket.yaml \
>     -v $(pwd)/prom-eu1:/prometheus \
>     --name prometheus-0-eu1-sidecar \
>     -u root \
>     quay.io/thanos/thanos:v0.28.0 \
>     sidecar \
>     --tsdb.path /prometheus \
>     --objstore.config-file /etc/thanos/minio-bucket.yaml \
>     --shipper.upload-compacted \
>     --http-address 0.0.0.0:19090 \
>     --grpc-address 0.0.0.0:19190 \
>     --prometheus.url http://172.17.0.1:9090
52d466db35f02020e36ffc98e267d0e4e59e771f65b7e8f2db0bbe2e252fdad3

# Step 3 - Fetching metrics from Bucket:

a. Deploying store for "EU1" Prometheus data:

- docker run -d --net=host --rm \
>     -v /root/editor/bucket_storage.yaml:/etc/thanos/minio-bucket.yaml \
>     --name store-gateway \
>     quay.io/thanos/thanos:v0.28.0 \
>     store \
>     --objstore.config-file /etc/thanos/minio-bucket.yaml \
>     --http-address 0.0.0.0:19091 \
>     --grpc-address 0.0.0.0:19191
979cc60805bbe3154dcff43e689651911d5d9ec3f6634615fc835d5cbfeb3cec

b. How to query Thanos store data?

- docker stop querier && \
> docker run -d --net=host --rm \
>    --name querier \
>    quay.io/thanos/thanos:v0.28.0 \
>    query \
>    --http-address 0.0.0.0:9091 \
>    --query.replica-label replica \
>    --store 172.17.0.1:19190 \
>    --store 172.17.0.1:19191
querier
500367ef71d97a6dcf44ad8a7c61dd5ecd4eb9dfc736736853fd3cfb9470796a

# Step 4 - Thanos Compactor:

a. Deploying Thanos Compactor:

- docker run -d --net=host --rm \
>  -v /root/editor/bucket_storage.yaml:/etc/thanos/minio-bucket.yaml \
>     --name thanos-compact \
>     quay.io/thanos/thanos:v0.28.0 \
>     compact \
>     --wait --wait-interval 30s \
>     --consistency-delay 0s \
>     --objstore.config-file /etc/thanos/minio-bucket.yaml \
>     --http-address 0.0.0.0:19095
61fdf2fa228d550c1641b57fadccc99dae9cdb233c00d4e394117afc588719c9