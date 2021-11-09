Building Kubernetes Clusters on various Cloud/Infrastructure environments can be a non-trivial task, from an operations perspective there are many things to consider.

1. What Base Image should be used to build clusters nodes? and what packages should be in them to make the nodes secure by default?
2. Since Kubernetes needs different Drivers for various Cloud Providers to enable Storage and Networking consumption,each cluster on a cloud provider needs different packages to be installed, how does one automate all of this? 
3. Once these clusters are created, how do you provide Development teams authorized access to the Kubernetes API? The Kubernetes API is pretty powerful and can make infrastructure changes.
4. Since Kubernetes lets development teams provision storage and networking objects, How do you ensure consistent capacity management?

Tanzu for Kubernetes Operations provides Automated solution to make provisioning Kubernetes Clusters simple, secure and efficient.

## Provision Kubernetes Clusters via Tanzu Kubernetes Grid

![Tanzu Kubernetes Build](../images/day1-build.png)

**Tanzu Kubernetes Grid** will help automate the Provisioning of Clusters on various Public/Hybrid or Datacenter Environments. Tanzu Kubernetes Grid Provisions a `Management Cluster` that understands the Cloud/Infrastructure the cluster is provisioned on. Once the `Management Cluster` is created it can be used to create many `Workload Clusters`. Tanzu Kubernetes Grid understands the various clouds primitives needed to build a Kubernetes Cluster. For e.g, when building a cluster on vSphere it will use vCenter to Provision Kubernetes nodes as Virtual Machines, deploy a CSI driver that will use `Datastores` for Storage and will use vDS + `NSX Advanced Load Balancer` to build Network Services. Similarly when Tanzu Kubernetes Grid is used on AWS, it will provision a cluster by deploying EC2 instances for cluster nodes, Elastic Block Storage (EBS) to provision storage and Virtual Private Cloud (VPC) + Elastic Load Balancers (ELB) to build Network services.

## Using Tanzu Mission Control to Provision Workload Clusters and provide Self-service Access

Once the Management Clusters are created on a given Cloud/Infrastructure, they can be added to Tanzu Mission Control for easier access and visibility. A DevOps team can be given access to create more workload clusters. Also, Tanzu Mission Control can also be used to `Attach` any CNCF Conformant clusters that were not built using Tanzu Kubernetes Grid. These cluster can then be bundled together in `Cluster Groups` to apply common policies.

- Click on the Tanzu Mission Control Tab, click on `Clusters` from the left hand navigation.
- Notice Clusters from vSphere, AWS, Azure Google Cloud listed
- Click on `Cluster Groups`, and click on the group `end-to-end` on the left hand Navigation pane, view how different clusters from different cloud Providers can be grouped together. This grouping will help us apply common policies that we will cover in the next chapter.

### Create Workload Clusters

Note: Do not hit `create cluster` at the end of this workflow, and if you do create a cluster please delete them once the demo is over.

- From the left-hand side menu, click on `clusters`. Click on the `Create Cluster` button on the right hand side.
- You will see management clusters for AWS and vSphere listed on the next screen.
- Select any one Management Cluster and click the button `Continue to Create Cluster`
- Notice, how we just have to provide details like `cluster name`, `cluster type` , rest of the details needed are pre-populated and selectable via drop-down
- Hitting the `Create Cluster` button at the end of the wizard will start provisioning the Workload cluster

For e.g, see the below screen shot of the wizard

![Cluster Create](../images/aws-cluster-create.png))

Note: Please Do not hit `create cluster` at the end of this workflow, exit the wizaed by clicking on the `Clsuter` list on the left hand side page. If you do create a cluster please delete them once the demo is over.

## Providing RBAC and Authentication to Development Teams.

 Kubernetes API is very important as it has the capability of accessing and modifying infrastructure objects. Kubernetes has its won Role based access to the Kubernetes API that determine which user has what level of access. However, by default Kubernetes does not provide any Identity backed Authentication. Tanzu Mission Control provides the capability to Automate Role Based Access Control to the API authenticated by your Organizations Identity services. VMware Cloud Services can federate to your LDAP/AD instance. Tanzu Mission Control will map a user from your organization to a role within the Kubernetes Cluster. This is the `Access Policy` and can be applied in a blanket mode across clusters at once.

- Click on `Policies` from the left hand navigation menu, click `Assignments` --> Click `Access` tab.
- You will see a list of Cluster Groups with various clusters in it on the left hand side and access policies on the right hand, like below

![Cluster Access Policy](../images/access-rbac.png)

- Click on the Cluster Group `tko-psp-demo` from the cluster groups list and expand it
- Click on the Direct Access Polices on the right and click the button `Create Role Binding`
- Select the Kubernetes Role from the `Roles` dropdown
- A user from the organizations identity provider can be added with an email ID or an imported drop-down list.
- Exit out of th wizard by clicking the `Cancel` button.
- Notice the existing role binding given to the `Tanzu Dev user`
