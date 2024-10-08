### Iteration 2 

Enable Techdocs Plugin with GCS Bucket with workload identity

PreReq:

1. Create GCS Bucket
2. Update `upstream.serviceAccount.name` & `upstream.serviceAccount.create` with service account to be used by workload identity
2. Create relevant IAM Binding for workload Identity auth to work

#### THIS Didn't work
```
gcloud storage buckets add-iam-policy-binding gs://rhdh-bucket \
--role=roles/storage.objectUser \
--member=principal://iam.googleapis.com/projects/${PROJECT_NUMBER}/locations/global/workloadIdentityPools/${PROJECT_ID}.svc.id.goog/subject/ns/${NAMESPACE}/sa/${KSA} \
--condition=None
```

#### This does work but requires storage admin role

```
gcloud iam service-accounts create ${GSA} --display-name="RHDH GSA"
```

add annotation to service account 

```
  serviceAccount: 
    name: ${KSA}
    create: true
    annotations:
      iam.gke.io/gcp-service-account: ${GSA}@${PROJECT_ID}.iam.gserviceaccount.com
```

```
gcloud iam service-accounts add-iam-policy-binding \
${GSA}@${PROJECT_ID}.iam.gserviceaccount.com \
  --role roles/iam.workloadIdentityUser \
  --member "serviceAccount:${PROJECT_ID}.svc.id.goog[${NAMESPACE}/${KSA}]"


gcloud storage buckets create gs://devhub-bucket  

gcloud storage buckets add-iam-policy-binding gs://devhub-bucket --member "serviceAccount:${GSA}@${PROJECT_ID}.iam.gserviceaccount.com" --role "roles/storage.objectAdmin"       
 


 curl -H "Metadata-Flavor: Google" http://metadata.google.internal/computeMetadata/v1/instance/service-accounts/default/?recursive=true
 ```

Steps
- Enable Techdocs plugin with the configuration to connect to GCS Bucket.

```
pluginConfig:
  techdocs:
    builder: external
    publisher:
      type: 'googleGcs'
      googleGcs:
        bucketName: '${BUCKET}'
```              