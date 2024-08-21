---

copyright:
  years: 2024
lastupdated: "2024-08-21"

subcollection: pattern-openshift-vpc-mz-resiliency

keywords:

---

{{site.data.keyword.attribute-definition-list}}

# Architecture decisions for networking
{: #networking-architecture}

The following tables summarize the networking architecture decisions for the Red Hat OpenShift service on {{site.data.keyword.Bluemix_notm}} multi-zone resiliency pattern.

## Architecture decisions for network segmentation and isolation
{: #network-segmentation-isolation}

The following are architecture decisions for network segmentation and isloation for this design.

| Architecture decision | Requirement | Alternative | Decision | Rationale |
| -------------- | -------------- | -------------- | -------------- | -------------- |
| App deployment | - Deploy workloads in isolated environment and enforce information flow policies. \n -Provide isolated security zones between app tiers | - Virtual Private Clouds (VPCs) \n -Subnets \n - Security Groups (SGs) \n - ACLs | VPCs, subnets, Security Groups (SGs) and ACLs | VPCs provide secure, virtual networks for apps which are logically isolated from other public cloud tenants. Subnets provide a range of private IP addresses for each app within a zone. Security Groups and ACLs are used as firewalls to limit access to servers and apps. |
{: caption="Table 6. Architecture decisions for network segmentation and isolation." caption-side="bottom"}

## Architecture decisions for cloud native connectivity
{: #cloud-native-connectivity}

| Architecture decision | Requirement | Alternative | Decision | Rationale |
| -------------- | -------------- | -------------- | -------------- | -------------- |
| Connectivity to cloud services | - Provide secure connection to cloud services |  - Virtual Private endpoints (VPE) \n - Private Cloud Service endpoints \n - Public Cloud Service endpoints | Virtual Private Endpoints (VPE) | Virtual Private Endpoints enable connectivity to {{site.data.keyword.Bluemix_notm}} services using private IP addresses allocated from a VPC subnet. |
| VPC to VPC Connectivity | - Connect two or more VPCs over private network |  - Local Transit Gateway \n - Global Transit Gateway | Local Transit Gateway (TGW) | The Local Transit Gateway enables connectivity between the management and workload VPCs. The Management VPC has a Portworx Backup cluster. |
{: caption="Table 7. Architecture decisions for cloud native connectivity." caption-side="bottom"}

## Architecture decisions for load balancing
{: #load-balancing-arch-decision}

| Architecture decision | Requirement | Alternative | Decision | Rationale |
| -------------- | -------------- | -------------- | -------------- | -------------- |
| Cluster load balancer | Distribute compute across zones for high availability. | - VPC Application Load Balancer (ALB) \n - VPC Network Load Balancer | VPC ALB | The VPC ALB distributes traffic among worker nodes across zones and it is automatically provisioned when a multi-zone OpenShift cluster is created. |
{: caption="Table 8. Architecture decisions for load balancing." caption-side="bottom"}

