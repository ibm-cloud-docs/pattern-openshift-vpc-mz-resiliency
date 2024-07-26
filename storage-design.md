---

copyright:
  years: 2024
lastupdated: "2024-07-24"

subcollection: pattern-openshift-vpc-mz-resiliency

keywords:

---

{{site.data.keyword.attribute-definition-list}}

# Storage design
{: #storage-design}


The Multi-zone Resiliency pattern for ROKS includes highly available storage options on IBM Cloud for the stateful containerized applications.

The following are the IBM Cloud storage recommendations for ROKS for enterprise stateful applications:

-   Use VPC Block storage (10 IOPS) for worker nodes. Create the Block Storage for VPC instances and attach these to each worker node to be added to the Portworx storage layer. For highly availability, Portworx requires at least 3 worker nodes with raw and unformatted block storage (spread the nodes evenly across the AZs).
-   Use Cloud Object Storage to store logs and backups. Cloud Object Storage (COS) is highly available, durable, and secure and provides region and cross-region [resiliency options](https://cloud.ibm.com/docs/cloud-object-storage/basics?topic=cloud-object-storage-endpoints). Use region COS buckets for logs and cross-region COS buckets for backups.
-   Portworx provides a software defined storage (SDS) solution for a multizone ROKS clusters for stateful apps. Portworx components to be used: PX-Store, STORK and PX-Backup