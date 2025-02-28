# Source
https://www.youtube.com/watch?v=MeU5_k9ssrs

# Argo CD
- Is a continuous delivery tool
- it's installed directly inside the kluster, so it knows what are the pod status after a
`kubectl apply...`
- it watches a remote repository for changes in manifests file in order to apply them to the cluster
- arogcd is based on gitops

## Separate git repository
- Separate git repository for application source and application configuration 
(k8s manifest files)
- even separate git repostory for system configgurations

### Why ?
- Different manifests can change independently
    - deployment, service, configMap, Secret...

## Benefits
- Git as source of truth
- version controll on cluster changes
- easy rollback
- cluster disaster recovery
- access control with git

# Practice
