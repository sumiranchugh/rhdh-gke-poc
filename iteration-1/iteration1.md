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



Defaulted container "backstage-backend" out of: backstage-backend, cloud-sql-proxy, vault-agent, install-dynamic-plugins (init), vault-agent-init (init)
{"actor":{"actorId":"scaffolder-backend"},"errors":[{"message":"fetch failed","name":"HttpError","stack":"HttpError: fetch failed
    at /opt/app-root/src/node_modules/@octokit/request/dist-node/index.js:146:11
    at process.processTicksAndRejections (node:internal/process/task_queues:95:5)
    at async hook (/opt/app-root/src/node_modules/@octokit/auth-app/dist-node/index.js:352:18)
    at async Object.next (/opt/app-root/src/node_modules/@octokit/rest/node_modules/@octokit/plugin-paginate-rest/dist-node/index.js:76:28)
    at async GithubAppManager.getInstallationData (/opt/app-root/src/node_modules/@backstage/integration/dist/index.cjs.js:1678:30)
    at async /opt/app-root/src/node_modules/@backstage/integration/dist/index.cjs.js:1645:45
    at async Cache.getOrCreateToken (/opt/app-root/src/node_modules/@backstage/integration/dist/index.cjs.js:1592:34)
    at async Promise.all (index 0)
    at async GithubAppCredentialsMux.getAppToken (/opt/app-root/src/node_modules/@backstage/integration/dist/index.cjs.js:1713:21)
    at async SingleInstanceGithubCredentialsProvider.getCredentials (/opt/app-root/src/node_modules/@backstage/integration/dist/index.cjs.js:1771:17)
    at async getOctokitOptions (/opt/app-root/src/dynamic-plugins-root/backstage-plugin-scaffolder-backend-module-github-dynamic-0.4.0/dist/index.cjs.js:157:47)
    at async defaultClientFactory (/opt/app-root/src/dynamic-plugins-root/backstage-plugin-scaffolder-backend-module-github-dynamic-0.4.0/dist/index.cjs.js:2121:27)
    at async Object.handler (/opt/app-root/src/dynamic-plugins-root/backstage-plugin-scaffolder-backend-module-github-dynamic-0.4.0/dist/index.cjs.js:2289:23)
    at async NunjucksWorkflowRunner.executeStep (/opt/app-root/src/node_modules/@backstage/plugin-scaffolder-backend/dist/cjs/router-O4Kmvgd-.cjs.js:2809:9)
    at async NunjucksWorkflowRunner.execute (/opt/app-root/src/node_modules/@backstage/plugin-scaffolder-backend/dist/cjs/router-O4Kmvgd-.cjs.js:2934:9)
    at async TaskWorker.runOneTask (/opt/app-root/src/node_modules/@backstage/plugin-scaffolder-backend/dist/cjs/router-O4Kmvgd-.cjs.js:3226:26)"}],"eventName":"ScaffolderTaskStepExecution","isAuditLog":true,"level":"error","message":"Step Create a pull request (id: publish-pr) of task b180e6cf-b56f-4637-86d2-b0bc937db276 failed","meta":{"isDryRun":false,"stepAction":"publish:github:pull-request","stepId":"publish-pr","stepName":"Create a pull request","taskId":"b180e6cf-b56f-4637-86d2-b0bc937db276","templateRef":"template:default/create-database-environment"},"plugin":"scaffolder","service":"backstage","stage":"completion","status":"failed","timestamp":"2024-10-16 15:00:10"}
{"actor":{"actorId":"scaffolder-backend"},"errors":[{"message":"fetch failed","name":"HttpError"}],"eventName":"ScaffolderTaskExecution","isAuditLog":true,"level":"error","message":"Scaffolding task with taskId: b180e6cf-b56f-4637-86d2-b0bc937db276 failed","meta":{"taskId":"b180e6cf-b56f-4637-86d2-b0bc937db276","taskParameters":{"asset":"fjfhdfh","branchName":"ddd","built_in_db_users":["hdsh"],"database_name":"hdhdsh","db_owner":"dshffh","env":"dhdhd","iam_db_users":["a@anz.com"],"instance_name":"shduhdh","jiraLink":"fffj","prTitle":"fff","repoUrl":"github.service.anz?owner=ssddd&repo=fffffgj"}},"plugin":"scaffolder","service":"backstage","stage":"completion","status":"failed","timestamp":"2024-10-16 15:00:13"}
{"actor":{"actorId":"scaffolder-backend"},"errors":[{"message":"fetch failed","name":"HttpError","stack":"HttpError: fetch failed
    at /opt/app-root/src/node_modules/@octokit/request/dist-node/index.js:146:11
    at process.processTicksAndRejections (node:internal/process/task_queues:95:5)
    at async hook (/opt/app-root/src/node_modules/@octokit/auth-app/dist-node/index.js:352:18)
    at async Object.next (/opt/app-root/src/node_modules/@octokit/rest/node_modules/@octokit/plugin-paginate-rest/dist-node/index.js:76:28)
    at async GithubAppManager.getInstallationData (/opt/app-root/src/node_modules/@backstage/integration/dist/index.cjs.js:1678:30)
    at async /opt/app-root/src/node_modules/@backstage/integration/dist/index.cjs.js:1645:45
    at async Cache.getOrCreateToken (/opt/app-root/src/node_modules/@backstage/integration/dist/index.cjs.js:1592:34)
    at async Promise.all (index 0)
    at async GithubAppCredentialsMux.getAppToken (/opt/app-root/src/node_modules/@backstage/integration/dist/index.cjs.js:1713:21)
    at async SingleInstanceGithubCredentialsProvider.getCredentials (/opt/app-root/src/node_modules/@backstage/integration/dist/index.cjs.js:1771:17)
    at async getOctokitOptions (/opt/app-root/src/dynamic-plugins-root/backstage-plugin-scaffolder-backend-module-github-dynamic-0.4.0/dist/index.cjs.js:157:47)
    at async defaultClientFactory (/opt/app-root/src/dynamic-plugins-root/backstage-plugin-scaffolder-backend-module-github-dynamic-0.4.0/dist/index.cjs.js:2121:27)
    at async Object.handler (/opt/app-root/src/dynamic-plugins-root/backstage-plugin-scaffolder-backend-module-github-dynamic-0.4.0/dist/index.cjs.js:2289:23)
    at async NunjucksWorkflowRunner.executeStep (/opt/app-root/src/node_modules/@backstage/plugin-scaffolder-backend/dist/cjs/router-O4Kmvgd-.cjs.js:2809:9)
    at async NunjucksWorkflowRunner.execute (/opt/app-root/src/node_modules/@backstage/plugin-scaffolder-backend/dist/cjs/router-O4Kmvgd-.cjs.js:2934:9)
    at async TaskWorker.runOneTask (/opt/app-root/src/node_modules/@backstage/plugin-scaffolder-backend/dist/cjs/router-O4Kmvgd-.cjs.js:3226:26)"}],"eventName":"ScaffolderTaskStepExecution","isAuditLog":true,"level":"error","message":"Step Create a pull request (id: publish-pr) of task 7a19b109-8e82-4c1f-8a65-933c85f42ace failed","meta":{"isDryRun":false,"stepAction":"publish:github:pull-request","stepId":"publish-pr","stepName":"Create a pull request","taskId":"7a19b109-8e82-4c1f-8a65-933c85f42ace","templateRef":"template:default/create-database-environment"},"plugin":"scaffolder","service":"backstage","stage":"completion","status":"failed","timestamp":"2024-10-16 15:00:14"}
{"actor":{"actorId":"scaffolder-backend"},"errors":[{"message":"fetch failed","name":"HttpError"}],"eventName":"ScaffolderTaskExecution","isAuditLog":true,"level":"error","message":"Scaffolding task with taskId: 7a19b109-8e82-4c1f-8a65-933c85f42ace failed","meta":{"taskId":"7a19b109-8e82-4c1f-8a65-933c85f42ace","taskParameters":{"asset":"fjfhdfh","branchName":"ddd","built_in_db_users":["hdsh"],"database_name":"hdhdsh","db_owner":"dshffh","env":"dhdhd","iam_db_users":["a@anz.com"],"instance_name":"shduhdh","jiraLink":"fffj","prTitle":"fff","repoUrl":"github.service.anz?owner=ssddd&repo=fffffgj"}},"plugin":"scaffolder","service":"backstage","stage":"completion","status":"failed","timestamp":"2024-10-16 15:00:16"}





{"class":"GithubEntityProvider$1","level":"error","message":"github-provider:systemInventorySpecs refresh failed, HttpError: fetch failed fetch failed","name":"HttpError","plugin":"catalog","request":{"headers":{"accept":"application/vnd.github.v3+json","authorization":"bearer [REDACTED]","user-agent":"octokit-rest.js/19.0.13 octokit-core.js/4.2.4 Node.js/20.16.0 (linux; x64)"},"method":"GET","request":{},"url":"https://github.service.anz/api/v3/app/installations"},"service":"backstage","stack":"HttpError: fetch failed
    at /opt/app-root/src/node_modules/@octokit/request/dist-node/index.js:146:11
    at process.processTicksAndRejections (node:internal/process/task_queues:95:5)
    at async hook (/opt/app-root/src/node_modules/@octokit/auth-app/dist-node/index.js:352:18)
    at async Object.next (/opt/app-root/src/node_modules/@octokit/rest/node_modules/@octokit/plugin-paginate-rest/dist-node/index.js:76:28)
    at async GithubAppManager.getInstallationData (/opt/app-root/src/node_modules/@backstage/integration/dist/index.cjs.js:1678:30)
    at async /opt/app-root/src/node_modules/@backstage/integration/dist/index.cjs.js:1645:45
    at async Cache.getOrCreateToken (/opt/app-root/src/node_modules/@backstage/integration/dist/index.cjs.js:1592:34)
    at async Promise.all (index 0)
    at async GithubAppCredentialsMux.getAppToken (/opt/app-root/src/node_modules/@backstage/integration/dist/index.cjs.js:1713:21)
    at async SingleInstanceGithubCredentialsProvider.getCredentials (/opt/app-root/src/node_modules/@backstage/integration/dist/index.cjs.js:1771:17)
    at async GithubEntityProvider$1.createGraphqlClient (/opt/app-root/src/dynamic-plugins-root/backstage-plugin-catalog-backend-module-github-dynamic-0.6.5/dist/index.cjs.js:851:25)
    at async GithubEntityProvider$1.findCatalogFiles (/opt/app-root/src/dynamic-plugins-root/backstage-plugin-catalog-backend-module-github-dynamic-0.6.5/dist/index.cjs.js:863:20)
    at async GithubEntityProvider$1.refresh (/opt/app-root/src/dynamic-plugins-root/backstage-plugin-catalog-backend-module-github-dynamic-0.6.5/dist/index.cjs.js:836:21)
    at async fn (/opt/app-root/src/dynamic-plugins-root/backstage-plugin-catalog-backend-module-github-dynamic-0.6.5/dist/index.cjs.js:821:13)
    at async TaskWorker.fn (/opt/app-root/src/node_modules/@backstage/backend-defaults/dist/scheduler.cjs.js:552:9)
    at async TaskWorker.runOnce (/opt/app-root/src/node_modules/@backstage/backend-defaults/dist/scheduler.cjs.js:310:7)","status":500,"target":"github-provider:systemInventorySpecs","taskId":"github-provider:systemInventorySpecs:refresh","taskInstanceId":"30ae361d-78d4-4204-a879-e8a278214f79","timestamp":"2024-10-16 15:16:25"}
{"actor":{"actorId":"scaffolder-backend"},"errors":[{"message":"fetch failed","name":"HttpError","stack":"HttpError: fetch failed
    at /opt/app-root/src/node_modules/@octokit/request/dist-node/index.js:146:11
    at process.processTicksAndRejections (node:internal/process/task_queues:95:5)
    at async hook (/opt/app-root/src/node_modules/@octokit/auth-app/dist-node/index.js:352:18)
    at async Object.next (/opt/app-root/src/node_modules/@octokit/rest/node_modules/@octokit/plugin-paginate-rest/dist-node/index.js:76:28)
    at async GithubAppManager.getInstallationData (/opt/app-root/src/node_modules/@backstage/integration/dist/index.cjs.js:1678:30)
    at async /opt/app-root/src/node_modules/@backstage/integration/dist/index.cjs.js:1645:45
    at async Cache.getOrCreateToken (/opt/app-root/src/node_modules/@backstage/integration/dist/index.cjs.js:1592:34)
    at async Promise.all (index 0)
    at async GithubAppCredentialsMux.getAppToken (/opt/app-root/src/node_modules/@backstage/integration/dist/index.cjs.js:1713:21)
    at async SingleInstanceGithubCredentialsProvider.getCredentials (/opt/app-root/src/node_modules/@backstage/integration/dist/index.cjs.js:1771:17)
    at async getOctokitOptions (/opt/app-root/src/dynamic-plugins-root/backstage-plugin-scaffolder-backend-module-github-dynamic-0.4.0/dist/index.cjs.js:157:47)
    at async defaultClientFactory (/opt/app-root/src/dynamic-plugins-root/backstage-plugin-scaffolder-backend-module-github-dynamic-0.4.0/dist/index.cjs.js:2121:27)
    at async Object.handler (/opt/app-root/src/dynamic-plugins-root/backstage-plugin-scaffolder-backend-module-github-dynamic-0.4.0/dist/index.cjs.js:2289:23)
    at async NunjucksWorkflowRunner.executeStep (/opt/app-root/src/node_modules/@backstage/plugin-scaffolder-backend/dist/cjs/router-O4Kmvgd-.cjs.js:2809:9)
    at async NunjucksWorkflowRunner.execute (/opt/app-root/src/node_modules/@backstage/plugin-scaffolder-backend/dist/cjs/router-O4Kmvgd-.cjs.js:2934:9)
    at async TaskWorker.runOneTask (/opt/app-root/src/node_modules/@backstage/plugin-scaffolder-backend/dist/cjs/router-O4Kmvgd-.cjs.js:3226:26)"}],"eventName":"ScaffolderTaskStepExecution","isAuditLog":true,"level":"error","message":"Step Create a pull request (id: publishPr) of task 87a693b7-0907-4fc5-b5f4-c9b69d6a0644 failed","meta":{"isDryRun":false,"stepAction":"publish:github:pull-request","stepId":"publishPr","stepName":"Create a pull request","taskId":"87a693b7-0907-4fc5-b5f4-c9b69d6a0644","templateRef":"template:default/create-repo"},"plugin":"scaffolder","service":"backstage","stage":"completion","status":"failed","timestamp":"2024-10-16 15:17:13"}
{"actor":{"actorId":"scaffolder-backend"},"errors":[{"message":"fetch failed","name":"HttpError"}],"eventName":"ScaffolderTaskExecution","isAuditLog":true,"level":"error","message":"Scaffolding task with taskId: 87a693b7-0907-4fc5-b5f4-c9b69d6a0644 failed","meta":{"taskId":"87a693b7-0907-4fc5-b5f4-c9b69d6a0644","taskParameters":{"allow_merge_commit":true,"allow_rebase_merge":true,"jiraLink":"https://jira.service.anz/browse/PSRE-15656","members":["chauhai1"],"organization":"PSRE","repoDescription":"test-repo","repoName":"test-repo","required_approving_review_count":1,"selectedGroup":"psre","teamDescription":"Sample request","teamName":"octopod","write_access_team":["octopod"]}},"plugin":"scaffolder","service":"backstage","stage":"completion","status":"failed","timestamp":"2024-10-16 15:17:13"}






{"class":"GithubEntityProvider$1","level":"error","message":"github-provider:componentInventorySpecs refresh failed, HttpError: fetch failed fetch failed","name":"HttpError","plugin":"catalog","request":{"headers":{"accept":"application/vnd.github.v3+json","authorization":"bearer [REDACTED]","user-agent":"octokit-rest.js/19.0.13 octokit-core.js/4.2.4 Node.js/20.16.0 (linux; x64)"},"method":"GET","request":{},"url":"https://github.service.anz/api/v3/app/installations"},"service":"backstage","stack":"HttpError: fetch failed
    at /opt/app-root/src/node_modules/@octokit/request/dist-node/index.js:146:11
    at process.processTicksAndRejections (node:internal/process/task_queues:95:5)
    at async hook (/opt/app-root/src/node_modules/@octokit/auth-app/dist-node/index.js:352:18)
    at async Object.next (/opt/app-root/src/node_modules/@octokit/rest/node_modules/@octokit/plugin-paginate-rest/dist-node/index.js:76:28)
    at async GithubAppManager.getInstallationData (/opt/app-root/src/node_modules/@backstage/integration/dist/index.cjs.js:1678:30)
    at async /opt/app-root/src/node_modules/@backstage/integration/dist/index.cjs.js:1645:45
    at async Cache.getOrCreateToken (/opt/app-root/src/node_modules/@backstage/integration/dist/index.cjs.js:1592:34)
    at async Promise.all (index 0)
    at async GithubAppCredentialsMux.getAppToken (/opt/app-root/src/node_modules/@backstage/integration/dist/index.cjs.js:1713:21)
    at async SingleInstanceGithubCredentialsProvider.getCredentials (/opt/app-root/src/node_modules/@backstage/integration/dist/index.cjs.js:1771:17)
    at async GithubEntityProvider$1.createGraphqlClient (/opt/app-root/src/dynamic-plugins-root/backstage-plugin-catalog-backend-module-github-dynamic-0.6.5/dist/index.cjs.js:851:25)
    at async GithubEntityProvider$1.findCatalogFiles (/opt/app-root/src/dynamic-plugins-root/backstage-plugin-catalog-backend-module-github-dynamic-0.6.5/dist/index.cjs.js:863:20)
    at async GithubEntityProvider$1.refresh (/opt/app-root/src/dynamic-plugins-root/backstage-plugin-catalog-backend-module-github-dynamic-0.6.5/dist/index.cjs.js:836:21)
    at async fn (/opt/app-root/src/dynamic-plugins-root/backstage-plugin-catalog-backend-module-github-dynamic-0.6.5/dist/index.cjs.js:821:13)
    at async TaskWorker.fn (/opt/app-root/src/node_modules/@backstage/backend-defaults/dist/scheduler.cjs.js:552:9)
    at async TaskWorker.runOnce (/opt/app-root/src/node_modules/@backstage/backend-defaults/dist/scheduler.cjs.js:310:7)","status":500,"target":"github-provider:componentInventorySpecs","taskId":"github-provider:componentInventorySpecs:refresh","taskInstanceId":"bb2d38fc-c905-4737-82b9-8148abff255b","timestamp":"2024-10-16 15:46:26"}




    at async Cache.getOrCreateToken (/opt/app-root/src/node_modules/@backstage/integration/dist/index.cjs.js:1592:34)
25    at async Promise.all (index 0)
26    at async GithubAppCredentialsMux.getAppToken (/opt/app-root/src/node_modules/@backstage/integration/dist/index.cjs.js:1713:21)
27    at async SingleInstanceGithubCredentialsProvider.getCredentials (/opt/app-root/src/node_modules/@backstage/integration/dist/index.cjs.js:1771:17)
28    at async getOctokitOptions (/opt/app-root/src/dynamic-plugins-root/backstage-plugin-scaffolder-backend-module-github-dynamic-0.4.0/dist/index.cjs.js:157:47)
29    at async defaultClientFactory (/opt/app-root/src/dynamic-plugins-root/backstage-plugin-scaffolder-backend-module-github-dynamic-0.4.0/dist/index.cjs.js:2121:27)
30    at async Object.handler (/opt/app-root/src/dynamic-plugins-root/backstage-plugin-scaffolder-backend-module-github-dynamic-0.4.0/dist/index.cjs.js:2289:23)
31    at async NunjucksWorkflowRunner.executeStep (/opt/app-root/src/node_modules/@backstage/plugin-scaffolder-backend/dist/cjs/router-O4Kmvgd-.cjs.js:2809:9)
32    at async NunjucksWorkflowRunner.execute (/opt/app-root/src/node_modules/@backstage/plugin-scaffolder-backend/dist/cjs/router-O4Kmvgd-.cjs.js:2934:9)
33    at async TaskWorker.runOneTask (/opt/app-root/src/node_modules/@backstage/plugin-scaffolder-backend/dist/cjs/router-O4Kmvgd-.cjs.js:3226:26)
has context menu


has context menu


{"isDryRun":true,"location":{"target":"https://github.service.anz/PSRE/symphony-catalog/blob/main/all-catalog.yaml","type":"url"}},"plugin":"catalog","request":{"body":{"target":"https://github.service.anz/PSRE/symphony-catalog/blob/main/all-catalog.yaml","type":"url"},"method":"POST","params":{},"query":{"dryRun":"true"},"url":"/api/catalog/locations?dryRun=true"},"service":"backstage","stage":"completion","status":"failed","timestamp":"2024-10-16 18:38:42"}




2024-10-16T06:11:53.781Z Beginning step Create Json file
22024-10-16T06:11:54.078Z info: Created JSON file at /tmp/0ef74e47-ea51-4122-a650-3eb9cc43ccb7/cloudsql.json
32024-10-16T06:11:54.078Z info: Content of cloudsql.json:
4{
5  "instance_name": "sssddss",
6  "database_name": "hhhddhshshsj",
7  "iam_db_users": [
8    "ssssssss@anz.com, Opens in a new window"
9  ],
10  "db_owner": "fffff",
11  "built_in_db_users": [
12    "ssssss"
13  ]
14}
152024-10-16T06:11:54.171Z Finished step Create Json file
162024-10-16T06:11:54.182Z Beginning step Create a pull request
172024-10-16T06:11:54.973Z HttpError: fetch failed    
at /opt/app-root/src/node_modules/@octokit/request/dist-node/index.js:146:11
19    at process.processTicksAndRejections (node:internal/process/task_queues:95:5)
20    at async hook (/opt/app-root/src/node_modules/@octokit/auth-app/dist-node/index.js:352:18)
21    at async Object.next, Opens in a new window (/opt/app-root/src/node_modules/@octokit/rest/node_modules/@octokit/plugin-paginate-rest/dist-node/index.js:76:28)
22    at async GithubAppManager.getInstallationData (/opt/app-root/src/node_modules/@backstage/integration/dist/index.cjs.js:1678:30)
23    at async /opt/app-root/src/node_modules/@backstage/integration/dist/index.cjs.js:1645:45
24    at async Cache.getOrCreateToken (/opt/app-root/src/node_modules/@backstage/integration/dist/index.cjs.js:1592:34)
25    at async Promise.all (index 0)
has context menu


has context menu