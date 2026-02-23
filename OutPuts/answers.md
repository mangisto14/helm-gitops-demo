
# 🧱 PART 1 — Environment Setup

## Step 1 — Start Kubernetes (Minikube)
>minikube start --cpus=4 --memory=8192
```
C:\Users\Media>minikube start --cpus=4 --memory=8192
* minikube v1.37.0 on Microsoft Windows 11 Pro 10.0.26200.7705 Build 26200.7705
* Using the docker driver based on existing profile

X Exiting due to MK_USAGE: Docker Desktop has only 7591MB memory but you specified 8192MB


C:\Users\Media>minikube start --cpus=4 --memory=6144
* minikube v1.37.0 on Microsoft Windows 11 Pro 10.0.26200.7705 Build 26200.7705
* Using the docker driver based on existing profile
! You cannot change the memory size for an existing minikube cluster. Please first delete the cluster.
! You cannot change the CPUs for an existing minikube cluster. Please first delete the cluster.
* Starting "minikube" primary control-plane node in "minikube" cluster
* Pulling base image v0.0.48 ...
* Restarting existing docker container for "minikube" ...
! Failing to connect to https://registry.k8s.io/ from inside the minikube container
* To pull new external images, you may need to configure a proxy: https://minikube.sigs.k8s.io/docs/reference/networking/proxy/
* Preparing Kubernetes v1.34.0 on Docker 28.4.0 ...
* Verifying Kubernetes components...
  - Using image gcr.io/k8s-minikube/storage-provisioner:v5
* After the addon is enabled, please run "minikube tunnel" and your ingress resources would be available at "127.0.0.1"
  - Using image registry.k8s.io/ingress-nginx/kube-webhook-certgen:v1.6.2
  - Using image registry.k8s.io/ingress-nginx/kube-webhook-certgen:v1.6.2
  - Using image registry.k8s.io/ingress-nginx/controller:v1.13.2
* Verifying ingress addon...
* Enabled addons: storage-provisioner, default-storageclass, ingress
* Done! kubectl is now configured to use "minikube"
```
* Enable ingress:
>minikube addons enable ingress
```
C:\Users\Media>minikube addons enable ingress
* ingress is an addon maintained by Kubernetes. For any concerns contact minikube on GitHub.
You can view the list of minikube maintainers at: https://github.com/kubernetes/minikube/blob/master/OWNERS
* After the addon is enabled, please run "minikube tunnel" and your ingress resources would be available at "127.0.0.1"
  - Using image registry.k8s.io/ingress-nginx/controller:v1.13.2
  - Using image registry.k8s.io/ingress-nginx/kube-webhook-certgen:v1.6.2
  - Using image registry.k8s.io/ingress-nginx/kube-webhook-certgen:v1.6.2
* Verifying ingress addon...
* The 'ingress' addon is enabled
```

* Verify:
> kubectl get nodes
```
C:\Users\Media>kubectl get nodes
NAME       STATUS   ROLES           AGE   VERSION
minikube   Ready    control-plane   28d   v1.34.0
```
# 🚀 PART 2 — Install ArgoCD

## Step 2 — Create Namespace
>kubectl create namespace argocd
```
PS C:\Users\repo\DevOps\devops-course\class8 - ArgoCD\Home Assignment\helm-gitops-demo> kubectl create namespace argocd
namespace/argocd created
```

## Step 3 — Install ArgoCD
>kubectl apply -n argocd -f https://raw.githubusercontent.com/argoproj/argo-cd/stable/manifests/install.yaml
```
PS C:\Users\repo\DevOps\devops-course\class8 - ArgoCD\Home Assignment\helm-gitops-demo> kubectl replace -n argocd -f https://raw.githubusercontent.com/argoproj/argo-cd/stable/manifests/install.yaml 
customresourcedefinition.apiextensions.k8s.io/applications.argoproj.io replaced
customresourcedefinition.apiextensions.k8s.io/applicationsets.argoproj.io replaced
customresourcedefinition.apiextensions.k8s.io/appprojects.argoproj.io replaced
...
```
* Wait until pods are ready:
>kubectl get pods -n argocd
```
PS C:\Users\repo\DevOps\devops-course\class8 - ArgoCD\Home Assignment\helm-gitops-demo> kubectl get pods -n argocd
NAME                                                READY   STATUS    RESTARTS   AGE
argocd-application-controller-0                     1/1     Running   0          2m40s
argocd-applicationset-controller-6d87796c6c-tl828   1/1     Running   0          2m41s
argocd-dex-server-5544864d7-vn4dg                   1/1     Running   0          2m41s
argocd-notifications-controller-5dc9c8c9dd-zlfxl    1/1     Running   0          2m41s
argocd-redis-86d798767-xf7x5                        1/1     Running   0          2m41s
argocd-repo-server-7bbc85547f-q747v                 1/1     Running   0          2m41s
argocd-server-777ffdbd95-86fwr                      1/1     Running   0          2m41s
```


