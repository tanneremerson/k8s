# Kubernetes Play

To begin, I installed docker desktop and minikube. This gave me a simple fast
way to spin up a new cluster. Some useful commands that I used were:

```sh
# Creates a fresh cluster
minikube start

# Deletes the existing cluster
minikube delete
```

Now that I have a fresh cluster, I am going to install argocd. The Goal here is
to run some `kubectl` commands to bootstrap the cluster, then allow argo to do
the rest.


```
# Create the argocd namespace
kubectl apply -f base/argocd/argocd-namespace.yaml

# Copied the manifest loaclly
kubectl apply -n argocd -f base/argocd/argo-manifest.yaml

# The argocd-server should be type ClusterIp
kubectl get service -n argocd

# How do we update the manifest so that we don't need this step
# Convert the argocd-server to type loadbalancer
kubectl patch svc argocd-server -n argocd -p '{"spec": {"type": "LoadBalancer"}}'

# The initial password for argocd can be retrieved using
# The default username is admin
kubectl -n argocd get secret argocd-initial-admin-secret -o jsonpath="{.data.password}" | base64 -d; echo
```

