# DevSecOps CI/CD with Jenkins and ArgoCD

This repository contains the helm charts and Jenkinsfile used to automate the Continous Deployment phase of a CI/CD pipeline to EKS. Continous delivery is accomplished using ArgoCD. This repo is intended to be used with the two repositories listed below in order.
- https://github.com/yemisprojects/eks-infra (contains terraform code and github workflow to automate EKS deployment)
- https://github.com/yemisprojects/eks-app (contains application source code to be containerized)

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

The app will be deployed by ArgoCD in the `vprofile` namespace (Ref: namespace.yml). An Application load balancer is spun up using the ingress and can be accessed in this example vprofile.devopsprotech.com (Ref: vproapp-ingress.yaml). Application pods will be kept at a target utiilization of 50 and auto-scaled usin the Horizontal pod autoscaler.(Ref: hpa.yml). The init containers are used to ensure the application is only started after DB and Memcache are reaachable by way of a nslookup (Ref: vprofile-app-deployment.yml)