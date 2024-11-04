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
│   |    └──   configmap.yaml
│   ├── production/
│   │   └── kustomization.yaml      <<<< resources :- namespace: production(OVERWRITES)
```

# 3 BOOTSTRAP

```
flux bootstrap github --owner=jkb91jkb91 --repository=PASTE_HERE_REPO_NAME --branch=PASTE_HERE_BRANCH --path=apps/production --personal
flux bootstrap github --owner=jkb91jkb91 --repository=flux_gcp --branch=master --path=apps/production --personal
```
