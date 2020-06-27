
# Running the exporter

    nutanix_exporter -nutanix.url https://nutanix_cluster:9440 -nutanix.username <user> -nutanix.password <password>

    localhost:9405/metrics

# Running exporter with different sections

    nutanix_exporter -nutanix.conf ./config.yml

During the Query pass GET-Parameter Section

    localhost:9405/metrics?section=cluster01


Config
```
default:
  nutanix_host: https://nutanix.cluster.local:9440
  nutanix_user: prometheus
  nutanix_password: p@ssw0rd

cluster02:
  nutanix_host: https://nutanix02.cluster.local:9440
  nutanix_user: prometheus
  nutanix_password: qwertz
```

# Prometheus extendended Configuration

Nutanix Config:
```
nutanix.cluster.local:
  nutanix_host: https://nutanix.cluster.local:9440
  nutanix_user: prometheus
  nutanix_password: p@ssw0rd

nutanix02.cluster.local:
  nutanix_host: https://nutanix02.cluster.local:9440
  nutanix_user: prometheus
  nutanix_password: qwertz
```

Prometheus Config:
```
scrape_configs:
  - job_name: nutanix_export
    metrics_path: /metrics
    static_configs:
    - targets:
      - nutanix.cluster.local
      - nutanix02.cluster.local
    relabel_configs:
    - source_labels: [__address__]
      target_label: __param_section
    - source_labels: [__address__]
      target_label: __param_target
    - source_labels: [__param_target]
      target_label: instance
    - target_label: __address__
      replacement: nutanix_exporter:9405
```
Alternative Way:
Start GO Binary to see all options and parse the output for each cluster on another port.
./nutanix-exporter_linux_amd64 --help
Usage of ./nutanix-exporter_linux_amd64:
  -listen-address string
    	The address to lisiten on for HTTP requests. (default &quot;:9405&quot;)
  -log.level value
    	Only log messages with the given severity or above. Valid levels: [debug, info, warn, error, fatal, panic].
  -nutanix.conf string
    	Which Nutanixconf.yml file should be used
  -nutanix.password string
    	Nutanix API User Password
  -nutanix.url string
    	Nutanix URL to connect to API https://nutanix.local.host:9440
  -nutanix.username string
    	Nutanix API User

Example:
./nutanix-exporter_linux_amd64 -list-address ":9406" -nutanix.url https://your-cluster-ip:9440 -nutanix.username your-nutanix-viewer-user -nutanix.password password

Check the runtime Result on the given Port 9406/metric on your prometheus server.

Extention:
Build a Batch File in the GO/BIN Directory to enable a systemd service for automatic starting

Example for batchfile:
#!/bin/bash
#

/path to/go/bin/nutanix-exporter_linux_amd64 -nutanix.url https://cluster-ip:9440 -nutanix.username NAME -nutanix.password PASSWORD



