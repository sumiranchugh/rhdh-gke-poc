### Iteration 2 

Enable RHDH with AD RBAC 

PreReq:

1. AZURE Entra ID App Registration
2. MSGraph sync enabled


Steps
- Create policy-config cm with policies and group mappings. Below is an illustration, please update as per your requirements.

```
kind: ConfigMap
apiVersion: v1
metadata:
  name: rbac-policy
data:
  rbac-policy.csv: |
    # Admin Role
    # Catalog permissions
    p, role:default/admins, catalog.entity.read, read, allow
    p, role:default/admins, catalog.entity.create, create, allow
    p, role:default/admins, catalog.entity.refresh, update, allow
    p, role:default/admins, catalog.entity.delete, delete, allow
    p, role:default/admins, catalog.location.read, read, allow
    p, role:default/admins, catalog.location.create, create, allow
    p, role:default/admins, catalog.location.delete, delete, allow
    # Scaffolder permissions
    p, role:default/admins, scaffolder.action.execute, use, allow
    p, role:default/admins, scaffolder.template.parameter.read, read, allow
    p, role:default/admins, scaffolder.template.step.read, read, allow
    p, role:default/admins, scaffolder.task.create, create, allow
    p, role:default/admins, scaffolder.task.cancel, use, allow
    p, role:default/admins, scaffolder.task.read, read, allow
    # RBAC permissions
    p, role:default/admins, policy.entity.read, read, allow
    p, role:default/admins, policy.entity.create, create, allow
    p, role:default/admins, policy.entity.update, update, allow
    p, role:default/admins, policy.entity.delete, delete, allow
    # Kubernetes permissions
    p, role:default/admins, kubernetes.proxy, use, allow
    # OCM permissions
    p, role:default/admins, ocm.entity.read, read, allow
    p, role:default/admins, ocm.cluster.read, read, allow
    # Topology permissions
    p, role:default/admins, topology.view.read, read, allow


    # teama Role
    # Catalog permissions
    p, role:default/teama, catalog.entity.read, read, allow
    p, role:default/teama, catalog.entity.create, create, allow
    p, role:default/teama, catalog.entity.refresh, update, allow
    p, role:default/teama, catalog.entity.delete, delete, allow
    p, role:default/teama, catalog.location.read, read, allow
    p, role:default/teama, catalog.location.create, create, allow
    p, role:default/teama, catalog.location.delete, delete, allow
    # Scaffolder permissions
    p, role:default/teama, scaffolder.action.execute, use, allow
    p, role:default/teama, scaffolder.template.parameter.read, read, allow
    p, role:default/teama, scaffolder.template.step.read, read, allow
    p, role:default/teama, scaffolder.task.create, create, allow
    p, role:default/teama, scaffolder.task.cancel, use, allow
    p, role:default/teama, scaffolder.task.read, read, allow
    # RBAC permissions
    p, role:default/teama, policy.entity.read, read, allow
    p, role:default/teama, policy.entity.create, create, allow
    p, role:default/teama, policy.entity.update, update, allow
    p, role:default/teama, policy.entity.delete, delete, allow
    # Kubernetes permissions
    #p, role:default/teama, kubernetes.proxy, use, allow
    # Topology permissions
    #p, role:default/teama, topology.view.read, read, allow

    # teamb Role
    p, role:default/teamb, catalog.entity.read, read, deny

    # Groups
    g, group:default/teama, role:default/teama
    g, group:default/teamb, role:default/teamb
```

- create conditional policies config map

```
kind: ConfigMap
apiVersion: v1
metadata:
  name: conditional-policy
data:
  conditional-policy.yaml: |
        ---
        result: CONDITIONAL
        roleEntityRef: 'role:default/test'
        pluginId: catalog
        resourceType: catalog-entity
        permissionMapping:
        - read
        - update
        conditions:
        rule: IS_ENTITY_OWNER
        resourceType: catalog-entity
        params:
            claims:
            - 'group:default/teama'
            - 'group:default/teamb'
        ---
        result: CONDITIONAL
        roleEntityRef: 'role:default/test'
        pluginId: catalog
        resourceType: catalog-entity
        permissionMapping:
        - delete
        conditions:
        rule: IS_ENTITY_OWNER
        resourceType: catalog-entity
        params:
            claims:
            - 'group:default/teama'
```


- update values.yaml with below configuration to mount policy to developer hub:

```
    extraVolumeMounts:
      - mountPath: /opt/app-root/src/rbac-policy.csv
        subPath: rbac-policy.csv
        name: rbac-policy
      - mountPath: /opt/app-root/src/conditional-policy.yaml
        subPath: conditional-policy.yaml
        name: conditional-policy   
      - mountPath: /opt/app-root/src/app-config-rhdh.yaml
        name: app-config-rhdh
        subPath: app-config-rhdh.yaml
      - mountPath: /opt/app-root/src/app-config-from-configmap.yaml
        name: backstage-app-config
        subPath: app-config.yaml
      - mountPath: /opt/app-root/src/dynamic-plugins-root
        name: dynamic-plugins-root
      - mountPath: /var/log/audit
        name: audit-log-data     
    extraVolumes:
      - name: rbac-policy
        configMap:
          name: rbac-policy
      - name: conditional-policy
        configMap:
          name: conditional-policy    
      - name: dynamic-plugins-root
        ephemeral:
          volumeClaimTemplate:
            spec:
              accessModes:
              - ReadWriteOnce
              resources:
                requests:
                  storage: 2Gi
      - configMap:
          defaultMode: 420
          name: dynamic-plugins
          optional: true
        name: dynamic-plugins
      - name: dynamic-plugins-npmrc
        secret:
          defaultMode: 420
          optional: true
          secretName: dynamic-plugins-npmrc             
      - name: dynamic-plugins-registry-auth
        secret:
          defaultMode: 416
          optional: true
          secretName: rhdh-dynamic-plugins-registry-auth
      - name: npmcacache
        emptyDir: {}
      - name: audit-log-data
        persistentVolumeClaim:
          claimName: rhdh-audit-log  
```

- update app-config.yaml to update permissions config 

```
permission:
  enabled: true 
  rbac:
   policyFileReload: true
   policies-csv-file: /opt/app-root/src/rbac-policy.csv
   conditionalPoliciesFile: /opt/app-root/src/conditional-policies.yaml
```        