# THIS IS FOR MIKROTIK MONITORING

1. Install snmp_exporter in your machine server from this url with wget.
https://github.com/prometheus/snmp_exporter/releases

2. tar file and after you tar the file move it to /etc/snmp_exporter.

3. Create the service file. 


```bash
sudo nano /etc/systemd/system/snmp_exporter.service
```


```ini
[Unit]
Description=SNMP Exporter
Wants=network-online.target
After=network-online.target
[Service]
ExecStart=/etc/snmp_exporter/snmp_exporter --config.file=/etc/snmp_exporter/snmp.yml
Restart=always
[Install]
WantedBy=multi-user.target
```


3. Enable the service.


```bash
sudo systemctl enable snmp_exporter.service

sudo systemctl restart snmp_exporter.service
```


## Note 
Inspect the distros carefully.


4. Config the prometheus.yaml in the scraping config section to scrape from mikrotik modules like this.


```yml
- job_name: snmp-exporter
  static_configs:
  - targets:
    - 192.168.24.1
  metrics_path: /snmp
  params:
    module: [mikrotik]
  relabel_configs:
    - source_labels: [__address__]
      target_label: __param_target
    - source_labels: [__param_target]
      target_label: instance
    - target_label: __address__
      replacement: 192.168.24.46:9116
- job_name: snmp-system
  static_configs:
  - targets:
    - 192.168.24.1
  metrics_path: /snmp
  params:
    module: [system]
  relabel_configs:
    - source_labels: [__address__]
      target_label: __param_target
    - source_labels: [__param_target]
      target_label: instance
    - target_label: __address__
      replacement: 192.168.24.46:9116
- job_name: snmp-hrDevice
  static_configs:
  - targets:
    - 192.168.24.1
  metrics_path: /snmp
  params:
    module: [hrDevice]
  relabel_configs:
    - source_labels: [__address__]
      target_label: __param_target
    - source_labels: [__param_target]
      target_label: instance
    - target_label: __address__
      replacement: 192.168.24.46:9116
- job_name: snmp-hrStorage
  static_configs:
  - targets:
    - 192.168.24.1
  metrics_path: /snmp
  params:
    module: [hrStorage]
  relabel_configs:
    - source_labels: [__address__]
      target_label: __param_target
    - source_labels: [__param_target]
      target_label: instance
    - target_label: __address__
      replacement: 192.168.24.46:9116
- job_name: snmp-if_mib
  static_configs:
  - targets:
    - 192.168.24.1
  metrics_path: /snmp
  params:
    module: [if_mib]
  relabel_configs:
    - source_labels: [__address__]
      target_label: __param_target
    - source_labels: [__param_target]
      target_label: instance
    - target_label: __address__
      replacement: 192.168.24.46:9116
```

Or look through snmp.yaml to see module.