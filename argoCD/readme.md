# Install and confgiure ArgoCD

The first thing, as I said before, ArgoCD is written in GO, which helps us a lot in the installation process.

Here we need to divide this installation into two parts, installing ArgoCD as an operator in Kubernetes, and installing ArgoCD as a CLI, so that you can use ArgoCD in your daily life.

It also has a graphical interface, which is the ArgoCD UI, but we won't cover it for now, I want people to be very comfortable with the ArgoCD CLI, which is what we will use in our daily lives.

In the beginning we will still only use the CLI, but very soon we will use manifests to define our applications within ArgoCD.

## Installing ArgoCD as a worker on Kubernetes

Para instalar o ArgoCD como um operador no Kubernetes, antes precisamos criar uma namespace chamada argocd, e para isso basta executar o seguinte comando:

```kubectl create namespace argocd```

The output of this command will be something like this:

```namespace/argocd created```

Now let's install ArgoCD as a worker in Kubernetes:

```kubectl apply -n argocd -f https://raw.githubusercontent.com/argoproj/argo-cd/stable/manifests/install.yaml```


The output of this command will be something like this:

```namespace/argocd created
customresourcedefinition.apiextensions.k8s.io/applications.argoproj.io created
customresourcedefinition.apiextensions.k8s.io/applicationsets.argoproj.io created
customresourcedefinition.apiextensions.k8s.io/appprojects.argoproj.io created
serviceaccount/argocd-application-controller created
serviceaccount/argocd-applicationset-controller created
serviceaccount/argocd-dex-server created
serviceaccount/argocd-notifications-controller created
serviceaccount/argocd-redis created
serviceaccount/argocd-repo-server created
serviceaccount/argocd-server created
role.rbac.authorization.k8s.io/argocd-application-controller created
role.rbac.authorization.k8s.io/argocd-applicationset-controller created
role.rbac.authorization.k8s.io/argocd-dex-server created
role.rbac.authorization.k8s.io/argocd-notifications-controller created
role.rbac.authorization.k8s.io/argocd-server created
clusterrole.rbac.authorization.k8s.io/argocd-application-controller created
clusterrole.rbac.authorization.k8s.io/argocd-server created
rolebinding.rbac.authorization.k8s.io/argocd-application-controller created
rolebinding.rbac.authorization.k8s.io/argocd-applicationset-controller created
rolebinding.rbac.authorization.k8s.io/argocd-dex-server created
rolebinding.rbac.authorization.k8s.io/argocd-notifications-controller created
rolebinding.rbac.authorization.k8s.io/argocd-redis created
rolebinding.rbac.authorization.k8s.io/argocd-server created
clusterrolebinding.rbac.authorization.k8s.io/argocd-application-controller created
clusterrolebinding.rbac.authorization.k8s.io/argocd-server created
configmap/argocd-cm created
configmap/argocd-cmd-params-cm created
configmap/argocd-gpg-keys-cm created
configmap/argocd-notifications-cm created
configmap/argocd-rbac-cm created
configmap/argocd-ssh-known-hosts-cm created
configmap/argocd-tls-certs-cm created
secret/argocd-notifications-secret created
secret/argocd-secret created
service/argocd-applicationset-controller created
service/argocd-dex-server created
service/argocd-metrics created
service/argocd-notifications-controller-metrics created
service/argocd-redis created
service/argocd-repo-server created
service/argocd-server created
service/argocd-server-metrics created
deployment.apps/argocd-applicationset-controller created
deployment.apps/argocd-dex-server created
deployment.apps/argocd-notifications-controller created
deployment.apps/argocd-redis created
deployment.apps/argocd-repo-server created
deployment.apps/argocd-server created
statefulset.apps/argocd-application-controller created
networkpolicy.networking.k8s.io/argocd-application-controller-network-policy created
networkpolicy.networking.k8s.io/argocd-applicationset-controller-network-policy created
networkpolicy.networking.k8s.io/argocd-dex-server-network-policy created
networkpolicy.networking.k8s.io/argocd-notifications-controller-network-policy created
networkpolicy.networking.k8s.io/argocd-redis-network-policy created
networkpolicy.networking.k8s.io/argocd-repo-server-network-policy created
networkpolicy.networking.k8s.io/argocd-server-network-policy created 
```


