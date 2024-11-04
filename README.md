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
