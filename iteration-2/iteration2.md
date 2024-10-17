### Iteration 2 

Enable Techdocs Plugin with GCS Bucket with workload identity


#### THIS Didn't work
```
gcloud storage buckets add-iam-policy-binding gs://rhdh-bucket \
--role=roles/storage.objectUser \
--member=principal://iam.googleapis.com/projects/${PROJECT_NUMBER}/locations/global/workloadIdentityPools/${PROJECT_ID}.svc.id.goog/subject/ns/${NAMESPACE}/sa/${KSA} \
--condition=None
```

#### This does work but requires storage admin role

1. Create Google IAM service account
```
gcloud iam service-accounts create ${GSA} --display-name="RHDH GSA"
```

2. add annotation to service account 

```
  serviceAccount: 
    name: ${KSA}
    create: true
    annotations:
      iam.gke.io/gcp-service-account: ${GSA}@${PROJECT_ID}.iam.gserviceaccount.com
```

3. Add workload identity role to the Google Service Account 
```
gcloud iam service-accounts add-iam-policy-binding \
${GSA}@${PROJECT_ID}.iam.gserviceaccount.com \
  --role roles/iam.workloadIdentityUser \
  --member "serviceAccount:${PROJECT_ID}.svc.id.goog[${NAMESPACE}/${KSA}]"
```

4. Create GCS bucket

`gcloud storage buckets create gs://rhdh-bucket`
  
5. Add Object admin role to the bucket

```
gcloud storage buckets add-iam-policy-binding gs://rhdh-bucket --member "serviceAccount:${GSA}@${PROJECT_ID}.iam.gserviceaccount.com" --role "roles/storage.objectAdmin"       
```

add another role with permission  `storage.get.buckets` i.e. `roles/storage.legacyBucketReader` or a custom role

```
gcloud storage buckets add-iam-policy-binding gs://rhdh-bucket --member "serviceAccount:${GSA}@${PROJECT_ID}.iam.gserviceaccount.com" --role "roles/storage.legacyBucketReader"  
```

6. Enable Techdocs plugin with the configuration to connect to GCS Bucket.

```
pluginConfig:
  techdocs:
    builder: external
    publisher:
      type: 'googleGcs'
      googleGcs:
        bucketName: '${BUCKET}'
```              

Troubleshoot

from the pod execute following url to verify the GSA impersonated by the pod

`curl -H "Metadata-Flavor: Google" http://metadata.google.internal/computeMetadata/v1/instance/service-accounts/default/?recursive=true`