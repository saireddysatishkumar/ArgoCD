# ArgoCD
- Install ArgoCD into k8s cluster
``````
# From ArgoCD repo
cd terraform && terraform init && terraform apply
kubectl get secrets -n argocd
kubectl get secrets -n argocd argocd-initial-admin-secret -o yaml
kubectl get secrets/argocd-initial-admin-secret -n argocd  -o go-template='
{{range $k,$v := .data}}{{printf "%s: " $k}}{{if not $v}}{{$v}}{{else}}{{$v | base64decode}}{{end}}{{"\n"}}{{end}}'
``````
- Install argocd CLI in local system        
``````
brew install argocd
``````

- Login to ArgoCD  
``````
kubectl get svc -n argocd
kubectl port-forward svc/argocd-server 8010:8080 -n argocd
# Open URL http://localhost:8010/
``````

- Login argocd from CLI  
``````
argocd login 127.0.0.1:8010 
    WARNING: server is not configured with TLS. Proceed (y/n)? y
    Username: admin
    Password: 
    'admin:login' logged in successfully
    Context '127.0.0.1:8010' updated

``````


## General Project : gestbook
argocd app create helm-guestbook --repo https://github.com/argoproj/argocd-example-apps.git --path helm-guestbook --dest-server https://kubernetes.default.svc --dest-namespace default
argocd app sync helm-guestbook


## 001-Project argocd Manifests
argocd app create todo --repo https://github.com/saireddysatishkumar/ArgoCD.git --path deploy-manifests --dest-server https://kubernetes.default.svc --dest-namespace todo-app
argocd app sync todo

## 002-Project 




