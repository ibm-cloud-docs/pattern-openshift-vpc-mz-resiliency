---

copyright:
  years: 2024
lastupdated: "2024-08-02"

subcollection: pattern-openshift-vpc-mz-resiliency

keywords:

---

# Architecture decisions for resiliency
{: #resiliency-architecture}

The following sections summarize the resiliency architecture decisions for Red Hat OpenShift deployed on {{site.data.keyword.Bluemix_notm}} VPC infrastructure.

## Architecture decisions for high availability
{: #arch-ha}

The following are architecture decisions for high availability for this design.

| Architecture decision | Requirement | Alternative | Decision | Rationale |
| -------------- | -------------- | -------------- | -------------- | -------------- |
| Workload and Portworx cluster | - Ensure availability of resources if outages occur. \n - Support SLA targets for application availability.  | - Single zone, single region \n - Multi zone, single region \n - Multi-zone, multi region   | VPCs, subnets, Security Groups (SGs), and ACLs | - Provides protection from zone outage \n - Supports 99.99% availability for the infrastructure \n - Recommended approach for: \n - Core business applications \n - Enterprise-level workloads with stringent resiliency requirements \n - Business continuity policies with country boundaries or geodata residence constraints |
| Portworx Backup cluster                                    | Ensure availability of backup solution if there is a workload cluster failures                                                           | -Separate cluster for Portworx Backup\n - Use workload cluster                                 | Separate cluster for Portworx Backup | A best practice is to use a separate cluster for Portworx Backup since if the backup solution is installed on the workload cluster and in the case the workload cluster goes down so does the backup solution.                                                                                         | Portworx Backup cluster High Availability Deployment       | Ensure availability of Portworx Backup service if outages occur.                                                             | -Single zone, single region\n - Multi zone, single region                                     | Multi-zone, single region            | Provides protection from zone outage |
{: caption="Table 1. Architecture decisions for workload and Portworx cluster." caption-side="bottom"}

## Architecture decisions for backup and restore
{: #arch-backup}

The following are architecture decisions for high availability for this design.
| Architecture decision | Requirement | Alternative | Decision | Rationale |
| -------------- | -------------- | -------------- | -------------- | -------------- |
| Backup type                                                | Backup and restore at the application level                                                                                           | - Application backup \n - Virtual machines backup \n - Nodes backup \n - Other                                         | Application backup                   | Kubernetes consistent backup of applications allows for fast and efficient recovery of applications in the cluster.                                                                                          | Application backup                                         | Backup applications to enable recovery of applications if unplanned outages occur                                            | - Portworx Backup \n - Kasten \n - Trilio for Red Hat OpenShift \n - Others that include Velero, KubeDR, and Zerto for K8s  | Portworx backup                      | Portworx not only supports Kubernetes backup and restore, but also disaster recovery and local high availability. It provides Kubernetes consistent backup of applications across multi-pod in a namespace by using Storage Orchestrator Runtime for Kubernetes (STORK).                                                                                      | Backup automation                                          | Schedule regular applications backups based on backup policy requirements to enable data recovery if unplanned outages occur. | - Portworx backup \n - Kasten \n - Trilio for Red Hat OpenShift \n - Others that include Velero, KubeDR, and Zerto for K8s   | Portworx backup                      | Portworx backup allows defining backup policies to manage the creation and deletion of backup and provides UI in Portworx Central that has RBAC capabilities.                                                                                     {: caption="Table 2. Architecture decisions for backup and restore" caption-side="bottom"}
