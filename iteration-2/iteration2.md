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

```
gloud iam service-accounts create rhdh-gsa --display-name="RHDH GSA"
```

add annotation to service account 

```
  serviceAccount: 
    name: devhub-sa
    create: true
    annotations:
      iam.gke.io/gcp-service-account: rhdh-gsa@openenv-b4g6h.iam.gserviceaccount.com
```

```
gcloud iam service-accounts add-iam-policy-binding \
rhdh-gsa@${PROJECT_ID}.iam.gserviceaccount.com \
  --role roles/iam.workloadIdentityUser
  --member "serviceAccount.${PROJECT_ID}.svc.id.goog[${NAMESPACE}/{KSA}]"

gcloud iam service-accounts add-iam-policy-binding \
rhdh-gsa@${PROJECT_ID}.iam.gserviceaccount.com \
  --role roles/storage.objectUser
  --member "serviceAccount.${PROJECT_ID}.svc.id.goog[${NAMESPACE}/{KSA}]"


 curl -H "Metadata-Flavor: Google" http://metadata.google.internal/computeMetadata/v1/instance/service-accounts/default/?recursive=true
 ```

Steps
- Enable Techdocs plugin with the configuration to connect to GCS Bucket.

```
pluginConfig:
  techdocs:
    publisher:
    type: 'googleGcs'
    googleGcs:
        bucketName: 'rhdh-bucket'
```              