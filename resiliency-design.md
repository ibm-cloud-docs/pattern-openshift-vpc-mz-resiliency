---

copyright:
  years: 2024
lastupdated: "2024-08-02"

subcollection: pattern-openshift-vpc-mz-resiliency

keywords:

---

{{site.data.keyword.attribute-definition-list}}

# Resiliency design considerations
{: #resiliency-design}

The resiliency of applications that are deployed on Red Hat OpenShift clusters is determined by the availability of the cluster as well as the availability of the application.

## Cluster availability
{: #cluster-availability}

Namely, the availability of Red Hat OpenShift clusters depends on the availability of the control plane components the main nodes and etcd database, and the availability of the worker nodes where the applications are deployed.

For workloads deployed on Red Hat OpenShift on {{site.data.keyword.Bluemix_notm}} clusters, {{site.data.keyword.Bluemix_notm}} is responsible for the availability of the cluster’s control plane. When a Red Hat OpenShift on {{site.data.keyword.Bluemix_notm}} cluster is created, {{site.data.keyword.Bluemix_notm}} sets up highly available main nodes and control plane components, automatically backs up etcd data, and provides automated disaster recovery for the control plane. {{site.data.keyword.Bluemix_notm}} users are responsible for setting up highly available worker nodes. The following are deployment options for Red Hat OpenShift on {{site.data.keyword.Bluemix_notm}} clusters:

### Single-zone clusters
{: #single-zone-clusters}

In single zone clusters, all the worker nodes are provisioned in one availability zone that is selected by the user. For high availability, at least 3 worker nodes are recommended so that application instances can be distributed across multiple worker nodes to provide protection from worker node failures. Red Hat OpenShift automatically reschedules pods from unavailable worker nodes. In single-zone clusters, {{site.data.keyword.Bluemix_notm}} automatically creates three replicas of main components for high availability. If the cluster is created in a multizone region, the three primary replicas are spread across the availability zones in the region.

Single-zone clusters provide protection from logical and container failures but do not protect from zone failures or physical host failures since there is no guarantee that the worker nodes will be placed in different physical hosts. Single-zone clusters provide cost-effective solutions with 99.9% infrastructure availability that might be appropriate for nonproduction environments or nonbusiness critical applications.

### Multizone clusters
{: #multizone-clusters}

In multizone clusters, the worker nodes are distributed across availability zones that are selected by the user. {{site.data.keyword.Bluemix_notm}} automatically provisions three replicas of primary components spread across availability zones within a region for high availability.

It is recommended to spread the worker nodes evenly across three zones, with 50% capacity provisioned in each zone for a total 150% capacity across all worker nodes. This is a cost-efficient solution that guarantees 100% capacity for the workloads if one zone fails and provides 99.99% infrastructure availability. In contrast, spreading the nodes across two zones would require 100% capacity in each zone for a total capacity of 200% vs 150% and would offer only 99.9% infrastructure availability.

Multizone clusters, also known as stretched clusters, provide protection from zone failures and physical host failures. If resources in one zone go down, the workloads can continue to run in worker nodes in the other zones.

Multi-zone clusters are recommended for business-critical applications.
{: note}

## Application availability
{: #application-availability}

The availability of a containerized application depends on the availability of the pods where the application is deployed and the application’s scalability and ability to handle workload changes. For stateful applications, persistent storage data availability and consistency are also important considerations.

You can use Kubernetes constructs such as deployments, replica sets, and pod anti-affinity to increase the application availability, as follows:

- Use a [deployment](https://kubernetes.io/docs/concepts/workloads/controllers/deployment/){: external} to support pod rescheduling, replica sets, and rolling updates.
- Use [replica sets](/docs/openshift?topic=openshift-app#replicaset) to define multiple instances of your application. Include enough replicas for your workload plus two.
- Use [pod anti-affinity](/docs/openshift?topic=openshift-app#affinity) options to spread the deployment of pods across different worker nodes to protect applications from worker nodes failures. Worker nodes in one zone are not guaranteed to be on separate physical compute hosts. To protect against physical compute hosts failure, you must set up a multizone cluster, which spreads worker nodes across physical hosts in different zones.
- Enable [horizontal pod autoscaling](/docs/openshift?topic=openshift-update_app#app_scaling) to automatically increase or decrease the number of instances of the application based on metrics that are collected from the pods.
- Use a [cluster autoscaler](/docs/openshift?topic=openshift-cluster-scaling-classic-vpc&interface=ui) to automatically increase or decrease the number of worker nodes based on workload demands.

For stateful applications, use highly available persistent storage based on the application requirements and deploy applications in a multizone cluster to distribute persistent storage data across multiple availability zones.

In multizone clusters, data replication across availability zones can be achieved through application-level replication or storage replication.

Portworx and Red Hat OpenShift Data Foundation (ODF) are Software Defined Storage (SDS) solutions that support data replication and can be deployed on Red Hat OpenShift clusters on {{site.data.keyword.Bluemix_notm}} to provide persistent storage for stateful apps. Portworx and Red Hat OpenShift Data Foundation replicate data across worker nodes to ensure that the stateful application can be rescheduled to a different worker node if there is a failure without losing data.

## High availability design
{: #high-availability-design}

The multizone resiliency pattern for Red Hat OpenShift deploys the Red Hat OpenShift cluster architecture across multiple availability zones within a region. The worker nodes are evenly spread across three availability zones. For multizone clusters, Portworx is recommended as an SDS for stateful applications. Portworx is a highly available SDS solution that provides persistent storage for the containerized databases and stateful apps and enables sharing of data between pods across multiple zones. Portworx abstracts storage of various types and sizes that are attached to the worker nodes in the cluster. Worker nodes with attached storage are added as a node to a storage cluster and presented as a virtual storage pool for the stateful applications. The storage cluster is managed by PX-Store, a component of Portworx and it automatically replicates data in the volumes across worker nodes and zones in the cluster so stateful applications can be rescheduled to another worker node if there is a worker node failure or restart providing high availability across nodes, racks, and availability zones. PX-Store supports multi-writer shared volumes across multiple containers.

The following are Portworx high availability requirements for enterprise stateful applications:

- A minimum 3 worker nodes across 3 AZs
- A minimum of 8 cores and 8 GB RAM are recommended to be reserved for Portworx  
    
These are the minimum number of nodes, cores, and memory. Depending on the scale of the production environment, more nodes and higher cores and memory are required.
{: note}

## Backup and restore design
{: #backup-and-restore-design}

Backup and restore solutions for containerized applications must protect both the cluster as well as the application to enable recovery if there are failures triggered by ransomware attacks, software data corruption, accidental deletions, or any other cause.

Namely, red Hat OpenShift on {{site.data.keyword.Bluemix_notm}} clusters include automatic back-up and recovery of cluster control plane components, etcd data. The user is responsible for backing up the application. Application backups must protect application data and metadata that is included in application-related cluster resources, stateful application’s data stored in persistent storage, and internal images.

The Multi-zone Resiliency pattern for Red Hat OpenShift uses Portworx to backup the application.
{: note}

Portworx backup provides a Kubernetes data protection platform for the persistent data both in transit and at rest. It is app-aware including stateful apps and provides a simple self-service interface for backup and restore. It can operate at a pod, namespace, or cluster level. Portworx Backup (PX-Backup) is setup in Management VPC in an administration mutizone cluster. The following is a Portworx backup solution at a high level:

- Backup Source: Portworx Enterprise (PX-Store) – SDS for stateful application
- Target: {{site.data.keyword.Bluemix_notm}} Object Storage (Cloud Object Storage)
- Portworx has Backup Automation and Scheduling
- Storage Orchestrator Runtime for Kubernetes (STORK) provides Kubernetes consistent backup of applications that are running in a workload cluster across multi-pod in a namespace. STORK is an agent and should be installed on the application cluster before PX-Backup cluster installation. STORK bridges the gap between Portworx backup running on your management cluster, and the workload cluster that needs to be backed up.
- Portworx Backup uses MongoDB that runs with 3 replicas for high availability as the data store and is installed as part of the Portworx Backup deployment for writing the metadata of backup object data.

![](image/Portworx.svg)
{: caption="Figure 4. Portworx Backup Architecture" caption-side="bottom"}

