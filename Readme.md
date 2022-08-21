# Argo CD Setup

## General
Git Ops Principles to sync planned states in GIT with actual state in the cluster, and make sure they are equal. 
No need for changing something with CLI or Web Frontends, and make sure the defined State from GitOps is same as the depoloyed Applikations.

**Overview:**
![Architecture](https://argo-cd.readthedocs.io/en/stable/assets/argocd_architecture.png)

## Source
https://argo-cd.readthedocs.io/en/stable/

## Installation

### Installation Guides

    https://argo-cd.readthedocs.io/en/stable/getting_started/

### Option 1) Local Baseline Install with Ingress

Install Base Manifests from Argo CD GIT Repo with sample Local Ingress Controller on localhos without SSL, all in Folder base,

    kubectl apply -k base

### Option 2) Install wit Ingress, SSL and Specific Domain Name

Sample Ingress in **overlays/mgmt-cluster** with SSL from Cert Manager and Lets Encrypt Certificate.
Use this as Baseline for your own Domain Name or copy it in a new folder and use this Folder to deploy Argo CD. 
Add your own overlay to change the the Ingress and or change Baseline Settings from latest stable Version 

    kubectl apply -k overlays/<YOUR-FOLDER>


### Install CLI Tools

Use Homebrew on Macs or Linux:

    brew install argocd

or download the latest Argo CD version from https://github.com/argoproj/argo-cd/releases/latest. 
More detailed installation instructions can be found via the CLI installation documentation.


### Access The Server

If you have installed and configured the Ingress controller with Baseline config, 
just call https://argocd.127.0.0.1.nip.io if you have added your own overlay, use your Domain name.

If your Ingress doesn't work as expected, use Port Forward to test the service, for example

    kubectl port-forward svc/argocd-server -n argocd 8080:443
    open https://localhost:8080

Now you are able to call the ArgoCD API server via ARGO-CLI Tool and with your Web Browser https://localhost:8080 


### Login Using the CLI

Get the initial Password for the admin account

    kubectl -n argocd get secret argocd-initial-admin-secret -o jsonpath="{.data.password}" | base64 -d; echo

Use the CLI to login to your Argo CD Server. If you added a Portforward, for example localhost:8080

    argocd login <ARGO-CD-SERVER>

Use the **username** admin and the **password** from the previous step.


### Change the Password

    argocd account update-password


### (Optional) Register cluster to deploy Apps to

If you want to manage Deployments on other clusters, than the cluster you installed Argo CD

List all your cluster contexts

    kubectl config get-contexts -o name

Add another context from the list with the contextname. First find the name of the cluster you want to manage with the previous command,
and use this with the following command

    argocd cluster add <CLUSTER-NAME>

The above command installs a ServiceAccount (argocd-manager), into the kube-system namespace of that kubectl context, and binds the service account to an admin-level ClusterRole. Argo CD uses this service account token to perform its management tasks (i.e. deploy/monitoring).


## Summary

Now you should have a vanilla Argo CD running in your Kubernetes Cluster.
