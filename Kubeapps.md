## Kubeapps Installation using Helm
This is a step by step for kubeapps installation on k8s using helm chart.

### Pre-requisites for working environment
Your machine must have the following installed and in working condition:
1. `docker`.
2. `kubeadm`.
3. `helm`.
4. Host machine must be able to provide at least: 18vCPUs and 18GiB RAM.  

### Key knowledge you need to explore
1. `Kubectl Commands`
2. `Helm Commands`
3. `Kubernetes Secrets`
4. `Kubernetes Sngress`

### Important notes
1. You need a workable kubernetes cluster installed on your machines
2. You need to workable helm chart installed on the same master node of your kubernetes cluster.
3. If you want Kubeapps to be available over internet. You need a domain name pointed to your kubernetes cluster. You also need a SSL certificate and set the certificate in the ingress resource.

### Step 1: Basic steps to install kubeapps
1. Add Bitnami Helm Repository to you kubernetes cluster
```
[root@localhost ~]# helm repo add bitnami https://charts.bitnami.com/bitnami
[root@localhost ~]# helm search repo bitnami/kubeapps
```
2. Download Kubeapps `chart.values` from Bitnami repository.
```
[root@localhost ~]# helm show values bitnami/kubeapps > chart.values 
```
3. Edit `chart.values` to meet you preference

> For smooth installation, remove all repository except `bitnami`. Kubeapps will scans all repositories that were already installed in your kubernetes cluster. This process might take load on your cluster depending on your kubernetes cluster settings.

4. Install kubeapps helm chart

```
[root@localhost ~]#  helm install kubeapps --namespace kubeapps --create-namespace -f chart.values bitnami/kubeapps
```

> If you already remove all the repositories except `bitnami` as suggested in step 3. You might want to check running `conjobs`, `jobs` and `pods` named `apprepo-`\<your-repositories-name\>. 
- Delete `cronjobs` except `bitnami` repository cronjob.
- Delete failed `jobs` & `pods` not related to `bitnami` repository jobs & pods.

```
[root@localhost ~]#  kubectl get cronjobs,jobs,pods -n kubeapps 
```
- Make you all pods, cronjobs, jobs were `Running` or `Completed`. 
- Delete any `Failed` pods / jobs.

5. You can access your kubeapps locally depend on how you setup you `service` & `ingress` in `chart.values`

### Step 2: Create a demo credential with which to access Kubeapps and Kubernetes
1. Create `serviceaccount` & `clusterrolebinding`
```
[root@localhost ~]# kubectl create --namespace default serviceaccount kubeapps-operator
[root@localhost ~]# kubectl create clusterrolebinding kubeapps-operator --clusterrole=cluster-admin --serviceaccount=default:kubeapps-operator

```

2. Get access token

On Linux/macOS:

```
[root@localhost ~]# kubectl get --namespace default secret $(kubectl get --namespace default serviceaccount kubeapps-operator -o jsonpath='{range .secrets[*]}{.name}{"\n"}{end}' | grep kubeapps-operator-token) -o jsonpath='{.data.token}' -o go-template='{{.data.token | base64decode}}' && echo

```
On Windows (using powershell):
```
[Text.Encoding]::UTF8.GetString([System.Convert]::FromBase64String($(kubectl get --namespace default secret $(kubectl get --namespace default serviceaccount kubeapps-operator -o jsonpath='{.secrets[].name}') -o jsonpath='{.data.token}')))
```

### References
1. [Installing Helm](https://helm.sh/docs/intro/install/)
2. [Get Started with Kubeapps](https://github.com/vmware-tanzu/kubeapps/blob/main/docs/tutorials/getting-started.md)
3. [The Bitnami Library for Kubernetes](https://github.com/bitnami/charts)