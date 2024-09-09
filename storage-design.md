---

copyright:
  years: 2024
lastupdated: "2024-08-02"

subcollection: pattern-openshift-vpc-mz-resiliency

keywords:

---

{{site.data.keyword.attribute-definition-list}}

# Storage design
{: #storage-design}

The multizone resiliency pattern for Red Hat OpenShift includes highly available storage options on {{site.data.keyword.Bluemix_notm}} for the stateful containerized applications. The following are the {{site.data.keyword.Bluemix_notm}} storage recommendations for Red Hat OpenShift for enterprise stateful applications:

- Use VPC Block storage 10 IOPS for worker nodes. Create the Block Storage for VPC instances and attach these to each worker node to be added to the Portworx storage layer. For high availability, Portworx requires at least 3 worker nodes with raw and unformatted block storage. Make sure that you spread the nodes evenly across the availability zones.
- Use Cloud Object Storage to store logs and backups. Cloud Object Storage is highly available, durable, and secure and provides region and cross-region [resiliency options](/docs/cloud-object-storage/basics?topic=cloud-object-storage-endpoints). Use region Cloud Object Storage buckets for logs and cross-region Cloud Object Storage buckets for backups.
- Portworx provides a software-defined storage (SDS) solution for a multizone Red Hat OpenShift cluster for stateful apps. Portworx components to be used: PX-Store, STORK, and PX-Backup
