---

copyright:
  years: 2024
lastupdated: "2024-07-26"

subcollection: pattern-openshift-vpc-mz-resiliency

keywords:

---

{{site.data.keyword.attribute-definition-list}}

# Architecture decisions for networking
{: #networking-architecture}



The following tables summarize the networking architecture decisions for the Red Hat OpenShift service on IBM Cloud multi-zone resiliency pattern.

## 

| **\#**                                    | **AD**                         | **Requirement**                                                                                                                       | **Alternative**                                                                                      | **Decision**                                   | **Rationale**                                                                                                                                                                                                                                                                |
|-------------------------------------------|--------------------------------|---------------------------------------------------------------------------------------------------------------------------------------|------------------------------------------------------------------------------------------------------|------------------------------------------------|------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| **1. Network Segmentation and Isolation** |                                |                                                                                                                                       |                                                                                                      |                                                |                                                                                                                                                                                                                                                                              |
| 1.1                                       | App Deployment                 | -Deploy workloads in isolated environment and enforce information flow policies.\n -Provide isolated security zones between app tiers | -Virtual Private Clouds (VPCs)\n -Subnets\n -Security Groups (SGs)\n -ACLs                             | VPCs, subnets, Security Groups (SGs) and ACLs  | VPCs provide secure, virtual networks for apps which are logically isolated from other public cloud tenants. Subnets provide a range of private IP addresses for each app within a zone. Security Groups and ACLs are used as firewalls to limit access to servers and apps. |
| **2. Cloud Native Connectivity**          |                                |                                                                                                                                       |                                                                                                      |                                                |                                                                                                                                                                                                                                                                              |
| 2.1                                       | Connectivity to Cloud Services | -Provide secure connection to Cloud Services                                                                                         | -Virtual Private Endpoints (VPE)\n -Private Cloud Service endpoints\n -Public Cloud Service Endpoints | Virtual Private Endpoints (VPE)                | Virtual Private Endpoints enable connectivity to IBM Cloud services using private IP addresses allocated from a VPC subnet.                                                                                                                                                  |
| 2.2                                       | VPC to VPC Connectivity        | -Connect two or more VPCs over private network                                                                                       | -Local Transit Gateway\n -Global Transit Gateway                                                     | Local Transit Gateway (TGW)                    | The Local Transit Gateway enables connectivity between the Management and Workload VPCs. The Management VPC has Portworx Backup cluster.                                                                                                                                     |
| **3. Load Balancing**                     |                                |                                                                                                                                       |                                                                                                      |                                                |                                                                                                                                                                                                                                                                              |
| 3.1                                       | Cluster Load Balancer          | Distribute compute across zones for high availability.                                                                                | -VPC ALB\n -VPC NLB                                                                                  | VPC ALB                                        | The VPC ALB distributes traffic among worker nodes across zones and it is automatically provisioned when a multi-zone OpenShift cluster is created.                                                                                                                          |
{: caption="Table 5. Architecture decisions for networking" caption-side="bottom"}
