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

- On the Policies page, Security tab, expand the Cluster Group `e2e-amer`, Notice that cluster group has the PSP `Dhubao-Strict` applied with default PSP disabled.
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

## Image Download Policy

The best part about containers is they are ultra-portable. They can be layered like a cake to build brand new images. A Container image from the internet can be taken and used to add a new binary to build something custom. That's the appeal of containers. However, this means Application Development teams can download images from anywhere on the Internet and build new images inheriting vulnerabilities.

There are multiple ways to implement policies that make sure container images that get deployed are safe. 

- Implement Vulnerability Scanning in the container Registry that stores the container images, and prevents images with critical vulnerabilities from being deployed (Harbor)
- Implement Policies to not allow images from certain Image Registries (TMC)
- Policy that prevents container images with no digest from deploying (TMC)
- Stop container images with latest tag from deploying. (TMC)
- Blacklist certain images/repo's (TMC)

Tanzu Mission Control, part of the the Tanzu for Kubernetes Operations solution provides out of the box policies that can be applied to a fleet of clusters spread across Multiple Clouds.

Tanzu Mission control has Image based policies that can be applied to namespaces within a cluster. These policies can be applied fleet-wide across clusters and clouds by grouping namespaces together in a logical group called `workspaces`

- Go to the tab with Tanzu Mission Control --> Click on `Policies`  from the left hand menu --> click on `Assignments`
- Click on the `Image Registry` tab --> Click on `Workspaces`
- Select the workspace `tko-demo`
- You will notice a Direct Image Registry Policy applied called `no-busybox`
- Expand the policy `no-busybox` and click `EDIT` --> Click the first `Rule`

You will notice this is a custom policy that blocks any container image that has the name `busybox` on it. Like below
![TMC Image Policy](../images/tmc-image-policy.png)

- Add a new rule and take a look at the other Image based policies that can be applied.

Let's try to deploy `BusyBox` image on the namespace `tko-image-policy ` in the cluster `gke-psp-demo`

- Make sure the namespace exists on the cluster
```execute
kubectl --kubeconfig=kubeconfig-gke-psp-demo.yaml get ns
```

- Create a deployment with the image`busybox` from Docker Hub

```execute
kubectl --kubeconfig=kubeconfig-gke-psp-demo.yml create deployment busybox --image=busybox -n tko-image-policy
```

Notice the deployment is stuck and wont progress because of the image rules
```execute
kubectl --kubeconfig=kubeconfig-gke-psp-demo.yml describe deployment busybox -n tko-image-policy
```
Notice the deployment isn't creating any replicas.
- `{Cleanup}` Delete the deplyment
```execute
kubectl --kubeconfig=kubeconfig-gke-psp-demo.yml delete deployment busybox -n tko-image-policy
```
## Quota Policies

Application Development teams love Kubernetes cause they can request infrastructure resources like compute, network and storage for running their apps without having to deal with Operations team or raise a Ticket to provision things. However, on the flip side, this means the teams managing the platform need to be aware of the capacity they have and implement any quota/restrictions on consumption. Tanzu Mission Controls Quota based policy allows you to do just that from an operations perspective.

- Go to the tab with Tanzu Mission Control --> Click on `Policies` --> `Assignments`
- Click on the tab `Quota`, select the `Cluster Group` : `tko-psp-demo`
- Notice the Direct Quota Policy applied `quota-large` -->expand it and click `EDIT`
- Notice it has been assigned an quota to limit of 2 vCPU and 2 GB of memory per workload.
- You can opt to create a `custom` policy, if you don't want to use any of the pre-defined ones.

This is how Tanzu Mission Control will help set individual workload limits/Quota.

## Implement mTLS for Application Services across Multiple CLouds

If you have developed an application from the ground up to be cloud native and easily portable across clouds, you often have a lot of flexibility when it comes to where you deploy.  But from what we've seen, that isn't most applications.  You have issues of data gravity that keep certain services more tied to a specific deployment location.

You also want to be able to leverage the best of breed services that every cloud has to offer no matter which cloud it is on.

But getting the services on one cloud to work with services deployed on a different cloud can be challenging.  Every cloud has its own methods of configuring networking and the complexity can be daunting.  It can be difficult manage the protection of your data in flight all the way from the source application to the destination and back.  And you don't have time to go back and redesign your applications to do all this.

Tanzu makes the process of connecting applications running across clouds simple for operations and applications teams by grouping all those services into a global namespace that spans multiple clusters.  By leveraging open source projects like Istio, Tanzu enables applications to discover remote services through DNS.  It routes network traffic to remote clusters, and manages mTLS encrypted links between clouds that ensure your data is always protected.

* Click on the tab for Tanzu Service Mesh
* Show the Global Namespace called `e2e-demo`.  Point out this application is running on one cluster running TKG in AWS (e2e-tsm1) and one cluster that is running on EKS (e2e-eks).  Show that we have an ingress gateway that is the main ingress for the application (istio-ingressgateway).  Highlight that the catalog service is running in the e2e-eks cluster, and that we can see an mTLS protected, cross-cluster link between the shopping service and the catalog service.  No special rules had to be created to enable this, and this link is being completely managed by TSM.
* Hover over the catalog service name in the graph and show the performance stats.  The value of exposing these KPIs right in the Service Mesh interface is to speed troubleshooting.
* Next, let's look at how global namespaces are set up.  Click on the "Edit" button for the `e2e-demo` global namespace.  On the first page of the dialog, highlight that the "Domain" field is an arbitrary domain that you will use to refer to all the services in that namespace.  It doesn't need to be a registered domain, as this name is only used by services in the namespace to reach each other.  That domain is simply a signal to the mesh that it needs to look in the global namespace to resolve that domain name.  Click "Next" to go to the "Service Mapping" dialog.
* In the "Service Mapping" dialog, highlight the mapping rules that onboard services into the new global namespace.  Point out we've selected a cluster and namespace combination for both clusters.  Open up the "Service Preview" section on each mapping rule to show the services that are mapped in.  Click next to skip to the "Security" section to highlight we're enforcing 'mTLS' encryption between services and block non-encrypted traffic.  Highlight that policy is managed by the mesh itself, and the applications don't have to make any changes to enable that functionality.
* Click on the "Cancel" button to exit the dialog.

And there you have it!  Tanzu helps you address all those challenges we discussed in the beginning around developing, delivering and operating resilient applications that meet the strictest security standards and follow best practices for cloud native applications.

* Refresh the Pet Clinic app.

Thanks for watching!