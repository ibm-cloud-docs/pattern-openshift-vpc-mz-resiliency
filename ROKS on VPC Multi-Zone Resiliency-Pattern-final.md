Red Hat OpenShift Kubernetes Service on Virtual Private Cloud Multi-Zone Resiliency Pattern

Dwarkanath P Rao1

[Company]

June 11, 2024

Table of Contents

[Executive Summary](#executive-summary)

[1. Pattern Objectives](#1-pattern-objectives)

[2. Pattern Overview](#2-pattern-overview)

[3. Pattern Requirements](#3-pattern-requirements)

[4. Solution Architecture](#4-solution-architecture)

[4.1 Solution Architecture Diagram](#41-solution-architecture-diagram)

[4.2 Solution Components](#42-solution-components)

[5. Compute Design Considerations](#5-compute-design-considerations)

[6. Storage Design Considerations](#6-storage-design-considerations)

[7. Networking Design Considerations](#7-networking-design-considerations)

[9. Resiliency Design Considerations](#9-resiliency-design-considerations)

[9.1 High Availability Design](#91-high-availability-design)

[9.2 Backup and Restore Design](#92-backup-and-restore-design)

[10. Service Management Design Considerations](#10-service-management-design-considerations)

[10.1 Monitoring Design](#_Toc168913045)

[10.2 Logging Design](#_Toc168913046)

[10.3 Alerting Design](#_Toc168913047)

[12. Architecture Decisions](#12-architecture-decisions)

[12.1 Compute Architecture Decisions](#121-compute-architecture-decisions)

[12.2 Storage Architecture Decisions](#122-storage-architecture-decisions)

[12.3 Networking Architecture Decisions](#123-networking-architecture-decisions)

[12.4 Security Architecture Decisions](#124-security-architecture-decisions)

[12.4 Resiliency Architecture Decisions](#124-resiliency-architecture-decisions)

[12.5 Service Management Architecture Decisions](#125-service-management-architecture-decisions)

[13. References](#13-references)

[14. Glossary of Terms/Abbreviations](#14-glossary-of-termsabbreviations)

[No index entries found.](#no-index-entries-found)

# Executive Summary

\<Note to author\> \<Leave this section blank – AI will be used to create\>

# 1. Pattern Objectives

The objective of this pattern is to provide a resilient solution design for Red Hat OpenShift Kubernetes Service (ROKS) on a Virtual Private Cloud (VPC) architecture deployment, on {{site.data.keyword.Bluemix_notm}}, that meets high availability requirements for enterprise workloads that require persistent storage. This pattern is intended to:

-   Accelerate and simplify solution design by providing a standard {{site.data.keyword.Bluemix_notm}} deployment architecture reference following the [IBM Architecture Design Framework](https://cloud.ibm.com/docs/architecture-framework).
-   Provide a prescriptive, end-2-end enterprise-class solution design, with diagrams, component architecture decisions along with rationale for cloud component selection to meet enterprise requirements.
-   Ensure requirements can be met from a performance, system availability and security perspective.

This document focuses on leveraging cloud platform capabilities to architect resilient ROKS on VPC. The pattern provides high availability and backup architecture within a single region for Red Hat OpenShift on VPC.

# 2. Pattern Overview

The Multi-Zone Resiliency Pattern for Red Hat OpenShift deploys containerized enterprise workloads that require persistent storage on VPC, using compute, storage, and network cloud resources as well as other Cloud Services provisioned across multiple availability zones within a single region .

The multi-zone Red Hat OpenShift cluster pattern provides 99.99% infrastructure availability. For high availability of stateful application, close coordination between application teams, infrastructure teams (including availability of storage), and database teams is essential to design highly available stateful applications on multi-zone Red Hat OpenShift cluster.

This pattern uses a multi-zone workload cluster configured with Portworx-Store to provide persistent storage for databases and other stateful application components and a Portworx-Backup cluster for application data backup and recovery.

Deploying Red Hat OpenShift cluster across three availability zones is the recommended option for a highly available cluster to be used for highly available stateful applications. {{site.data.keyword.Bluemix_notm}}-managed control plane nodes are automatically distributed across availability zones on {{site.data.keyword.Bluemix_notm}}. The network communication across {{site.data.keyword.Bluemix_notm}} availability zones has low enough latency to satisfy Red Hat OpenShift etcd and Portworx requirements. Portworx provides a robust data service platform for persistent storage with replication and high availability features across multiple availability zones to run stateful containerized applications. This pattern is not to be used across {{site.data.keyword.Bluemix_notm}} regions, which have much higher latency for region-to-region network communication.

The Multi-Zone Resiliency Pattern for Red Hat OpenShift can be used to support business continuity policies or regulatory requirements with country boundaries or geo data residence constraints. It does not support out-of-region disaster recovery. [See the **Cross-Region Resiliency Pattern** to address disaster recovery policies or business continuity policies with geo or distance compliance requirements.]

Following the [Architecture Framework](https://cloud.ibm.com/docs/architecture-framework?topic=architecture-framework-intro)\*, the Multi-Zone **Resiliency** Pattern for Red Hat OpenShift covers design considerations and architecture decisions for the following aspects and domains:

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

[Multi-Zone Resiliency for Red Hat OpenShift Solution Design Scope]

\*The Architecture Design Framework provides a consistent approach to design cloud solutions by addressing requirements across a set of "aspects" and "domains", which are technology-agnostic architectural areas that need to be considered for any enterprise solution. See [Introduction to the Architecture Design Framework](https://cloud.ibm.com/docs/architecture-framework?topic=architecture-framework-intro) for more details.

# 3. Pattern Requirements

The following represents a typical set of requirements for enterprise-ready Red Hat OpenShift deployed in a public cloud.

| **Aspect**         | **Requirements**                                                                                                                                             |
|--------------------|--------------------------------------------------------------------------------------------------------------------------------------------------------------|
| Compute            | Provide platform for containerized applications and management tools with adequate compute capacity                                                          |
| Storage            | Provide storage that meets the stateful applications and database requirements.                                                                              |
| Networking         | Deploy workloads in isolated environment and enforce information flow policies.                                                                              |
|                    |                                                                                                                                                              |
|                    | Distribute incoming application requests across available compute resources.                                                                                 |
|                    |                                                                                                                                                              |
| Security           | Ensure all operation actions are executed securely.                                                                                                          |
|                    |                                                                                                                                                              |
|                    | Ensure data encryption at rest and in transit for the storage layer.                                                                                         |
|                    | Encrypt all backup data to protect from unauthorized disclosure.                                                                                             |
|                    | Encrypt all security data (operational and audit logs) to protect from unauthorized disclosure.                                                              |
|                    | Encrypt all data using customer managed keys to meet regulatory compliance requirements for additional security and customer control.                        |
|                    |                                                                                                                                                              |
| Resiliency         | Provide containerized platform that supports application availability targets and business continuity policies.                                              |
|                    | Provide highly available compute, storage, network, and other cloud services for a resilient containerized application with persistent storage requirements. |
|                    | Provide backup solution for containers platform and application data to enable recovery in the event of unplanned outages.                                   |
|                    | Provide highly available storage for containerized databases and stateful applications.                                                                      |
| Service Management | Monitor system health metrics and logs to detect issues that might impact the availability of the platform.                                                  |
|                    | Generate alerts/notifications about issues that might impact the availability of platform to trigger appropriate responses to minimize down time.            |
|                    | Monitor audit logs to track changes and detect potential security problems.                                                                                  |
|                    | Provide a mechanism to identify and send notifications about issues found in audit logs.                                                                     |

# 4. Solution Architecture

-   Red Hat OpenShift is deployed on VPC Servers across three availability zones within a region.
-   Compatible/recommended nodes (that are currently available for Red Hat OpenShift on {{site.data.keyword.Bluemix_notm}} – Virtual Servers Instances Shared) are used for worker nodes to run stateful application in production environment.   
    ![](image/545eb7ba85b9774f5a5a2b170ea6242a.png)Ref: IBM Cloud Docs
-   High performance Secondary storage (10 IOPS Block) is used and attached to worker nodes. Portworx Enterprise (an SDS – Software Defined Storage) is setup in the cluster across three zones using a Converged architecture (compute and storage in same node). Built-in internal key-value database (KVDB) is used for Portworx cluster.
-   ![Portworx deployment architecture hyperconverged](image/0c1947f67fce0cfed4a32e2fb6107f7d.png)Ref: Portworx Docs
-   Any databases that may be required for the stateful applications use the Portworx SDS.
-   In the Red Hat OpenShift multizone cluster, the worker nodes in the worker pools are distributed across multiple zones within one region. Red Hat OpenShift multizone clusters are designed to *evenly* schedule pods across worker nodes and zones to ensure availability and recovery from failure. If worker nodes are not spread *evenly* across the zones or capacity is insufficient in one of the zones, the Red Hat OpenShift controller might fail to schedule all requested pods.
-   Application Load Balancer (ALB) for VPC with multi-zone support is used to route traffic to the containerized application.
-   Containerized stateful applications are deployed within the Workload cluster and VPC. Management tools, such as backup tools, are deployed in the Management cluster and VPC. A local Transit Gateway allows traffic between the management and workload VPCs.
-   Worker nodes in worker pools are placed in separate subnets within each availability zone.
-   Security groups and ACLs (Access Control Lists) are used as firewalls to limit access to worker node server for operational purposes and control network traffic.
-   All storage is encrypted at rest using storage encryption with customer-provided keys managed by Key Protect.
-   Data is encrypted in transit using TLS encryption. Portworx Backup is used to enable backup and recovery of containerized applications deployed in the workload cluster. The Portworx Backup source is the PX-Store (SDS) configured for the workload cluster. The Portwork Backup target is IBM Cloud Object Storage (COS). Portworx Backup supports encryption of backups in transit. IBM COS supports encryption with Key Management Services. Portworx Backup is setup in management VPC in a management or administration cluster also spread across three availability zones.
-   The OpenShift Container Platform monitoring stack is based on the Prometheus, which also can be used to monitor the Portworx cluster
-   Built-in Red Hat OpenShift logging tools are used for Portworx logging.

## 4.1 Solution Architecture Diagram

![](image/30d2ddd09c25b8b2bb1dbc65da9c7602.png)

[Multi-Zone Resiliency for Red Hat OpenShift Solution Architecture]

## 4.2 Solution Components

| Category              | Solution Components                                                                                                                   | How it is used in solution                                                                                                            |
|-----------------------|---------------------------------------------------------------------------------------------------------------------------------------|---------------------------------------------------------------------------------------------------------------------------------------|
| Compute               | Red Hat OpenShift Cluster                                                                                                             | Kubernetes cluster for containerized applications and backup tools                                                                    |
| Storage               | VPC Block Storage                                                                                                                     | For Worker nodes storage & Portworx SDS                                                                                               |
|                       | Portworx Enterprise                                                                                                                   | Portworx Enterprise provides highly available unified storage across multiple zones for stateful applications                         |
|                       | [Cloud Object Storage](https://cloud.ibm.com/docs/cloud-object-storage?topic=cloud-object-storage-about-cloud-object-storage) (COS)   | backups, logs (application, operational and audit logs)                                                                               |
|                       | Portworx Internal KVDB                                                                                                                | To store the cluster's state, configuration data, and metadata associated with storage volumes and snapshots.                         |
| Networking            | [VPC Virtual Private Network (VPN) Client](https://cloud.ibm.com/docs/iaas-vpn?topic=iaas-vpn-getting-started)                        | Remote access to manage resources in private network                                                                                  |
|                       | [Virtual Private Clouds (VPCs), Subnets, Security Groups (SGs), ACLs](https://cloud.ibm.com/docs/vpc?topic=vpc-getting-started)       | VPCs for workload isolation Subnets, SGs, and ACLs for restricted access to stateful application components                           |
|                       | [Local Transit Gateway (TGW)](https://cloud.ibm.com/docs/transit-gateway?topic=transit-gateway-getting-started)                       | Connectivity between Workload and Management VPCs                                                                                     |
|                       | [Virtual Private Gateway & Virtual Private Endpoint (VPE)](https://cloud.ibm.com/docs/vpc?topic=vpc-about-vpe)                        | Private network access to Cloud Services, e.g. Key Protect, COS, etc.                                                                 |
|                       | [VPC Application Load Balancer](https://cloud.ibm.com/docs/vpc?topic=vpc-load-balancers)                                              | Application Load Balancing for containerized apps                                                                                     |
|                       |                                                                                                                                       |                                                                                                                                       |
|                       | [Cloud Internet Services (CIS)](https://cloud.ibm.com/docs/cis?topic=cis-getting-started)                                             | Public DNS resolution                                                                                                                 |
|                       | [DNS Services](https://cloud.ibm.com/docs/dns-svcs?topic=dns-svcs-about-dns-services)                                                 | Private DNS resolution                                                                                                                |
| Security              | [IAM](https://cloud.ibm.com/docs/account?topic=account-cloudaccess)                                                                   | IBM Cloud Identity & Access Management                                                                                                |
|                       | [Cloud Internet Services (CIS)](https://cloud.ibm.com/docs/cis?topic=cis-getting-started)                                             | DDoS protection and Web App Firewall                                                                                                  |
|                       | [Key Protect](https://cloud.ibm.com/docs/key-protect?topic=key-protect-about)                                                         | Key Management Service                                                                                                                |
|                       | [Secrets Manager](https://cloud.ibm.com/catalog/services/secrets-manager)                                                             | Certificate and Secrets Management                                                                                                    |
|                       |                                                                                                                                       |                                                                                                                                       |
| Service Management    | OpenShift Logging & Monitoring Tools                                                                                                  | For ROKS cluster and app logs and cluster metrics and app monitoring & Portworx Enterprise and Portworx Backup logging and monitoring |
| Resiliency & Capacity | [Cross-Region COS Buckets](https://cloud.ibm.com/docs/cloud-object-storage/basics?topic=cloud-object-storage-endpoints#endpoints-geo) | Backup storage (target for PX-Backup)                                                                                                 |
|                       | Cluster Autoscaler                                                                                                                    | To adjust compute and storage capacity based on load changes                                                                          |
|                       | VPC VSI (worker nodes) evenly spread across three zones, Portworx Enterprise (SDS) across multiple zones in a region                  | For Stateful Containerized apps high availability deployment                                                                          |
|                       | Portworx Backup (PX-Backup)                                                                                                           | For Backup and Restore of stateful applications data                                                                                  |

# 5. Compute Design Considerations

The Multi-zone Resiliency pattern for ROKS includes compute options that are highly available, properly isolated, provide the defined capacity for the applications, and can handle increased workload demands.

Provision VPC Virtual Server Infrastructure (VSI) for the worker nodes as follows:

-   VPC Virtual Server Infrastructure (VSI): select compute and memory profiles, based on capacity and performance requirements. Spread worker nodes hosts evenly across three availability zones (less than 10ms latency required for synchronous replication). 8 cores, 8 GB memory is to be reserved for Portworx, and 10 Gbps bandwidth is recommended.
-   Cluster-Autoscaler: With the cluster-autoscaler add-on, the worker pools in ROKS on IBM Cloud VPC cluster can be scaled automatically to increase or decrease the number of worker nodes in the worker pool based on the sizing needs of the scheduled workloads.
-   Multi-zone deployment: deploy worker nodes across multiple availability zones to protect the application from zone outages. Use a VPC Application Load Balancer (ALB) to front end the services.

# 6. Storage Design Considerations

The Multi-zone Resiliency pattern for ROKS includes highly available storage options on IBM Cloud for the stateful containerized applications.

The following are the IBM Cloud storage recommendations for ROKS for enterprise stateful applications:

-   Use VPC Block storage (10 IOPS) for worker nodes. Create the Block Storage for VPC instances and attach these to each worker node to be added to the Portworx storage layer. For highly availability, Portworx requires at least 3 worker nodes with raw and unformatted block storage (spread the nodes evenly across the AZs).
-   Use Cloud Object Storage to store logs and backups. Cloud Object Storage (COS) is highly available, durable, and secure and provides region and cross-region [resiliency options](https://cloud.ibm.com/docs/cloud-object-storage/basics?topic=cloud-object-storage-endpoints). Use region COS buckets for logs and cross-region COS buckets for backups.
-   Portworx provides a software defined storage (SDS) solution for a multizone ROKS clusters for stateful apps. Portworx components to be used: PX-Store, STORK and PX-Backup

# 7. Networking Design Considerations

The Multi-zone Resiliency pattern for ROKS leverages IBM Cloud VPC infrastructure and network services to segment the management and application workloads and support the application deployment across multiple availability zones.

-   Deploy the ROKS within a Virtual Private Cloud (VPC) provisioned across multiple availability zones within a region to provide workload isolation within the public cloud.
-   Place each worker node in a separate subnet in each availability zone (less than 10ms latency required for synchronous replication). Use security groups and ACLs as firewalls to limit access to server instances for operational purposes and to control network traffic.
-   Use VPC Application Load Balancers (ALB) to distribute incoming requests to stateful containerized applications.

# 9. Resiliency Design Considerations

The resiliency of applications deployed on Red Hat OpenShift clusters is determined by the availability of the cluster as well as the availability of the application.

**Cluster availability**

The availability of Red Hat OpenShift clusters depends on the availability of the control plane components, namely, the master nodes and etcd database, and the availability of the worker nodes where the applications are deployed.

For workloads deployed on Red Hat OpenShift on IBM Cloud clusters, IBM Cloud is responsible for the availability of the cluster’s control plane. When a Red Hat OpenShift on IBM Cloud cluster is created, IBM Cloud sets up highly available master nodes and control plane components, automatically backs up etcd data, and provides automated disaster recovery for the control plane. IBM Cloud users are responsible for setting up highly available worker nodes. The following are deployment options for Red Hat OpenShift on IBM Cloud clusters:

**Single-zone clusters**

In single zone clusters, all the worker nodes are provisioned in one availability zone selected by the user. For high availability, at least 3 worker nodes are recommended so that application instances can be distributed across multiple worker nodes to provide protection from worker node failures. Red Hat OpenShift automatically reschedules pods from unavailable worker nodes. In single-zone clusters, IBM Cloud automatically creates three replicas of master components for high availability. If the cluster is created in a multizone region, the three master replicas are spread across the availability zones in the region.

Single-zone clusters provide protection from logical and container failures but do not protect from zone failures or physical host failures since there is no guarantee that the worker nodes will be placed in different physical hosts. Single-zone clusters provide cost-effective solutions with 99.9% infrastructure availability that might be appropriate for non-production environments or non-business critical applications.

**Multi-zone clusters**

In multi-zone clusters, the worker nodes are distributed across availability zones selected by the user. IBM Cloud automatically provisions three replicas of master components spread across availability zones within a region for high availability.

It is recommended to spread the worker nodes evenly across three zones, with 50% capacity provisioned in each zone for a total 150% capacity across all worker nodes. This is a cost-efficient solution that guarantees 100% capacity for the workloads in the event of one zone failure and provides 99.99% infrastructure availability. In contrast, spreading the nodes across two zones would require 100% capacity in each zone for a total capacity of 200% vs 150% and would offer only 99.9% infrastructure availability.

Multi-zone clusters, also known as stretched clusters, provide protection from zone failures and physical host failures. If resources in one zone go down, the workloads can continue to run in worker nodes in the other zones.

Multi-zone clusters are recommended for business-critical applications.

**Application availability**

The availability of a containerized application depends on the availability of the pods where the application is deployed and the application’s scalability and ability to handle workload changes. For stateful applications, persistent storage data availability and consistency are also important considerations.

You can use Kubernetes constructs such as deployments, replica sets, and pod anti-affinity to increase the application availability, as follows:

-   Use a [deployment](https://kubernetes.io/docs/concepts/workloads/controllers/deployment/) to support pod rescheduling, replica sets, and rolling updates.
-   Use [replica sets](https://cloud.ibm.com/docs/openshift?topic=openshift-app#replicaset) to define multiple instances of your application. Include enough replicas for your workload plus two.
-   Use [pod anti-affinity](https://cloud.ibm.com/docs/openshift?topic=openshift-app#affinity) options to spread the deployment of pods across different worker nodes to protect applications from worker nodes failures. Note that worker nodes in one zone are not guaranteed to be on separate physical compute hosts. To protect against physical compute hosts failure, you must set up a multi-zone cluster which will spread worker nodes across physical hosts in different zones.

Enable [horizontal pod autoscaling](https://cloud.ibm.com/docs/openshift?topic=openshift-update_app#app_scaling) to automatically increase or decrease the number of instances of the application based on metrics collected from the pods.

Use [cluster autoscaler](https://cloud.ibm.com/docs/openshift?topic=openshift-cluster-scaling-classic-vpc&interface=ui) to automatically increase or decrease the number of worker nodes based on workload demands.

For stateful applications, use highly available persistent storage based on the application requirements and deploy applications in a multizone cluster to distribute persistent storage data across multiple availability zones.

In multi-zone clusters, data replication across availability zones can be achieved through application-level replication or storage replication.

Portworx and OpenShift Data Foundation (ODF) are Software Defined Storage (SDS) solutions that support data replication and can be deployed on Red Hat OpenShift clusters on IBM Cloud to provide persistent storage for stateful apps. Portworx and ODF replicate data across worker nodes to ensure the stateful app can be rescheduled to a different worker node in case of failure without losing data.

## 9.1 High Availability Design

The Multi-zone Resiliency pattern for ROKS deploys the Red Hat OpenShift cluster architecture across multiple availability zones within a region. The worker nodes are evenly spread across three availability zones. For multizone clusters, Portworx is recommended as SDS for stateful applications. Portworx is a highly available SDS solution that provides persistent storage for the containerized databases and stateful apps and enables sharing of data between pods across multiple zones. Portworx abstracts storage of various types and sizes attached to the worker nodes in the cluster. Worker nodes with attached storage are added as a node to a storage cluster and presented as a virtual storage pool for the stateful applications. The storage cluster is managed by PX-Store, a component of Portworx and it automatically replicates data in the volumes across worker nodes and zones in the cluster so stateful applications can be rescheduled to another worker node in case of a worker node failure or reboot providing high availability across nodes/racks/AZs. PX-Store supports multi-writer shared volumes across multiple containers.

Following are Portworx (High Availability) requirements for enterprise stateful applications:

-   Minimum 3 worker nodes across 3 AZs
-   Minimum of 8 cores & 8 GB RAM are recommended to be reserved for Portworx  
    [Note: These are minimum number of nodes/cores/memory. Depending on the scale of production environment, more nodes and higher cores/memory will be required.]

## 9.2 Backup and Restore Design

Backup and restore solutions for containerized applications must protect both the cluster as well as the application to enable recovery in the event of failures triggered by ransomware attacks, software data corruption, accidental deletions, or any other cause.

Red Hat OpenShift on IBM Cloud clusters include automatic back-up and recovery of cluster control plane components, namely etcd data. The user is responsible for backing up the application. Application backups must protect application data and metadata included in application-related cluster resources, stateful application’s data stored in persistent storage, and internal images.

The Multi-zone Resiliency pattern for ROKS uses Portworx to backup the application.

Portworx Backup provides Kubernetes data protection platform for the persistent data both in transit and at rest. It is app-aware including stateful apps and provides a simple self-service interface for backup and restore. It can operate at Pod, Namespace or Cluster level. Portworx Backup (PX-Backup) is setup in Management VPC in an administration mutizone cluster. The following is Portworx Backup solution at a high level:

-   Backup Source: Portworx Enterprise (PX-Store) – SDS for stateful application
-   Target: IBM Cloud Object Storage (COS)
-   Portworx has Backup Automation and Scheduling
-   STORK (STorage Orchestrator Runtime for Kubernetes) provides Kubernetes consistent backup of applications (running in workload cluster) across multi-pod in a namespace. STORK is an agent and should be installed on the application cluster before PX-Backup cluster installation. STORK bridges the gap between Portworx Backup running on your management cluster, and the workload cluster that need to be backed up.
-   Portworx Backup uses MongoDB (runs with 3 replicas for high availability) as the datastore (installed as part of the Portworx Backup deployment) for writing the metadata of backup object data.

    ![Portworx Backup Architecture](image/5615e0b9ada438a79285b0847db0db93.png)

# 10. Service Management Design Considerations

To ensure Portworx storage infrastructure's health, performance, and reliability, the Portworx cluster needs to be monitored. For monitoring Portworx, ROKS on IBM Cloud include built-in tools (Prometheus) to manage the health of single cluster instance. For Portworx on Red Hat OpenShift version 4.14 or above, Portworx components need to be configured to work with the OpenShift Prometheus.

For Portworx logging, ROKS on IBM Cloud include built-in tools to manage the health of single cluster instance. Built-in pod logs are not configured with persistent storage and integration with a cloud database is required to back up the logging data and make it highly available.

Note: Portworx upgrades should follow the instructions at <https://docs.portworx.com/portworx-enterprise/operations/operate-kubernetes/upgrade/upgrade-px-ibm.>

## 10.1 Monitoring Design

The OpenShift Container Platform monitoring stack is based on the Prometheus. Portworx cluster can be monitored using Prometheus. For Portworx on Red Hat OpenShift version 4.14 or above, Portworx components need to be configured to work with the OpenShift Prometheus deployment for monitoring, since the native Portworx Prometheus support is not available on these OpenShift versions. To configure the Portworx monitoring solution see <https://docs.portworx.com/portworx-enterprise/install-portworx/monitoring/monitor-portworx-cluster>. [Note: Portworx Monitoring data can also be shown in IBM Cloud Monitoring and Portworx Grafana dashboard.]

## 10.2 Logging Design

Built-in Red Hat OpenShift logging tools are used and can be installed using the cluster logging operator. The worker pool should have at least 4 cores and 32 GB memory and 3 worker nodes to run cluster logging stack. To deploy the cluster logging operator and stack on ROKS on IBM Cloud cluster see [https://cloud.ibm.com/docs/openshift?topic=openshift-health\#oc_logging_operator](https://cloud.ibm.com/docs/openshift?topic=openshift-health#oc_logging_operator). The cluster logging instance needs an IBM Cloud Block Storage storage class.

## 10.3 Alerting Design

Prometheus Alertmanager handles alerts sent from the Prometheus server based on the set rules, which can be configured. When a rule is triggered, Alertmanager sends a notification to the specified receivers. The receivers are configured using an Alertmanager config file. To configure the Portworx alerting solution see <https://docs.portworx.com/portworx-enterprise/install-portworx/monitoring/monitor-portworx-cluster>.

# 12. Architecture Decisions

## 12.1 Compute Architecture Decisions

| **\#**                  | **AD**                                                                             | **Requirement**                                                                                                                   | **Alternative**                                                                   | **Decision**                                     | **Rationale**                                                                                                                                                          |
|-------------------------|------------------------------------------------------------------------------------|-----------------------------------------------------------------------------------------------------------------------------------|-----------------------------------------------------------------------------------|--------------------------------------------------|------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| **Compute: Containers** |                                                                                    |                                                                                                                                   |                                                                                   |                                                  |                                                                                                                                                                        |
| 1                       | ROKS on VPC Worker nodes                                                           | - Provide properly isolated compute resources with adequate compute capacity and storage for the enterprise stateful applications | - VPC VSIs - VPC Dedicated Hosts - VPC Bare Metal - VPC Bare Metal SDS Compliant  | VPC VSIs                                         | Currently available option for ROKS.                                                                                                                                   |
| 2                       | Host Profile for Worker nodes                                                      |                                                                                                                                   | - Data Intensive - RAM Intensive (multiple)                                       |  Based on memory, cores and storage requirements | Select the VSI as per application requirements. Minimum of 8 cores, 8 GB memory is to be reserved for Portworx.                                                        |
| 3                       | Worker Nodes distribution across AZs                                               |                                                                                                                                   | Evenly across three AZs Other                                                     | Evenly across 3 AZs                              | If worker nodes are not spread evenly across the zones or capacity is insufficient in one of the zones, the ROKS controller might fail to schedule all requested pods. |
| 5                       | Worker Nodes Spread across multiple zones                                          | - Provide compute resources that are highly available and can handle increased workload demands                                   | Anti-affinity pod deployment No control                                           | Anti-affinity pod deployment                     |  Protection against zone failure                                                                                                                                       |
| 6                       | Portwork Backup (PX-Backup) Cluster worker nodes (three nodes spread across 3 AZs) | - Provide compute resources that are highly available for PX-Backup                                                               | 8 cores, 16 GB (VSI) Other options                                                | 8 cores, 16 GB (VSI)                             | As per Portworx Backup documentation                                                                                                                                   |

## 12.2 Storage Architecture Decisions

| **\#**      | **AD**                              | **Requirement**                                                                                                                                    | **Alternative**                                                                                        | **Decision**                                                           | **Rationale**                                                                                                                                                                                                                                                                                                                                                                       |
|-------------|-------------------------------------|----------------------------------------------------------------------------------------------------------------------------------------------------|--------------------------------------------------------------------------------------------------------|------------------------------------------------------------------------|-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| **Storage** |                                     |                                                                                                                                                    |                                                                                                        |                                                                        |                                                                                                                                                                                                                                                                                                                                                                                     |
| 1           | Main Storage for Worker nodes       | - Provide highly available storage that meets the application performance requirements.                                                            | - VPC Block storage (5 IOPS) - VPC Block storage (10 IOPS) - VPC File storage  - Cloud Object storage  | - VPC Block storage (10 IOPS) (performance for production environment) | For Portworx across multi zone. Also, Cloud drives allows to dynamically provision the Portworx volumes. Portworx requires raw and unformatted block storage.                                                                                                                                                                                                                       |
| 2           | Containers Software Defined Storage | - Provide highly available storage that  abstracts data storage for worker nodes and aggregates them into a virtual pool.                          | - Portworx  - ODF - GlusterFS                                                                          | Portworx                                                               | For persistent storage across multi zone for stateful containerized apps. Portworx provides multiple data access mode like Read Write Many. Portworx provides persistent volume with high availability and disaster recovery as well data protection for backup and recovery.                                                                                                       |
| 3           | Portworx Architecture               | Provide highly available SDS for generic stateful application                                                                                      | Converged Disaggregated                                                                                | Converged – each worker node has the role of both computer and storage | Each worker node is connected to VPC Block Storage which provides reasonable performance for most workloads. [Note: Depending on the type of workloads, the disaggregated architecture may be suitable.]                                                                                                                                                                            |
| 4           | Logs Storage  (Audit, Operational)  | - Provide highly available storage for logs                                                                                                        | - VPC Block storage  - Cloud Object storage (COS)                                                      | VPC Block storage                                                      | Built-in Red Hat OpenShift monitoring tools are used and can be installed using the cluster logging operator. The cluster logging instance needs an IBM Cloud Block Storage class.                                                                                                                                                                                                  |
| 5           | Backup Storage                      | - Provide highly available storage for backups                                                                                                     | - VPC Block storage  - Cloud Object storage                                                            | COS                                                                    | Cloud Object Storage provides low cost, high available storage for data backups. Cloud Object Storage can be cross-region for data backups to enable recovery in the event of a region outage.                                                                                                                                                                                      |
| 6           | Portworx key-value database (KVDB)  | Provide a KVDB solution for Portworx to store the cluster's state, configuration data, and metadata associated with storage volumes and snapshots. | -Internal KVDB for Portworx - IBM Cloud Database for etcd - Other external KVDB                        | Internal KVDB for Portworx                                             | Portworx recommends using the built-in internal KVDB for your Portworx cluster except when configuring a synchronous DR setup, for example. For setting up external KVDB, see [https://cloud.ibm.com/docs/containers?topic=containers-storage_portworx_kv_store\#portworx-kv-db](https://cloud.ibm.com/docs/containers?topic=containers-storage_portworx_kv_store#portworx-kv-db).  |

## 12.3 Networking Architecture Decisions

## 

| **\#**                                    | **AD**                         | **Requirement**                                                                                                                       | **Alternative**                                                                                      | **Decision**                                   | **Rationale**                                                                                                                                                                                                                                                                |
|-------------------------------------------|--------------------------------|---------------------------------------------------------------------------------------------------------------------------------------|------------------------------------------------------------------------------------------------------|------------------------------------------------|------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| **1. Network Segmentation and Isolation** |                                |                                                                                                                                       |                                                                                                      |                                                |                                                                                                                                                                                                                                                                              |
| 1.1                                       | App Deployment                 | - Deploy workloads in isolated environment and enforce information flow policies. - Provide isolated security zones between app tiers | - Virtual Private Clouds (VPCs) - Subnets - Security Groups (SGs) - ACLs                             | VPCs, subnets, Security Groups (SGs) and ACLs  | VPCs provide secure, virtual networks for apps which are logically isolated from other public cloud tenants. Subnets provide a range of private IP addresses for each app within a zone. Security Groups and ACLs are used as firewalls to limit access to servers and apps. |
| **2. Cloud Native Connectivity**          |                                |                                                                                                                                       |                                                                                                      |                                                |                                                                                                                                                                                                                                                                              |
| 2.1                                       | Connectivity to Cloud Services | - Provide secure connection to Cloud Services                                                                                         | - Virtual Private Endpoints (VPE) - Private Cloud Service endpoints - Public Cloud Service Endpoints | Virtual Private Endpoints (VPE)                | Virtual Private Endpoints enable connectivity to IBM Cloud services using private IP addresses allocated from a VPC subnet.                                                                                                                                                  |
| 2.2                                       | VPC to VPC Connectivity        | - Connect two or more VPCs over private network                                                                                       | - Local Transit Gateway - Global Transit Gateway                                                     | Local Transit Gateway (TGW)                    | The Local Transit Gateway enables connectivity between the Management and Workload VPCs. The Management VPC has Portworx Backup cluster.                                                                                                                                     |
| **3. Load Balancing**                     |                                |                                                                                                                                       |                                                                                                      |                                                |                                                                                                                                                                                                                                                                              |
| 3.1                                       | Cluster Load Balancer          | Distribute compute across zones for high availability.                                                                                | - VPC ALB - VPC NLB                                                                                  | VPC ALB                                        | The VPC ALB distributes traffic among worker nodes across zones and it is automatically provisioned when a multi-zone OpenShift cluster is created.                                                                                                                          |

## 12.4 Security Architecture Decisions

| **\#**                                                         | **AD**                         | **Requirement**                                                                                        | **Alternative**                                                                                   | **Decision**                                   | **Rationale**                                                                                                                                                                                                                                                                        |
|----------------------------------------------------------------|--------------------------------|--------------------------------------------------------------------------------------------------------|---------------------------------------------------------------------------------------------------|------------------------------------------------|--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| **1. Data: Encryption at Rest**                                |                                |                                                                                                        |                                                                                                   |                                                |                                                                                                                                                                                                                                                                                      |
| 1.1                                                            | Encryption Approach            | - Encrypt all data to protect from unauthorized disclosure                                             | - Encryption with provider keys - Encryption with customer-managed keys                           | Encryption with customer-managed keys          | Encrypting data with customer-managed keys is recommended since it provides added security and customer control needed to meet regulatory compliance requirements.                                                                                                                   |
| 1.2                                                            | Data Encryption  (SDS)         | - Encrypt all application data at rest to protect from unauthorized disclosure                         | - Storage encryption with customer-managed keys - App-level encryption with customer-managed keys | Storage Encryption with customer-managed keys  | Portworx Encryption is integrated with IBM Key Protect or Hyper Protect Crypto Services API.                                                                                                                                                                                         |
| 1.3                                                            | Data Encryption  Backups       | - Encrypt all backup data to protect from unauthorized disclosure                                      | Storage encryption with customer-managed keys App-level encryption                                | Storage encryption with customer-managed keys  | Backup data is stored in COS which supports encryption with customer-managed keys by selecting a KMS.                                                                                                                                                                                |
| **2. Data: Encryption in Transit**                             |                                |                                                                                                        |                                                                                                   |                                                |                                                                                                                                                                                                                                                                                      |
| 2.1                                                            | Backup Data                    | - Encrypt all backup data in transit to protect from unauthorized disclosure                           | Application-level encryption with TLS                                                             | Application-level encryption with TLS          | Portworx Backup supports encryption of backups in transit.  The PX-Backup uses TLS to encrypt data in transit to COS.                                                                                                                                                                |
| **3. Data: Key Lifecycle Management & Certificate Management** |                                |                                                                                                        |                                                                                                   |                                                |                                                                                                                                                                                                                                                                                      |
| 3.1                                                            | Key Lifecycle Management & HSM | - Encrypt data at rest and in transit using customer managed keys to protect from unauthorized access  | Key Protect Hyper Protect Crypto Services (HPCS)                                                  | Key Protect                                    | Key Protect is recommended for applications that need to comply with regulations requiring encryption of data with customer-managed keys. Key Protect provides key management services using a shared (multi-tenant) FIPS 140-2 Level 3 certified hardware security modules (HSMs).  |

## 12.4 Resiliency Architecture Decisions

| **\#**                     | **AD**                                                     | **Requirement**                                                                                                                       | **Alternative**                                                                              | **Decision**                         | **Rationale**                                                                                                                                                                                                                                                                                                                                                                    |
|----------------------------|------------------------------------------------------------|---------------------------------------------------------------------------------------------------------------------------------------|----------------------------------------------------------------------------------------------|--------------------------------------|----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| **1. High Availability**   |                                                            |                                                                                                                                       |                                                                                              |                                      |                                                                                                                                                                                                                                                                                                                                                                                  |
|  1.1                       | Workload and Portworx cluster High Availability Deployment | - Ensure availability of resources in the event of outages. - Support SLA targets for application availability.                       | - Single zone, single region - Multi zone, single region - Multi-zone, multi region          | Multi-zone, single region            | **Recommended deployment for Multi-Zone Resiliency Pattern** - Provides protection from zone outage - Supports 99.99% availability for the infrastructure   Recommended approach for: - Core business applications - Enterprise level workloads with stringent resiliency requirements  - Business continuity policies with country boundaries or geo data residence constraints |
| 1.2                        | Portworx Backup cluster                                    | Ensure availability of backup solution in case of workload cluster failures                                                           | -Separate cluster for Portworx Backup -Use workload cluster                                  | Separate cluster for Portworx Backup | Best Practice is to use a separate cluster for Portworx Backup since if the backup solution is installed on workload cluster and in the case the workload cluster goes down so does the backup solution.                                                                                                                                                                         |
| 1.3                        | Portworx Backup cluster High Availability Deployment       | - Ensure availability of Portworx Backup service in the event of outages.                                                             | - Single zone, single region - Multi zone, single region                                     | Multi-zone, single region            | Provides protection from zone outage                                                                                                                                                                                                                                                                                                                                             |
| **2. Backup and Restore**  |                                                            |                                                                                                                                       |                                                                                              |                                      |                                                                                                                                                                                                                                                                                                                                                                                  |
| 2.1                        | Backup Type                                                | Backup and Restore at the application level                                                                                           | -Application backup -VMs backup -Nodes backup -Other                                         | Application backup                   | Kubernetes consistent backup of applications allows for fast and efficient recovery of applications in the cluster.                                                                                                                                                                                                                                                              |
| 2.2                        | Application backup                                         | - Backup applications to enable recovery of applications in the event of unplanned outages                                            | - Portworx Backup - Kasten - Trilio for OpenShift - Others (Velero, KubeDR, Zerto for K8s)   | Portworx Backup                      | Portworx not only support Kubernetes backup and restore, but also DR and local HA. Provides Kubernetes consistent backup of applications across multi-pod in a namespace using STORK (STorage Orchestrator Runtime for Kubernetes).                                                                                                                                              |
| 2.3                        | Backup Automation                                          | - Schedule regular applications backups based on backup policy requirements to enable data recovery in the event of unplanned outages | - Portworx Backup - Kasten - Trilio for OpenShift - Others (Velero, KubeDR, Zerto for K8s)   | Portworx Backup                      | Portworx Backup allows defining backup policies to manage the creation and deletion of backup and provides UI in Portworx Central that has RBAC capabilities.                                                                                                                                                                                                                    |

## 12.5 Service Management Architecture Decisions

| **\#**             | **AD**                                        | **Requirement**                                                                                         | **Alternative**                                                                                          | **Decision**                                              | **Rationale**                                                                                                                          |
|--------------------|-----------------------------------------------|---------------------------------------------------------------------------------------------------------|----------------------------------------------------------------------------------------------------------|-----------------------------------------------------------|----------------------------------------------------------------------------------------------------------------------------------------|
| **1. Monitoring**  |                                               |                                                                                                         |                                                                                                          |                                                           |                                                                                                                                        |
| 1.1                | Operational Monitoring of ROKS cluster        | - Monitor ROKS cluster health                                                                           | - OpenShift Monitoring integrated with IBM Cloud Monitoring - IBM Cloud Monitoring - BYO Monitoring Tool | OpenShift Monitoring integrated with IBM Cloud Monitoring | OpenShift monitoring can also be used to monitor Portworx. For an overall solution, it should be integrated with IBM Cloud Monitoring. |
| 1.2                | Operational Monitoring of Portworx components | - Monitor Portworx components health                                                                    | - OpenShift Monitoring - IBM Cloud Monitoring - BYO Monitoring Tool                                      | OpenShift Monitoring                                      | Portworx monitoring requires OpenShift monitoring (Prometheus)                                                                         |
| **2. Logging**     |                                               |                                                                                                         |                                                                                                          |                                                           |                                                                                                                                        |
| 2.1                | Logs Monitoring for ROKS                      | - Monitor operational logs to detect issues that might impact the availability of ROKS                  | - OpenShift Logging integrated with IBM Cloud Logging - IBM Cloud Logging - BYO Logging Tool             | OpenShift Logging integrated with IBM Cloud Logging       | OpenShift logging can also be used for Portworx. For an overall solution, it should be integrated with IBM Cloud Logging.              |
| 2.2                | Logs Monitoring for Portworx components       |                                                                                                         | - OpenShift Logging - IBM Cloud Logging - BYO Logging Tool                                               | OpenShift Logging                                         | On IBM Cloud, Portworx logging requires OpenShift logging tools                                                                        |
| **3. Alerting**    |                                               |                                                                                                         |                                                                                                          |                                                           |                                                                                                                                        |
| 3.1                | Operational alerts for ROKS cluster           | - Provide a mechanism to identify and send notifications about operational issues found in ROKS cluster | - Prometheus Alertmanager - IBM Cloud Monitoring +  IBM Cloud Logging + Event Notifications              | Prometheus Alertmanager                                   | Prometheus Alertmanager can be used for Portworx components                                                                            |
| 3.2                | Operational alerts for Portworx components    | - Provide a mechanism to identify and send notifications about issues found in Portworx components      | - Prometheus Alertmanager - IBM Cloud Activity Tracker + IBM Monitoring +  Event Notifications           | Prometheus Alertmanager                                   | Portworx alerting solution is based on Prometheus Alertmanager                                                                         |

# 13. References

-   **VPC Resiliency on IBM Cloud White Paper**
-   Cluster Autoscaler: https://cloud.ibm.com/docs/openshift?topic=openshift-cluster-scaling-classic-vpc&interface=ui
-   ALB multi-zone configuration:

    [https://cloud.ibm.com/docs/vpc?topic=vpc-load-balancers-about&interface=api\#horizontal-scaling](https://cloud.ibm.com/docs/vpc?topic=vpc-load-balancers-about&interface=api#horizontal-scaling)

-   IBM Cloud SLA:

    [https://www.ibm.com/support/customer/csol/terms/?id=i126-9268&lc=en\#detail-document](https://www.ibm.com/support/customer/csol/terms/?id=i126-9268&lc=en#detail-document)

-   Portworx Enterprise: https://cloud.ibm.com/docs/openshift?topic=openshift-storage_portworx_plan
-   Portworx Backup: https://cloud.ibm.com/docs/openshift?topic=openshift-storage_portworx_backup\#px-backup-and-restore
-   Zones and Regions: For an understanding of Zones and Regions in IBM Cloud, please see: <https://www.ibm.com/docs/en/cloud-orchestrator/2.5.0.9?topic=security-regions-availability-zones-quota>

# 14. Glossary of Terms/Abbreviations

# **No index entries found.**
