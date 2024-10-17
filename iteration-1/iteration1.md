### Iteration 1 

Install RHDH with internal postgress and ingress enabled

*Use values.yaml provides a yaml to replace values in.* 

*values-sample.yaml provides a sample with filled in values*

Steps

1.  provide host in `global.host` with dns value where RHDH will be exposed.
2.  import pull secret if required to pull images from Redhat Registry. Provide pull secret in `upstream.backstage.image.pullSecrets` or in `upstream.global.imagePullSecrets`. Refer sample values.yaml 
3.  This example uses `networking.gke.io/managed-certificates` if using self provisioned tls certs, provide secret name in `upstream.ingress.tls.secretName`
4. install helm chart 
``` 
helm install rhdh \
openshift-helm-charts/redhat-developer-hub  \
--namespace rhdh-gke \
--values iteration-1/values-sample.yaml
```


### Prereq ###

Connect to gke cluster
```
gcloud auth login --cred-file=credentials.json

gcloud config set project openenv-gzl5j

gcloud container clusters get-credentials autopilot-cluster-1 --location australia-southeast2
```


Add GSA

```
gcloud iam service-accounts create devhub-gsa \
    --project=openenv-gzl5j
```    

Grant the role required to GSA

```
gcloud projects add-iam-policy-binding openenv-gzl5j \
    --member "serviceAccount:devhub-sa@openenv-gzl5j.iam.gserviceaccount.com" \
    --role "ROLE_NAME"
``` 


Add workloadIdentity role to GSA
```
gcloud iam service-accounts add-iam-policy-binding devhub-gsa@openenv-gzl5j.iam.gserviceaccount.com \
    --role roles/iam.workloadIdentityUser \
    --member "serviceAccount:openenv-gzl5j.svc.id.goog[rhdh-gke/devhub-sa]"
```



