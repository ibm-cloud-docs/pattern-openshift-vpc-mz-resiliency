---

copyright:
  years: 2024
lastupdated: "2024-08-21"

subcollection: pattern-openshift-vpc-mz-resiliency

keywords:

---

{{site.data.keyword.attribute-definition-list}}

# Architecture decisions for storage
{: #storage-decisions}

| Architecture decision | Requirement |  Option | Decision | Rationale |
| -------------- | -------------- | -------------- | -------------- | -------------- |
| Main Storage for worker nodes       | - Provide highly available storage that meets the application performance requirements.                                                            | - VPC Block storage (5 IOPS) \n - VPC Block storage (10 IOPS) \n - VPC File storage \n - Cloud Object Storage  |  VPC Block storage (10 IOPS) performance for production environment | For Portworx across multi zone. Also, Cloud drives allow to dynamically provision the Portworx volumes. Portworx requires raw and unformatted block storage.                                                                                                                                                                                                                       |
| Containers software defined storage | - Provide highly available storage that abstracts data storage for worker nodes and aggregates them into a virtual pool.                          | - Portworx \n - ODF \n - GlusterFS                                                                          | Portworx                                                               | For persistent storage across multizone for stateful containerized apps. Portworx provides multiple data access mode like ReadWriteMany(RWX). Portworx provides persistent volume with high availability and disaster recovery as well as data protection for backup and recovery.                                                                                                       |
| Portworx architecture               | Provide highly available SDS for generic stateful application                                                                                      | Converged Disaggregated                                                                                | Converged: Each worker node has the role of both computer and storage | Each worker node is connected to VPC Block Storage, which provides reasonable performance for most workloads. Depending on the type of workloads, the disaggregated architecture might be suitable.                                                                                                                                                                            |
| Logs storage: Audit and operational  | - Provide highly available storage for logs                                                                                                        | - VPC Block storage  \n - Cloud Object Storage                                                      | VPC Block storage                                                      | Built-in Red Hat OpenShift monitoring tools are used to and can be installed by using the cluster logging operator. The cluster logging instance needs an {{site.data.keyword.Bluemix_notm}} Block Storage class.                                                                                                                                                                                                  |
| Backup storage                      | - Provide highly available storage for backups                                                                                                     | - VPC Block storage  \n - Cloud Object Storage                                                            | Cloud Object Storage                                                                    | Cloud Object Storage provides low-cost, high available storage for data backups. Cloud Object Storage can be cross-region for data backups to enable recovery if a region outage occurs.                                                                                                                                                                                      |
| Portworx key-value database (KVDB)  | Provide a KVDB solution for Portworx to store the cluster's state, configuration data, and metadata associated with storage volumes and snapshots. | - Internal KVDB for Portworx \n - {{site.data.keyword.Bluemix_notm}} Database for etcd - Other external KVDB                        | Internal KVDB for Portworx                                             | Portworx recommends by using the built-in internal KVDB for your Portworx cluster except when configuring a synchronous disaster recovery setup. For setting up an external KVDB, see [Setting up a Databases for etcd service instance](/docs/containers?topic=containers-storage_portworx_kv_store#portworx-kv-db).  |
{: caption="Table 3. Architecture decisions for Storage" caption-side="bottom"}
