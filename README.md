<h1 align="center">DevSecOps CI/CD with Jenkins and ArgoCD</h1>

This repository contains the helm charts and Jenkinsfile used to automate the Continous Deployment phase of a CI/CD pipeline to EKS. Continous delivery is accomplished using ArgoCD. This repo is intended to be used with the two repositories listed below in order.
- [eks-infra](https://github.com/yemisprojects/eks-infra) repo: contains terraform code and github workflow to automate EKS deployment
- [eks-app](https://github.com/yemisprojects/eks-app) repo: contains application source code to be containerized

## Summary of Helm charts
A snippet of the templates is shown below
```sh                                                                                                                                                                        
tree webapp/templates
├── db-deployment.yml
├── db-service.yml
├── hpa.yml
├── mc-deployment.yml
├── mc-service.yml
├── namespace.yml
├── rmq-deployment.yml
├── rmq-service.yml
├── vproapp-ingress.yaml
├── vprofile-app-deployment.yml
├── vprofile-app-secret.yml
└── vprofile-app-service.yml
```

- The app will be deployed by ArgoCD in the `vprofile` namespace (Ref: [namespace.yml](https://github.com/yemisprojects/kubernetes-manifests/blob/main/webapp/templates/namespace.yml)). 
- An Application load balancer is spun up using the ingress and can be accessed in this example `vprofile.devopsprotech.com` (Ref: [vproapp-ingress.yaml](https://github.com/yemisprojects/kubernetes-manifests/blob/main/webapp/templates/vproapp-ingress.yaml)). 
- The application's pods will be kept at a target utiilization of 50 and scaled horizontally using the Horizontal pod autoscaler(Ref: [hpa.yml](https://github.com/yemisprojects/kubernetes-manifests/blob/main/webapp/templates/hpa.yml)). 
- The init containers are used to ensure the application is only started after DB and Memcache are reachable by way of a `nslookup` (Ref: [vprofile-app-deployment.yml](https://github.com/yemisprojects/kubernetes-manifests/blob/main/webapp/templates/vprofile-app-deployment.yml))

## How to use this repo

Follow [step 6](https://github.com/yemisprojects/eks-app#step-6-update-helm-chart-repository) in the eks-app repo

## Deployed application from Argo CD view
<img alt="Argo CD" src="https://github.com/yemisprojects/kubernetes-manifests/blob/main/images/argocd_view.png">

## App Login page
<img alt="Login page" src="https://github.com/yemisprojects/kubernetes-manifests/blob/main/images/app_login_page.png">

