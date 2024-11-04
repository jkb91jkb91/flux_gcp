# INFO

1.) Install Flux  
2.) Architecture  
3.) Bootstrap  
4.) Testing  
5.) Add Service Grafana  




# 1 INSTALL FLUX AND CREATE PRODUCTION NAMESPACE IN CLUSTER
```
curl -s https://fluxcd.io/install.sh | sudo bash
```
```
kubectl create ns production
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

flux bootstrap github --owner=PASTE_YOUR_GITHUB_USER --repository=PASTE_HERE_REPO_NAME --branch=PASTE_HERE_BRANCH --path=apps/production --personal  
```
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

# 4 TESTING
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

```
spec:
      containers:
      - name: php
        image: jkb91/php_docker:1.0
```

```
spec:
      containers:
      - name: php
        image: jkb91/php_docker:2.0
```

AFTER ~3-5 min GO >> GO >> http://34.54.140.202 << COLOR OF THE SITE SHOUD BE DIFFERENT  


# 5 ADD SERVICE GRAFANA

```
helm install my-grafana grafana/grafana --namespace production
```

Update your DNS RECORDS ON CLOUD
RECORD A = DOMAIN
RECORD A = grafana.DOMAIN

Edit Ingress
```
kubectl edit ingress ingress-basics -n production
```

Update INGRESS
```
spec:
  rules:
  - host: projectdevops.eu
    http:
      paths:
      - backend:
          service:
            name: php-service
            port:
              number: 80
        path: /
        pathType: Prefix
  - host: grafana.projectdevops.eu
    http:
      paths:
      - backend:
          service:
            name: my-grafana
            port:
              number: 80
        path: /
        pathType: Prefi
```