# 🌐 PART 3 — Access ArgoCD UI
* Option A — Port Forward (Simple)
> kubectl port-forward svc/argocd-server -n argocd 8080:443
```
C:\Users\repo\DevOps\devops-course>kubectl port-forward svc/argocd-server -n argocd 8080:443
Forwarding from 127.0.0.1:8080 -> 8080
Forwarding from [::1]:8080 -> 8080
```
## Step 4 — Login
* Get password:
> kubectl -n argocd get secret argocd-initial-admin-secret \
-o jsonpath="{.data.password}" | base64 -d
```
Media@DESKTOP-TNMEV22 MINGW64 /c/Users/repo/DevOps/devops-course (main)
$ kubectl -n argocd get secret argocd-initial-admin-secret \
-o jsonpath="{.data.password}" | base64 -d
QPIEFWrqn3ZME3fM
```

# 📁 PART 4 — Prepare GitHub Helm Repository
## Step 5 — Create Helm Chart
* Initialize:
helm create myapp
>
>
> Push to GitHub:
```
PS C:\Users\repo\DevOps\devops-course\class8 - ArgoCD\Home Assignment\helm-gitops-demo> git add .
PS C:\Users\repo\DevOps\devops-course\class8 - ArgoCD\Home Assignment\helm-gitops-demo> git commit -m "Add values-dev.yaml and ensure it is not empty"
[master (root-commit) 543c97f] Add values-dev.yaml and ensure it is not empty
 16 files changed, 594 insertions(+)
 create mode 100644 argocd/dev-app.yaml
 create mode 100644 argocd/prod-app.yaml
 create mode 100644 charts/myapp/.helmignore
 create mode 100644 charts/myapp/Chart.yaml
 create mode 100644 charts/myapp/templates/NOTES.txt
 create mode 100644 charts/myapp/templates/_helpers.tpl
 create mode 100644 charts/myapp/templates/deployment.yaml
 create mode 100644 charts/myapp/templates/hpa.yaml
 create mode 100644 charts/myapp/templates/httproute.yaml
 create mode 100644 charts/myapp/templates/ingress.yaml
 create mode 100644 charts/myapp/templates/service.yaml
 create mode 100644 charts/myapp/templates/serviceaccount.yaml
 create mode 100644 charts/myapp/templates/tests/test-connection.yaml
 create mode 100644 charts/myapp/values-dev.yaml
 create mode 100644 charts/myapp/values-prod.yaml
 create mode 100644 charts/myapp/values.yaml
PS C:\Users\repo\DevOps\devops-course\class8 - ArgoCD\Home Assignment\helm-gitops-demo> git push origin main
error: src refspec main does not match any
error: failed to push some refs to 'origin'
PS C:\Users\repo\DevOps\devops-course\class8 - ArgoCD\Home Assignment\helm-gitops-demo> git branch
* master
PS C:\Users\repo\DevOps\devops-course\class8 - ArgoCD\Home Assignment\helm-gitops-demo> git branch -m master main
PS C:\Users\repo\DevOps\devops-course\class8 - ArgoCD\Home Assignment\helm-gitops-demo> git add .
PS C:\Users\repo\DevOps\devops-course\class8 - ArgoCD\Home Assignment\helm-gitops-demo> git commit -m "Fix: adding values-dev.yaml and charts"
On branch main
nothing to commit, working tree clean
PS C:\Users\repo\DevOps\devops-course\class8 - ArgoCD\Home Assignment\helm-gitops-demo> git push -u origin main
fatal: 'origin' does not appear to be a git repository
fatal: Could not read from remote repository.

Please make sure you have the correct access rights
and the repository exists.
PS C:\Users\repo\DevOps\devops-course\class8 - ArgoCD\Home Assignment\helm-gitops-demo> git remote -v
PS C:\Users\repo\DevOps\devops-course\class8 - ArgoCD\Home Assignment\helm-gitops-demo> git remote add origin https://github.com/mangisto14/helm-gitops-demo.git
PS C:\Users\repo\DevOps\devops-course\class8 - ArgoCD\Home Assignment\helm-gitops-demo> git push -u origin main
To https://github.com/mangisto14/helm-gitops-demo.git
 ! [rejected]        main -> main (fetch first)
error: failed to push some refs to 'https://github.com/mangisto14/helm-gitops-demo.git'
hint: Updates were rejected because the remote contains work that you do not
hint: have locally. This is usually caused by another repository pushing to
hint: the same ref. If you want to integrate the remote changes, use
hint: 'git pull' before pushing again.
hint: See the 'Note about fast-forwards' in 'git push --help' for details.
PS C:\Users\repo\DevOps\devops-course\class8 - ArgoCD\Home Assignment\helm-gitops-demo> git pull origin main --allow-unrelated-histories
remote: Enumerating objects: 122, done.
remote: Counting objects: 100% (122/122), done.
remote: Compressing objects: 100% (75/75), done.
remote: Total 122 (delta 27), reused 117 (delta 27), pack-reused 0 (from 0)
Receiving objects: 100% (122/122), 102.54 KiB | 1.77 MiB/s, done.
Resolving deltas: 100% (27/27), done.
From https://github.com/mangisto14/helm-gitops-demo
 * branch            main       -> FETCH_HEAD
 * [new branch]      main       -> origin/main
Auto-merging charts/myapp/Chart.yaml
CONFLICT (add/add): Merge conflict in charts/myapp/Chart.yaml
Automatic merge failed; fix conflicts and then commit the result.
PS C:\Users\repo\DevOps\devops-course\class8 - ArgoCD\Home Assignment\helm-gitops-demo> git add charts/myapp/Chart.yaml
PS C:\Users\repo\DevOps\devops-course\class8 - ArgoCD\Home Assignment\helm-gitops-demo> git commit -m "Fix merge conflict in Chart.yaml"
[main 5bdfd7a] Fix merge conflict in Chart.yaml
PS C:\Users\repo\DevOps\devops-course\class8 - ArgoCD\Home Assignment\helm-gitops-demo> git push origin main
Enumerating objects: 32, done.
Counting objects: 100% (32/32), done.
Delta compression using up to 12 threads
Compressing objects: 100% (24/24), done.
Writing objects: 100% (28/28), 8.17 KiB | 2.04 MiB/s, done.
Total 28 (delta 4), reused 1 (delta 0), pack-reused 0 (from 0)
remote: Resolving deltas: 100% (4/4), completed with 1 local object.
To https://github.com/mangisto14/helm-gitops-demo.git
   78b3672..5bdfd7a  main -> main
```
> 
* Create dev branch:
> 
>git checkout -b dev
```
PS C:\Users\repo\DevOps\devops-course\class8 - ArgoCD\Home Assignment\helm-gitops-demo> git checkout -b dev
Switched to a new branch 'dev'

```
>git push origin dev
```
PS C:\Users\repo\DevOps\devops-course\class8 - ArgoCD\Home Assignment\helm-gitops-demo> git push origin dev
Total 0 (delta 0), reused 0 (delta 0), pack-reused 0 (from 0)
To https://github.com/mangisto14/helm-gitops-demo.git
   78b3672..e3fd66a  dev -> dev
```

