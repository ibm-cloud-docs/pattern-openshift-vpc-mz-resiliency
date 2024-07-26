---

copyright:
  years: 2024
lastupdated: "2024-07-24"

subcollection: pattern-openshift-vpc-mz-resiliency

keywords:

---

{{site.data.keyword.attribute-definition-list}}

# Architecture decisions for compute
{: #compute-decisions}




| **\#**                  | **AD**                                                                             | **Requirement**                                                                                                                   | **Alternative**                                                                   | **Decision**                                     | **Rationale**                                                                                                                                                          |
|-------------------------|------------------------------------------------------------------------------------|-----------------------------------------------------------------------------------------------------------------------------------|-----------------------------------------------------------------------------------|--------------------------------------------------|------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| **Compute: Containers** |                                                                                    |                                                                                                                                   |                                                                                   |                                                  |                                                                                                                                                                        |
| 1                       | ROKS on VPC Worker nodes                                                           | - Provide properly isolated compute resources with adequate compute capacity and storage for the enterprise stateful applications | - VPC VSIs - VPC Dedicated Hosts - VPC Bare Metal - VPC Bare Metal SDS Compliant  | VPC VSIs                                         | Currently available option for ROKS.                                                                                                                                   |
| 2                       | Host Profile for Worker nodes                                                      |                                                                                                                                   | - Data Intensive - RAM Intensive (multiple)                                       |  Based on memory, cores and storage requirements | Select the VSI as per application requirements. Minimum of 8 cores, 8 GB memory is to be reserved for Portworx.                                                        |
| 3                       | Worker Nodes distribution across AZs                                               |                                                                                                                                   | Evenly across three AZs Other                                                     | Evenly across 3 AZs                              | If worker nodes are not spread evenly across the zones or capacity is insufficient in one of the zones, the ROKS controller might fail to schedule all requested pods. |
| 5                       | Worker Nodes Spread across multiple zones                                          | - Provide compute resources that are highly available and can handle increased workload demands                                   | Anti-affinity pod deployment No control                                           | Anti-affinity pod deployment                     |  Protection against zone failure                                                                                                                                       |
| 6                       | Portwork Backup (PX-Backup) Cluster worker nodes (three nodes spread across 3 AZs) | - Provide compute resources that are highly available for PX-Backup                                                               | 8 cores, 16 GB (VSI) Other options                                                | 8 cores, 16 GB (VSI)                             | As per Portworx Backup documentation                                                                                                                                   |
