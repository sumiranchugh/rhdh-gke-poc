### Iteration 2 

Enable RHDH with external postgress running in Cloud Storage with workload identity

PreReq:

1. Create GSA DB Account 
2. Create relevant IAM Binding for workload Identity auth to work
3. Provide relevant permissions on Database


Steps
- Update `upstream.postgress.enabled=false`
