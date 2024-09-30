### Iteration 2 

Enable RHDH with CloudSQL with workload identity

PreReq:

1. Create GSA DB Account 
2. Create relevant IAM Binding for workload Identity auth to work
3. Provide relevant permissions on Database


Steps
- Update `upstream.postgress.enabled=false`
- add sidecar for cloud-sql-pro
```
gcloud projects add-iam-policy-binding ${PROJECT_ID}  --member "serviceAccount:${GSA}@${PROJECT_ID}.iam.gserviceaccount.com" --role "roles/cloudsql.client"
```