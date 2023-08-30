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

## 002-Project blue Green Argo deployment
The blue green strategy is not supported by built-in Kubernetes Deployment but available via third-party Kubernetes controller.  
This example demonstrates how to implement blue-green deployment via Argo Rollouts:

- a. Install Argo Rollouts controller: https://github.com/argoproj/argo-rollouts#installation  
``````
kubectl create namespace argo-rollouts
``````
kubectl apply -n argo-rollouts -f https://github.com/argoproj/argo-rollouts/releases/latest/download/install.yaml   

- b. Create a sample application and sync it.  
``````
argocd app create --name blue-green --repo https://github.com/argoproj/argocd-example-apps --dest-server https://kubernetes.default.svc --dest-namespace default --path blue-green && argocd app sync blue-green
``````
Once the application is synced you can access it using blue-green-helm-guestbook service.  

- c. Change image version parameter to trigger blue-green deployment process:
``````
argocd app set blue-green -p image.tag=0.2 && argocd app sync blue-green
``````
Now application runs ks-guestbook-demo:0.1 and ks-guestbook-demo:0.2 images simultaneously. The ks-guestbook-demo:0.2 is still considered blue available only via preview service blue-green-helm-guestbook-preview.  

- d. Promote ks-guestbook-demo:0.2 to green by patching Rollout resource:
``````
argocd app patch-resource blue-green --kind Rollout --resource-name blue-green-helm-guestbook --patch '{ "status": { "verifyingPreview": false } }' --patch-type 'application/merge-patch+json'
``````
This promotes ks-guestbook-demo:0.2 to green status and Rollout deletes old replica which runs ks-guestbook-demo:0.1.