As you can see, with the command above we configure ArgoCD by creating several objects in Kubernetes, such as a deployment for argocd-server, a service for argocd-server, a configmap for argocd-cm, and so it goes.

If you want to know more about the project, go to the official repository


Vamos ver se os pods do ArgoCD foram criados com sucesso:

```kubectl get pods -n argocd```

The output of this command will be something like this:

```NAME                                                READY   STATUS    RESTARTS   AGE
argocd-application-controller-0                     1/1     Running   0          115s
argocd-applicationset-controller-5f67f4c987-vdtpr   1/1     Running   0          117s
argocd-dex-server-5859d89dcc-c69fx                  1/1     Running   0          117s
argocd-notifications-controller-75c986587-7jznn     1/1     Running   0          116s
argocd-redis-74c8c9c8c6-mzdlv                       1/1     Running   0          116s
argocd-repo-server-76f77874d7-8qscp                 1/1     Running   0          116s
argocd-server-64d5654c48-tkv65                      1/1     Running   0          116s
```


Where we have the following pods:

argocd-application-controller-0 - Responsible for managing Kubernetes resources
argocd-applicationset-controller-5f67f4c987-vdtpr - Controller responsible for managing ApplicationSets
argocd-dex-server-5859d89dcc-c69fx - Responsible for managing authentication
argocd-notifications-controller-75c986587-7jznn - Responsible for managing notifications, such as when an Application is updated
argocd-redis-74c8c9c8c6-mzdlv - Responsible for storing ArgoCD data
argocd-repo-server-76f77874d7-8qscp - Responsible for managing the repositories
argocd-server-64d5654c48-tkv65 - Responsible for exposing the ArgoCD graphical interface
 

Okay, introduced. Throughout the book we will talk more about each of these components, but for now this is what you need to know.

All of our powers are in Running status, which means they are working properly.


## Installing the ArgoCD CLI

As I said, ArgoCD has a graphical interface, but it is also possible to interact with it through commands. For this, we need to install the argocd CLI.

We will focus the first part of this book on the CLI, so that you can understand how ArgoCD works under the hood, and then enjoy the graphical interface.

To install the argocd CLI on Linux, simply run the following command:

```
curl -sSL -o argocd-linux-amd64 https://github.com/argoproj/argo-cd/releases/latest/download/argocd-linux-amd64

sudo install -m 555 argocd-linux-amd64 /usr/local/bin/argocd

rm argocd-linux-amd64
```


With the above command we downloaded the argocd CLI binary, and installed it in the /usr/local/bin/argocd directory, to carry out the installation we used the Linux install command, which is a command that installs files and directories . We pass the -m 555 parameters to define the file permissions, and the name of the file we want to install.

Ready! Our argocd CLI is installed.

Let's see if it's working correctly:

```argocd version```


## Authenticating with ArgoCD

Now that we have ArgoCD installed, both the CLI and the operator, we need to authenticate with ArgoCD so that we can take the first steps.

First of all, we need to know the ArgoCD address. To do this, we will execute the following command:

```kubectl get svc -n argocd```


The output of this command will be something like this:

```
NAME                                      TYPE        CLUSTER-IP       EXTERNAL-IP   PORT(S)                      AGE
argocd-applicationset-controller          ClusterIP   10.100.164.34    <none>        7000/TCP,8080/TCP            12m
argocd-dex-server                         ClusterIP   10.100.14.112    <none>        5556/TCP,5557/TCP,5558/TCP   12m
argocd-metrics                            ClusterIP   10.100.146.115   <none>        8082/TCP                     12m
argocd-notifications-controller-metrics   ClusterIP   10.100.81.159    <none>        9001/TCP                     12m
argocd-redis                              ClusterIP   10.100.174.178   <none>        6379/TCP                     12m
argocd-repo-server                        ClusterIP   10.100.148.141   <none>        8081/TCP,8084/TCP            12m
argocd-server                             ClusterIP   10.100.25.239    <none>        80/TCP,443/TCP               12m
argocd-server-metrics                     ClusterIP   10.100.46.64     <none>        8083/TCP                     12m
```


The service we need for now from ArgoCD is argocd-server, and the full address is argocd-server.argocd.svc.cluster.local.

Let's do the port-forward to access ArgoCD without having to expose it:

```
kubectl port-forward svc/argocd-server -n argocd 8080:443
```