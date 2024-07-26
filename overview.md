---

copyright:
  years: 2024
lastupdated: "2024-07-23"

subcollection: pattern-openshift-vpc-mz-resiliency

keywords:

---

{{site.data.keyword.attribute-definition-list}}

# Overview
{: #overview}



This pattern is intended to:
* Accelerate and simplify solution design by providing a standard IBM Cloud deployment architecture reference following the IBM Architecture Framework.
* Provide a prescriptive, end-2-end enterprise-class solution design, with diagrams, component architecture decisions along with rationale for cloud component selection to meet enterprise requirements.
* Ensure requirements can be met from a performance, system availability and security perspective.






# 1. Pattern Objectives

The objective of this pattern is to provide a resilient solution design for Red Hat OpenShift Kubernetes Service (ROKS) on a Virtual Private Cloud (VPC) architecture deployment, on IBM Cloud, that meets high availability requirements for enterprise workloads that require persistent storage. This pattern is intended to:

-   Accelerate and simplify solution design by providing a standard IBM Cloud deployment architecture reference following the [IBM Architecture Design Framework](https://cloud.ibm.com/docs/architecture-framework).
-   Provide a prescriptive, end-2-end enterprise-class solution design, with diagrams, component architecture decisions along with rationale for cloud component selection to meet enterprise requirements.
-   Ensure requirements can be met from a performance, system availability and security perspective.

This document focuses on leveraging cloud platform capabilities to architect resilient ROKS on VPC. The pattern provides high availability and backup architecture within a single region for ROKS on VPC.

# 2. Pattern Overview

The Multi-Zone Resiliency Pattern for ROKS deploys containerized enterprise workloads that require persistent storage on VPC, using compute, storage, and network cloud resources as well as other Cloud Services provisioned across multiple availability zones within a single region .

The multi-zone ROKS cluster pattern provides 99.99% infrastructure availability. For high availability of stateful application, close coordination between application teams, infrastructure teams (including availability of storage), and database teams is essential to design highly available stateful applications on multi-zone ROKS cluster.

This pattern uses a multi-zone workload cluster configured with Portworx-Store to provide persistent storage for databases and other stateful application components and a Portworx-Backup cluster for application data backup and recovery.

Deploying ROKS cluster across three availability zones is the recommended option for a highly available cluster to be used for highly available stateful applications. IBM Cloud-managed control plane nodes are automatically distributed across availability zones on IBM Cloud. The network communication across IBM Cloud availability zones has low enough latency to satisfy ROKS etcd and Portworx requirements. Portworx provides a robust data service platform for persistent storage with replication and high availability features across multiple availability zones to run stateful containerized applications. This pattern is not to be used across IBM Cloud regions, which have much higher latency for region-to-region network communication.

The Multi-Zone Resiliency Pattern for ROKS can be used to support business continuity policies or regulatory requirements with country boundaries or geo data residence constraints. It does not support out-of-region disaster recovery. [See the **Cross-Region Resiliency Pattern** to address disaster recovery policies or business continuity policies with geo or distance compliance requirements.]

Following the [Architecture Framework](https://cloud.ibm.com/docs/architecture-framework?topic=architecture-framework-intro)\*, the Multi-Zone **Resiliency** Pattern for ROKS covers design considerations and architecture decisions for the following aspects and domains:

-   **Compute:** Containers
-   **Storage:** Primary Storage, Backup Storage, SDS
-   **Networking:** Load Balancing
-   **Security:** Data Security
-   **Resiliency:** High Availability, Backup and Restore
-   **Service Management:** Monitoring, Logging, Auditing, Alerting

| Aspects            | Domains                 |                              |                            |                             |                             |                               |                           |
|--------------------|-------------------------|------------------------------|----------------------------|-----------------------------|-----------------------------|-------------------------------|---------------------------|
| Compute            | Bare Metal Servers      | Virtual Servers              | Virtualization             | Containers                  | Serverless                  |                               |                           |
| Storage            | Primary Storage         | Backup                       | Archive                    | Data  Migration             | SDS                         |                               |                           |
| Networking         | Enterprise Connectivity | BYOIP/Edge Gateways          | Segmentation and Isolation | Cloud Native Connectivity   | Load Balancing              | CDN                           | DNS                       |
| Security           | Data Security           | Identity & Access Management | Application Security       | Infrastructure and Endpoint | Threat Detection & Response | Governance, Risk & Compliance |                           |
| Resiliency         | High Availability       | Disaster Recovery            | Backup and Restore         |                             |                             |                               |                           |
| Service Management | Monitoring              | Logging                      | Auditing                   | Alerting                    | Event Management            | Automated Deployment          | Management/ Orchestration |
| Data               | Application Integration | Data Ops                     | Data Analytics             | Data Storage                | Business Intelligence       | Artificial Intelligence       |                           |

Domains covered in this document.

[Multi-Zone Resiliency for ROKS Solution Design Scope]

\*The Architecture Design Framework provides a consistent approach to design cloud solutions by addressing requirements across a set of "aspects" and "domains", which are technology-agnostic architectural areas that need to be considered for any enterprise solution. See [Introduction to the Architecture Design Framework](https://cloud.ibm.com/docs/architecture-framework?topic=architecture-framework-intro) for more details.
