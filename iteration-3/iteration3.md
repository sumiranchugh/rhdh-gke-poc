### Iteration 2 

Enable RHDH with CloudSQL with workload identity

PreReq:

1. Create GSA DB Account 
2. Create relevant IAM Binding for workload Identity auth to work
3. Provide relevant permissions on Database


Steps
- Update `upstream.postgress.enabled=false`

- create cloud sql instance

```
gcloud sql instances create prod-instance --database-version=POSTGRES_15 --cpu=1 --memory=8GiB --zone=australia-southeast2 --root-password=devhub
```

- add permissions for GSA to cloud sql 
```
gcloud projects add-iam-policy-binding ${PROJECT_ID}  --member "serviceAccount:${GSA}@${PROJECT_ID}.iam.gserviceaccount.com" --role "roles/cloudsql.client"
```
- add sidecar container for cloud sql access

```
extraContainers:
      - name: cloud-sql-proxy
        image: gcr.io/cloud-sql-connectors/cloud-sql-proxy
        args:
          - "--structured-logs"
          - "--port=5432"
          - "openenv-gzl5j:australia-southeast2:devhub"
          - "--private-ip"
        securityContext:
          runAsNonRoot: true
```

- configure to use cloudsql instance

```
        database:
          client: pg
          # ensureExists: true
          # pluginDivisionMode: 'schema'
          # knexConfig:
          #   searchPath: ['knex', 'public']
          connection:
            host: '127.0.0.1'
            port: '5432'
            user: ${DB_USER}
            password: ${DB_PASSWORD}
          prefix: devhub_plugin_   
 ```

 - enable cloud sql admin api

 `https://console.cloud.google.com/apis/library/sqladmin.googleapis.com?project=openenv-gzl5j`        