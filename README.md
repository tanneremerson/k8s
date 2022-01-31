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


```sh
# Create the argocd namespace
kubectl apply -f base/argocd/argocd-namespace.yaml

# Copied the manifest loaclly
kubectl apply -n argocd -f base/argocd/argo-manifest.yaml

# The argocd-server should be type ClusterIp
kubectl get service -n argocd

# Because we don't have ingress I had to use port forwarding
kubectl port-forward svc/argocd-server -n argocd 8080:443


# The initial password for argocd can be retrieved using
# The default username is admin
kubectl -n argocd get secret argocd-initial-admin-secret -o jsonpath="{.data.password}" | base64 -d; echo
```

Now that we have argocd in place, we can create an app using the argocd cli (I
installed this with brew). We are going to use this opportunity to install
nginx.

```sh
argcod app create {APP} --repo https://github.com/tanneremerson/k8s --path base/{APP} --dest-server https://kubernetes.default.svc

# How do we update the manifest so that we don't need this step
# Convert the argocd-server to type loadbalancer
# This doesn't do anything at this point because we don't have an ingress controller.
kubectl patch svc argocd-server -n argocd -p '{"spec": {"type": "LoadBalancer"}}'
```

