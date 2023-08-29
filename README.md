# ArgoCD
- Install ArgoCD
``````
# From ArgoCD repo
cd terraform && terraform init && terraform apply
kubectl get secrets -n argocd
kubectl get secrets -n argocd argocd-initial-admin-secret -o yaml
kubectl get secrets/argocd-initial-admin-secret -n argocd  -o go-template='
{{range $k,$v := .data}}{{printf "%s: " $k}}{{if not $v}}{{$v}}{{else}}{{$v | base64decode}}{{end}}{{"\n"}}{{end}}'
#Login to ArgoCD
kubectl get svc -n argocd
kubectl port-forward svc/argocd-server 8010:8080 -n argocd
# Open URL http://localhost:8010/

``````

## 001-Project argocd Manifests