# 🚀 PART 6 — Deploy Dev Application

## Step 6 — Create dev Application
>Apply:
kubectl apply -f dev-app.yaml
>
```
PS C:\Users\repo\DevOps\devops-course\class8 - ArgoCD\Home Assignment\helm-gitops-demo> kubectl apply -f argocd\dev-app.yaml
application.argoproj.io/myapp-dev unchanged
```
> Verify:
> kubectl get applications -n argocd
>
>  kubectl get pods -n dev

```
PS C:\Users\repo\DevOps\devops-course\class8 - ArgoCD\Home Assignment\helm-gitops-demo> kubectl get applications -n argocd
NAME         SYNC STATUS   HEALTH STATUS
myapp-dev    Unknown       Healthy
myapp-prod   Synced        Healthy
PS C:\Users\repo\DevOps\devops-course\class8 - ArgoCD\Home Assignment\helm-gitops-demo> kubectl get pods -n dev
NAME                         READY   STATUS    RESTARTS   AGE
myapp-dev-7df58bdf64-2qztw   1/1     Running   0          20s
```

# 🚀 PART 7 — Deploy Prod Application
> Apply:
kubectl apply -f prod-app.yaml
>
```
PS C:\Users\repo\DevOps\devops-course\class8 - ArgoCD\Home Assignment\helm-gitops-demo> kubectl apply -f argocd\prod-app.yaml
application.argoproj.io/myapp-prod unchanged
```
> Verify:
kubectl get pods -n prod
>
```
PS C:\Users\repo\DevOps\devops-course\class8 - ArgoCD\Home Assignment\helm-gitops-demo> kubectl get pods -n prod
NAME                          READY   STATUS    RESTARTS   AGE
myapp-prod-6ff4fcd694-86snb   1/1     Running   0          18m
myapp-prod-6ff4fcd694-rcggz   1/1     Running   0          18m
myapp-prod-6ff4fcd694-sktnr   1/1     Running   0          18m
```



# 🔁 PART 8 — GitOps Reconciliation Demo
Change replica count in values-dev.yaml:
replicaCount: 2

Commit & push:
git add .
git commit -m "scale dev to 2 replicas"
git push origin dev

Observe:
kubectl get pods -n dev

ArgoCD automatically updates cluster.

# 🗑️ Delete Deployment
* Delete app:
>kubectl delete application myapp-dev -n argocd
```
PS C:\Users\repo\DevOps\devops-course\class8 - ArgoCD\Home Assignment\helm-gitops-demo> kubectl delete application myapp-dev -n argocd
application.argoproj.io "myapp-dev" deleted from argocd namespace
```
* Delete namespace:
>kubectl delete namespace dev
```
PS C:\Users\repo\DevOps\devops-course\class8 - ArgoCD\Home Assignment\helm-gitops-demo> kubectl delete namespace dev
namespace "dev" deleted
```

* Delete ArgoCD:
>kubectl delete namespace argocd
```
PS C:\Users\repo\DevOps\devops-course\class8 - ArgoCD\Home Assignment\helm-gitops-demo> kubectl delete namespace argocd
namespace "argocd" deleted
```


