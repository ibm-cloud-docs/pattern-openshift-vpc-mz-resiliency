---

copyright:
  years: 2024
lastupdated: "2024-07-30"

subcollection: pattern-openshift-vpc-mz-resiliency

keywords:

authors:
- name: Joe Kiernan

version: 1.0

deployment-url:

docs: /docs/pattern-openshift-vpc-mz-resiliency

content-type: reference-architecture

---

{{site.data.keyword.attribute-definition-list}}

# Red Hat OpenShift on VPC Architecture 
{: #openshift-on-vpc}
{: toc-content-type="reference-architecture"}
{: toc-version="1.0"}

- Red Hat OpenShift is deployed on VPC servers across three availability zones within a region.
- Compatible and recommended nodes that are available for Red Hat OpenShift on IBM Cloud – Virtual Servers Instances Shared are used for worker nodes to run stateful application in production environment. 

    ![](image/ROKScluster.svg)Ref: IBM Cloud Docs

-   High-performance auxiliary storage (10 IOPS Block) is used to and attached to worker nodes. Portworx Enterprise (an SDS – Software Defined Storage) is setup in the cluster across three zones by using a Converged architecture (compute and storage in the same node). Built-in internal key-value database (KVDB) is used for Portworx cluster.

![Portworx deployment architecture hyperconverged](image/Cluster.svg)Ref: Portworx Docs

- Use the Portworx SDS for any databases that might be required for the stateful applications.
- In the Red Hat OpenShift multizone cluster, the worker nodes in the worker pools are distributed across multiple zones within one region. Red Hat OpenShift multizone clusters are designed to evenly schedule pods across worker nodes and zones to ensure availability and recovery from failure. If worker nodes are not spread evenly across the zones or capacity is insufficient in one of the zones, the Red Hat OpenShift controller might fail to schedule all requested pods.
- Application Load Balancer (ALB) for VPC with multizone support is used to route traffic to the containerized application.
- Containerized stateful applications are deployed within the workload cluster and VPC. Management tools, such as backup tools, are deployed in the management cluster and VPC. A local Transit Gateway allows traffic between the management and workload VPCs.
- Worker nodes in worker pools are placed in separate subnets within each availability zone.
- Security groups and access control lists (ACLs) are used as firewalls to limit access to worker node servers for operational purposes and control network traffic.
- All storage is encrypted at rest by using storage encryption with customer-provided keys that are managed by Key Protect.
- Data is encrypted in transit by using TLS encryption. Portworx Backup is used to enable backup and recovery of containerized applications that are deployed in the workload cluster. The Portworx Backup source is the PX-Store (SDS) configured for the workload cluster. The Portwork Backup target is IBM Cloud Object Storage. Portworx Backup supports encryption of backups in transit. IBM Cloud Object Storage supports encryption with Key Management Services. Portworx Backup is setup in management VPC in a management or administration cluster that is also spread across three availability zones.
-   The OpenShift Container Platform monitoring stack is based on the Prometheus, which also can be used to monitor the Portworx cluster
-   Built-in Red Hat OpenShift logging tools are used for Portworx logging.

## Architecture diagram
{: #architecture-diagram}

![Multi-Zone Resiliency for ROKS Solution Architecture](image/Architectureoverview.svg)

[Multi-Zone Resiliency for ROKS Solution Architecture]

## Solution components

| Category              | Solution components                                                                                                                   | How it is used in the solution                                                                                                            |
|-----------------------|---------------------------------------------------------------------------------------------------------------------------------------|---------------------------------------------------------------------------------------------------------------------------------------|
| Compute               | Red Hat OpenShift cluster                                                                                                             | Kubernetes cluster for containerized applications and backup tools                                                                    |
| Storage               | VPC block storage                                                                                                                     | For worker nodes storage and Portworx SDS                                                                                               |
|                       | Portworx Enterprise                                                                                                                   | Portworx enterprise provides highly available unified storage across multiple zones for stateful applications                         |
|                       | [IBM Cloud Object Storage](/docs/cloud-object-storage?topic=cloud-object-storage-about-cloud-object-storage)  | Backups, logs (application, operational, and audit logs)                                                                               |
|                       | Portworx Internal KVDB                                                                                                                | To store the cluster's state, configuration data, and metadata associated with storage volumes and snapshots.                         |
| Networking            | [VPC Virtual Private Network (VPN) Client](/docs/iaas-vpn?topic=iaas-vpn-getting-started)                        | Remote access to manage resources in a private network                                                                                  |
|                       | [Virtual Private Clouds (VPCs), Subnets, Security Groups (SGs), ACLs](/docs/vpc?topic=vpc-getting-started)       | VPCs for workload isolation Subnets, SGs, and ACLs for restricted access to stateful application components                           |
|                       | [Local Transit Gateway (TGW)](/docs/transit-gateway?topic=transit-gateway-getting-started)                       | Connectivity between workload and management VPCs                                                                                     |
|                       | [Virtual Private Gateway and Virtual Private Endpoint (VPE)](/docs/vpc?topic=vpc-about-vpe)                        | Private network access to Cloud Services, for example, Key Protect.                                                                |
|                       | [VPC Application Load Balancer](/docs/vpc?topic=vpc-load-balancers)                                              | Application Load Balancing for containerized apps                                                                                     |
|                       |                                                                                                                                       |                                                                                                                                       |
|                       | [Cloud Internet Services (CIS)](/docs/cis?topic=cis-getting-started)                                             | Public DNS resolution                                                                                                                 |
|                       | [DNS Services](/docs/dns-svcs?topic=dns-svcs-about-dns-services)                                                 | Private DNS resolution                                                                                                                |
| Security              | [Access management](/docs/account?topic=account-cloudaccess)                                                                   | IBM Cloud Identity & Access Management                                                                                                |
|                       | [Cloud Internet Services (CIS)](/docs/cis?topic=cis-getting-started)                                             | DDoS protection and Web App Firewall                                                                                                  |
|                       | [Key Protect](/docs/key-protect?topic=key-protect-about)                                                         | Key Management Service                                                                                                                |
|                       | [Secrets Manager](/catalog/services/secrets-manager){: external}                                                             | Certificate and Secrets Management                                                                                                    |
|                       |                                                                                                                                       |                                                                                                                                       |
| Service Management    | Red Hat OpenShift logging and monitoring tools                                                                                                  | For Red Hat OpenShift cluster, app logs, cluster metrics, app monitoring, Portworx Enterprise, and Portworx Backup logging and monitoring |
| Resiliency and capacity | [Cross-Region Cloud Object Storage Buckets](/docs/cloud-object-storage/basics?topic=cloud-object-storage-endpoints#endpoints-geo) | Backup storage (target for PX-Backup)                                                                                                 |
|                       | Cluster autoscaler                                                                                                                    | To adjust compute and storage capacity based on load changes                                                                          |
|                       | VPC VSI (worker nodes) evenly spread across three zones, Portworx Enterprise (SDS) across multiple zones in a region                  | For Stateful Containerized apps high availability deployment                                                                          |
|                       | Portworx Backup (PX-Backup)                                                                                                           | For Backup and restore of stateful applications data                                                                                  |
{: caption="Table 1. Red Hat OpenShift for VPC components" caption-side="bottom"}


## Design scope
{: #design-scope}

Following the [Architecture Framework](/docs/architecture-framework?topic=architecture-framework-taxonomy), pattern-name covers design considerations and architecture decisions for the following aspects and domains:

- **Compute:** Bare Metal and Virtual infrastructure

- **Storage:** Primary, Backup, Archive, and Migration

- **Networking:** Enterprise Connectivity, Edge Gateways, Segmentation and Isolation, Cloud Native Connectivity and Load Balancing

- **Security:** Data, Identity and Access Management, Infrastructure and Endpoint, Threat Detection and Response

- **Resiliency:** Backup and Restore, Disaster Recovery, High Availability

- **Service Management:** Monitoring, Logging, Alerting, Management/Orchestration

The Architecture Framework, described in [Introduction to the Architecture Framework](/docs/architecture-framework?topic=architecture-framework-intro), provides a consistent approach to design cloud solutions by addressing requirements across a pre-defined set of aspects and domains, which are technology-agnostic architectural areas that need to be considered for any enterprise solution. It can be used as a guide to make the necessary design and component choices to ensure that you have considered applicable requirements for each aspect and domain. After you have identified the applicable requirements and domains that are in scope, you can evaluate and select the best fit for purpose components for your enterprise cloud solution.


## Requirements 
{: #requirements}

The following represents a typical set of requirements for enterprise-ready ROKS deployed in a public cloud.

| Aspect         | Requirements                                                                                                                                             |
|--------------------|--------------------------------------------------------------------------------------------------------------------------------------------------------------|
| Compute            | Provide a platform for containerized applications and management tools with adequate compute capacity                                                          |
| Storage            | Provide storage that meets the stateful applications and database requirements.                                                                              |
| Networking         | Deploy workloads in an isolated environment and enforce information flow policies.                                                                              |
|                    |                                                                                                                                                              |
|                    | Distribute incoming application requests across available compute resources.                                                                                 |
|                    |                                                                                                                                                              |
| Security           | Ensure that all operation actions are run securely.                                                                                                          |
|                    |                                                                                                                                                              |
|                    | Ensure data encryption at rest and in transit for the storage layer.                                                                                         |
|                    | Encrypt all backup data to protect it from unauthorized disclosure.                                                                                             |
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
|                    | Provide a mechanism to identify and send notifications about issues found in audit logs.
{: caption="Table 2. Red Hat OpenShift for VPC requirements" caption-side="bottom"}


As mentioned earlier, the Architecture Framework is used to guide and determine the applicable aspects and domains for which architecture decisions need to be made. The following sections contain the considerations, and architecture decisions for the aspects and domains that are in play in this solution pattern.
