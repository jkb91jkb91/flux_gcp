# INFO

1.) Install Flux





# 1 INSTALL FLUX
```
curl -s https://fluxcd.io/install.sh | sudo bash
```

# 2 ARCHITECTURE

```
├── apps/
│   ├── php-app/
│   │    ├──   deployment-php.yaml
│   |    ├──   deployment-sql.yaml
|   |    ├──   ingress.yaml
|   |    ├──   deployment-sql.yaml
│   |    └──   configmap.yaml
│   ├── production/
│   │   └── kustomization.yaml      <<<< resources :- namespace: production(OVERWRITES)
```

# 3 BOOTSTRAP >> EXECUTE THIS COMMAND >> THIS WILL INSTALL FLUX-SYSTEM IN YOUR CLUSTER

```
flux bootstrap github --owner=jkb91jkb91 --repository=PASTE_HERE_REPO_NAME --branch=PASTE_HERE_BRANCH --path=apps/production --personal
flux bootstrap github --owner=jkb91jkb91 --repository=flux_gcp --branch=master --path=apps/production --personal
```
This will install flux-system and get all components to cluster
```
├── apps/
│   ├── php-app/
│   │    ├──   deployment-php.yaml
│   |    ├──   deployment-sql.yaml
|   |    ├──   ingress.yaml
|   |    ├──   deployment-sql.yaml
│   |    └──   configmap.yaml
│   ├── production/
│   │   └── kustomization.yaml
│   |   └── flux-system <<<<<<<<<<<<<<<<<<<<<<<<<<<<<<<<<<<<<<<<
```

# TESTING
```
kubectl get ingresses -n production
```
```
kuba@cloudshell:~/manifests (gowno-439010)$ k get ingress -n production
NAME             CLASS    HOSTS   ADDRESS         PORTS   AGE
ingress-basics   <none>   *       34.54.140.202   80      7m26s
```

GO >> http://34.54.140.202

NOW CHANGE in deployment-php.yaml IMAGE and push to REPOSITORY





