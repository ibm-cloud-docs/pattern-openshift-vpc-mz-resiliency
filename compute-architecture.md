---

copyright:
  years: 2024
lastupdated: "2024-08-21"

subcollection: pattern-openshift-vpc-mz-resiliency

keywords:

---

{{site.data.keyword.attribute-definition-list}}

# Architecture decisions for compute
{: #compute-decisions}

The following table summarizes the compute architecture decisions for

| Architecture decision| Requirement | Option | Decision| Rationale|
|---|---|---|---|---|
|Red Hat OpenShift on Virtual Private Cloud (VPC) worker nodes| Provide properly isolated compute resources with adequate compute capacity and storage for the enterprise stateful applications. | - VPC VSIs  \n - VPC Dedicated Hosts  \n -  VPC Bare Metal  \n - VPC Bare Metal SDS Compliant | VPC VSIs | The option that is available for RedHat OpenShift. |
| Host profile for worker nodes|    |Data Intensive: RAM Intensive (multiple) | Based on memory, cores and storage requirements | Select the VSI outlined in the application requirements. A minimum requirement of 8 cores and 8 GB memory is to be reserved for Portworx. |
| Worker nodes distribution across availability zones |  |  Evenly across three availability zones Other| Evenly across three availability zones | If worker nodes are not spread evenly across the zones or capacity is insufficient in one of the zones, the Red Hat OpenShift controller might fail to schedule all requested pods. |
| Worker nodes spread across multiple zones | Provide compute resources that are highly available and can handle increased workload demands. |  Anti-affinity pod deployment with no control | Anti-affinity pod deployment | Protection against zone failure |
| Portwork Backup (PX-Backup) Cluster worker nodes. Three nodes spread across three availability zones. | - Provide compute resources that are highly available for Portwork Backup |  8 cores, 16 GB (VSI) Other options | 8 cores, 16 GB (VSI) | Outlined in the Portworx Backup documentation |
{: caption="Table 4. Architecture decisions for Compute" caption-side="bottom"}
