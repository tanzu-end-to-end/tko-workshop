One of the key reason Kubernetes has grown so popular is, It provides Application Development teams an single API to drive infrastructure according to their application requirements. Application Development teams do not need to understand how the backend infrastructure runs, whether its a Public/Hybrid cloud or on-prem Datacenter. If they need a storage volume provisioned or a Load Balancer Provisioned, it's a simple Kubernetes API call away. Kubernetes drives the backend infrastructure based on the API call request and provisions/changes Compute, Storage and Network accordingly.

While this Kubernetes capability makes the day to day lot more streamlined and simplified for Application Development teams, it also gives Development teams unbound access to the Infrastructure. They can drive as many storage volumes, compute instances or Load Balancers needed etc. They can drive external traffic to applications/databases within the security perimeter of an Organization, or expose internal data. They can run rouge containers with vulnerabilities etc. As such, running Kubernetes platform with appropriate polices that implement security best practices is imperative.

Tanzu Mission Control provides out of the box policies that can be applied to Kubernetes Clusters across multiple cloud and on-prem environments. Tanzu Mission Control also provides a way to build custom polices based on an Organizations unique requirements.

## Implement Secure access policy

Kubernetes defines various Role based access Control policies to its API. Tanzu Mission Control allows a user from a Federated Identity to be mapped to various RBAC polices, providing a way to give right level of API access. We already saw this in the end of the previous Chapter. 

## Implement Pod Security Policies

Containers are processes that run on a given Kubernetes Host, they can have access to host file systems, networks, host namespaces, password files, listen to traffic on the host etc. An application running in a container can see host/system level objects. To prevent containers from doing so, Kubernetes has created Admission Controllers that check the addition of a Pod based on a set of Pod Security Policies (PSP's). 

Tanzu Mission Control by default implements Pod Security Policies around running pods with root access, Privileged mode, access to host networks , volumes etc.

To view these policies,

- Go the to the tab with Tanzu Mission Control --> Click on Policies on the left hand side, Click Assignments --> Click the `Security` tab
- Click on the root of the `Clusters` tree, on `Tanzu End to End`
- Select the `Direct Policy` Applied, `Dhubao-Strict` -->Click `edit`

Notice all the Pod Security Policies Applied by default.
THe Pod Security Policy can be overridden with the Button `Disable Native Pod Security Policies` at the bottom of the screen.

Let's validate this by trying to deploy a container that needs privileged access to run.

- On the Policies page, Security tab, expans the Cluster Group `e2e-amer`, Notice that cluster group has the PSP `Dhubao-Strict` applied with default PSP disabled.
- On the same page, click on the Cluster Group `tko-sps-strict` , notice the Direct policy on it called `psp=strict` this cluster group has the default PSP enabled.

- Deploy an app with root privileges on the cluster `e2e-amer ` that has no default PSP enabled

```execute
kubectl create deployment nginx --image=nginx -n {{session_namespace}}
```
Notice the pods do get created because default PSP is disabled on this cluster.

```
kubectl get pods -n {{session_namespace}}
```

- Deploy an app with root privileges on the cluster `gke-psp-demo` that has default PSP enabled on it in cluster group `tko-psp-demo`
- Fetch Kubeconfig for the cluster
  - Locate the cluster `gke-psp-demo` under the clusters page and click on it.
  - Click Actions from the top right hand side of the page and click `Access This CLuster`
  - Click on `view YAML`
  - Copy the YAML  config
  - Paste the value in a Config File, the below commnad will open an editor
  
```editor:append-lines-to-file
file: ~/kubeconfig-gke-psp-demo.yaml
text: |
```

Note: Once pasted, make sure to hit `CTL+S`

- Login to TMC
  - Fetch API Token for TMC, click on the dropdown of your username on the top right hand corner of Tanzu Mission Control, click " My Account" -->Click on the `API tokens` tab, copy existing Token or click on `Generate a new API Token` and copy that.
  - 
```execute
tmc login 
```
When Prompted to Provide the API Token, paste the one that oyu just copied. Give a context name like `tmc-tko` and `aws-hosted` for `Mangement_cluster` and `aws` for Provisioner name, like shown in the below snapshot.

![TMC Access Token](../images/tmc-access-api.png)

  - Deploy the same `nginx` application on this cluster 
  
```execute
kubectl --kubeconfig=kubeconfig-gke-psp-demo.yaml create deployment nginx --image=nginx -n {{session_namespace}}
```
Notice, the pods do not get created

```execute
kubectl --kubeconfig=kubeconfig-gke-psp-demo.yaml get pods  -n {{session_namespace}}
```
This is because the PSP policy is enabled on the cluster.