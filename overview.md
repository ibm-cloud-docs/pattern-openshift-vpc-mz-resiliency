---

copyright:
  years: 2024
lastupdated: "2024-07-30"

subcollection: pattern-openshift-vpc-mz-resiliency

keywords:

---

{{site.data.keyword.attribute-definition-list}}

# Overview
{: #overview}

The Red Hat OpenShift on VPC multi zone resiliency pattern is intended to provide an {{site.data.keyword.IBM}} solution design for deployment on {{site.data.keyword.Bluemix_short}}

This document focuses on using cloud platform capabilities to architect a resilient Red Hat OpenShift on Virtual Private Cloud solution. The pattern provides high availability and backup architecture within a single region for Red Hat OpenShift on VPC.

## Pattern objectives
{: #vpc-resiliency-objectives}

The objective of this pattern is to provide a resilient solution design for Red Hat OpenShift Kubernetes Service on a Virtual Private Cloud (VPC) architecture deployment, on {{site.data.keyword.Bluemix_notm}}, that meets high availability requirements for enterprise workloads that require persistent storage. This pattern is intended to:

-   Accelerate and simplify solution design by providing a standard {{site.data.keyword.Bluemix_notm}} deployment architecture reference following the [IBM Architecture Design Framework](https://cloud.ibm.com/docs/architecture-framework).
-   Provide a prescriptive, end-2-end enterprise-class solution design, with diagrams, component architecture decisions along with rationale for cloud component selection to meet enterprise requirements.
-   Ensure that requirements can be met from a performance, system availability and security perspective.

##  Pattern details
{: #vpc-resiliency-details}

The multi-zone resiliency pattern for Red Hat OpenShift deploys containerized enterprise workloads that require persistent storage on VPC, using compute, storage, and network cloud resources as well as other cloud services provisioned across multiple availability zones within a single region.

The multi-zone Red Hat OpenShift cluster pattern provides 99.99% infrastructure availability. For high availability of stateful application, close coordination between application teams, infrastructure teams (including availability of storage), and database teams is essential to design highly available stateful applications on a multi-zone ROKS cluster.

This pattern uses a multi-zone workload cluster that is configured with Portworx-Store to provide persistent storage for databases and other stateful application components and a Portworx-Backup cluster for application data backup and recovery.

Deploying ROKS cluster across three availability zones is the recommended option for a highly available cluster to be used for highly available stateful applications. {{site.data.keyword.Bluemix_notm}}-managed control plane nodes are automatically distributed across availability zones on {{site.data.keyword.Bluemix_notm}}. The network communication across {{site.data.keyword.Bluemix_notm}} availability zones has low enough latency to satisfy ROKS etcd and Portworx requirements. Portworx provides a robust data service platform for persistent storage with replication and high availability features across multiple availability zones to run stateful containerized applications. This pattern is not to be used across {{site.data.keyword.Bluemix_notm}} regions, which have higher latency for region-to-region network communication.

The Multi-Zone Resiliency Pattern for ROKS can be used to support business continuity policies or regulatory requirements with country boundaries or geo data residence constraints. It does not support out-of-region disaster recovery. [See the **Cross-Region Resiliency Pattern** to address disaster recovery policies or business continuity policies with geo or distance compliance requirements.]

Following the [Architecture Framework](/docs/architecture-framework?topic=architecture-framework-intro), the multi-zone resiliency pattern for Red Hat OpenShift covers design considerations and architecture decisions for the following aspects and domains:

-   **Compute:** Containers
-   **Storage:** Primary Storage, Backup Storage, SDS
-   **Networking:** Load Balancing
-   **Security:** Data Security
-   **Resiliency:** High Availability, Backup, and Restore
-   **Service Management:** Monitoring, Logging, Auditing, Alerting

![](image/heat-map.svg)

[Multi-Zone Resiliency for ROKS Solution Design Scope]

The Architecture Design Framework provides a consistent approach to design cloud solutions by addressing requirements across a set of "aspects" and "domains", which are technology-agnostic architectural areas that need to be considered for any enterprise solution. For more information, see [Introduction to the Architecture Design Framework](/docs/architecture-framework?topic=architecture-framework-intro). 
