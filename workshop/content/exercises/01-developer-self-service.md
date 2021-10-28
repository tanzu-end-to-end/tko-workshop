Building kubernetes Clusters on various cloud/Infrastructure environments can be non-trivial task, from an operations perspective there are many things to consider.

1. What Base Image should be used to build clusters nodes? and what packages should be in them to make the nodes secure by default?
2. Since Kubernetes needs different Drivers for various Cloud Providers to enable Storage and Networking consumption,each cluster on a cloud provider needs different packages to be installed, how does one automate all of this? 
3. Once these clusters are created, how do you provide Development teams authorized access to the Kubernetes API?
4. Since Kubernetes lets development teams provision storage and networking objects, How do you ensure capacity management?

Tanzu for Kubernetes Operations provides Automated solution to make provisioning Kubernetes Clusters simple, secure and efficient.

## Provision Kubernetes Clusters via Tanzu Kubernetes Grid

**Tanzu Kubernetes Grid** will help automate the Provisioning of Clusters on various Public/Hybrid or Datacenter Environments. Tanzu Kubernetes Grid Provisions a `Management Cluster` that understands the Cloud/Infrastructure the cluster is provisioned on. Once the `Management Cluster` is created it can be used to create many `Workload Clusters`. Tanzu Kubernetes Grid understands the various clouds primitives needed to build a Kubernetes Cluster. For e.g, when building a cluster on vSphere it will use vCenter to Provision Kubernetes nodes as Virtual Machines, deploy a CSI driver that will use `Datastores` for Storage and will use vDS + `AVI Advanced Load Balancer` to build Network Services. Similarly when Tanzu Kubernetes Grid is used on AWS, it will provision a cluster by deploying EC2 instances for cluster nodes, EBS to provision storage and VPC + ELB to build Network services.

## Using Tanzu Mission Control to Provision Workload Clusters and provide Self-service Access

Once the Management Clusters are created they can be added to Tanzu Mission Control for easier access and visibility. A team can be given access to create more workload clusters. Also, Tanzu Mission Control can be used to `Attach` any clusters that were not built using Tanzu Kubernetes Grid. These cluster can then be bundles together in `Cluster Groups` to apply common policies.

- Click on the Tanzu Mission Control Tab, click on `Clusters` from the left hand navigation.
- Notice Clusters from vSphere, AWS, Azure Google Cloud listed
- Click on `Cluster Groups` on the left hand Navigation pane, view how different clusters from different cloud Providers can be grouped together. This grouping will help us apply common policies that we will cover in the next chapter.

### Create Workload Clusters

Note: Do not hit `create cluster` at the end of this workflow, and if you do create a cluster please delete them once the demo is over.

- From the left-hand side menu, click on `clusters`. Click on the `Create Cluster` button on the right hand side.
- You will see management clusters for AWS and vSphere listed on the next screen.
- Select any one Management Cluster and click the button `Continue to Create Cluster`
- Notice, how we just have to provide details like `cluster name`, `cluster type` , rest of the details needed are pre-populated and selectable via drop-down
- Hitting the `Create Cluster` button at the end of the wizard will start provisioning the Workload cluster

For e.g, see the below screen shot of the wizard

![Cluster Create](../images/aws-cluster-create.png)

Note: Please Do not hit `create cluster` at the end of this workflow, and if you do create a cluster please delete them once the demo is over.

## Providing RBAC and Authentication to Development Teams.

- Click on `Policies` from the left hand navigation menu, click `Assignments` --> Click `Access` tab.
- You will see a list of Cluster Groups with various clusters in it on the left hand side and access policies on the right hand, like below

![Cluster Access Policy](../images/access-rbac.png)

- Click on the Direct Access Polices and click the button `Create Role Binding`
- Select the Kubernetes Role from the `Roles` dropdown
- Add a user with email address or select from drop-down list.
- This will enable the user to access the cluster with Identity that is already Configured at the Organization.
