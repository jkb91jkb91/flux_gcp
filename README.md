# INFO

1.) Install Flux  
2.) Architecture  and initial INGRESS
3.) Bootstrap   
4.) Testing   
------------------------------------- ADDING HELM CHARTS ---------------------------------  
5.) Add Service Grafana   
6.) Add Prometheus  
--------------------------------------------ADDING SSL -----------------------------------  
7.) ADD SSL HTTPS Managed Certificate   
X.) Debuging    




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

Initial Ingress  
```
apiVersion: networking.k8s.io/v1
kind: Ingress
metadata:
  name: ingress-basics
  namespace: production
  annotations:
    kubernetes.io/ingress.class: "gce"
spec:
  rules:
  - http:
      paths:
      - path: /
        pathType: Prefix
        backend:
          service:
            name: php-service
            port:
              number: 80
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
kubectl edit deploy deployment-php -n production  

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
        pathType: Prefix
```

# 6 ADD SERVICE PROMETHEUS
```
helm repo add prometheus-community https://prometheus-community.github.io/helm-charts
helm repo update
helm install prometheus prometheus-community/prometheus --namespace production
```

```
 - host: prometheus.projectdevops.eu
    http:
      paths:
      - backend:
          service:
            name: prometheus-server
            port:
              number: 80
        path: /
        pathType: Prefix
```

# 7 ADD SSL HTTPS Managed Certificate

```
apiVersion: networking.gke.io/v1
kind: ManagedCertificate
metadata:
  name: managed-cert-for-ingress
spec:
  domains:
    - projectdevops.eu
    - grafana.projectdevops.eu
    - prometheus.projectdevops.eu

```

```

# kubectl get managedcertificates -n production
NAME                       AGE   STATUS
managed-cert-for-ingress   15m   Provisioning >>> Active

```
Testing https
GO TO >> https://prometheus.projectdevops.eu/  
GOT TO >> https://grafana.projectdevops.eu/

# X Debuging
```

1 >>> HOW TO TRIGGER RECONCILATION MANUALLY
flux get sources git  # IT GIVES NAME

flux reconcile source git PASTE_NAME --namespace flux-system
flux reconcile source git flux-system --namespace flux-system # WYMUSZENIE SYNCHRO


kubectl get gitrepositories -n flux-system

2 >>> HOW TO CHECK HELM REPOSITORY APPLICATION
helm ls -n NAMESPACE
kubectl get helmreleases -A

IF HELM RELEASE NOT APPLIED >>> CHECK LOGS
kubectl logs -n flux-system deployment/source-controller
kubectl logs -n flux-system deployment/kustomize-controller

```
