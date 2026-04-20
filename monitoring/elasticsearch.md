# Elasricsearch installation

1. Add helm repo and update


```bash
helm repo add elastic https://helm.elastic.co

helm update
```

2. Install with helm


```bash
helm upgrade --install <release-name> elastic/elasticsearch --version 8.5.1 -f value.yaml -n <namespace> --create-namespace
```


## Important Values
volumeClaimTemplate.resources.requests.storage: size of persistence storage
volumeClaimTemplate.storageClassName: Name of the storage class
ingress.enabled: To enable the ingress(If used)
protocols: change to https

# Shared file system repository backup 

1. Install Kibana UI

```bash
helm upgrade --install kibana elastic/kibana\
  --version 7.17.3 \
  --namespace mpt-ingest \
  -f kibana-values.yaml
```

kibana-values.yaml

```yml
kibanaConfig:
  elasticsearch.hosts: '["http://elasticsearch-master.mpt-ingest.svc.cluster.local:9200"]'
```


2. Create pvc on the cluster and wanted namespace with RWX mode

3. Mount volume with these key
 
 
```yml
extraVolumes:
- name: snapshot-repo
  persistentVolumeClaim:
    claimName: es-backup-pvc
# - name: extras
#   emptyDir: {}

extraVolumeMounts:
- name: snapshot-repo
  mountPath: /mount/backups
```

4. Use api request to register the repository on Kibana by go to dev tools and use this api request
```rest
PUT _snapshot/my_fs_backup
{
  "type": "fs",
  "settings": {
    "location": "/mount/backups/my_fs_backup_location"
  }
}
```

5. Create policy for schedule back-up

```rest
PUT _slm/policy/nightly-snapshots
{
  "schedule": "0 30 1 * * ?",       
  "name": "<nightly-snap-{now/d}>", 
  "repository": "my_repository",    
  "config": {
    "indices": "*",                 
    "include_global_state": true    
  },
  "retention": {                    
    "expire_after": "30d",
    "min_count": 5,
    "max_count": 50
  }
}
```

https://www.elastic.co/docs/troubleshoot/monitoring/unavailable-shards (Elastic search api troubleshoot)

