Applying patches, performing regular upgrades and backups are very critical to maintaining platforms. Tanzu for Kubernetes Operations provides a easy way of performing Upgrades and backups for any kind of cluster in any Cloud.


## Backup Kubernetes Clusters

Development teams may get into a situation where for various reasons, they may want to restore the cluster's resources to a specific time.
Tanzu Mission Control can help backup an entire cluster or a specific namespace from within a cluster. Backup target locations can be any S3 compatible storage or even an AWS S3 location.
Backup target locations can be setup for a fleet of clusters in a Cluster Group providing efficiency of configuration. Tanzu Mission Control also provides a simple interface to setup schedules for backups or trigger a backup anytime.

Go to the tab with Tanzu Mission Control on it,

- Click on `Administration` from the left hand side menu.
- Click on the tab `Target Locations` ,these are the S3 /S3 compatible locations where clusters can be backed up
- Click on the three dots next to the target `tko-dataprotection-target`, and click Edit.
- Notice, the `Cluster Groups` this target is associated to :`tko-psp-demo`
- Any Clusters in that CLuster Group can now start being backed up using this target location

Let's take a look at the backup and restore functions

- Click on `Cluster Groups` from the left hand side menu, click on `tko-psp-demo` cluster group from the list. This is the same cluster group that was associated with the backup target location `tko-dataprotection-target`
- Click on the cluster `gke-psp-demo` from the list of clusters.
- Go to the `Data Protection` tab
- You will notice the backups taken for this cluster
- Click `Create Backup` button to walk through the various options available for creating a backup
- Notice on the `What to backup` tab, there are options to backup the entire cluster, a specific namespace or a specific resource
- Click `Next` Button
- Select a `target` location, all the targets that are available for this cluster's group will be available to select from. 
- Click `Next`button
- Show the options to schedule a backup, weekly, monthly, hourly, custom etc.
- Click through the rest of the workflow, but do not click create. 

Restoring data from a backup location
- On the same DataProtection tab from above, look at the various backup's that have executed , they are listed under the `Backups` section
- Once you click on a any of the backups there, notice the `Restore` Button.
- Click on it 

Warning: Do not go through the Restore process

- Notice you can select to restore the entire cluster, a namespace or a specific resource.
- Click `Cancel` to exit out of the wizard.

## Upgrades

Tanzu Mission Control also provides an efficient way to upgrade Kubernetes versions of a manged workload/management cluster.

Click on any cluster that has an older version on it, that is managed by Tanzu Mission Control, notice the `upgrade` button on it.


