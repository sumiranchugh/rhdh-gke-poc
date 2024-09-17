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
openshift-helm-charts-ci-1.3/redhat-developer-hub  \
--namespace rhdh-gke \
--values iteration-1/values-sample.yaml
--devel
```