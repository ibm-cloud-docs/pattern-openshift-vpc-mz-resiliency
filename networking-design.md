---

copyright:
  years: 2024
lastupdated: "2024-07-23"

subcollection: pattern-openshift-vpc-mz-resiliency

keywords:

---

{{site.data.keyword.attribute-definition-list}}

# Network design
{: #network-design}

The Multi-zone Resiliency pattern for ROKS leverages IBM Cloud VPC infrastructure and network services to segment the management and application workloads and support the application deployment across multiple availability zones.

-   Deploy the ROKS within a Virtual Private Cloud (VPC) provisioned across multiple availability zones within a region to provide workload isolation within the public cloud.
-   Place each worker node in a separate subnet in each availability zone (less than 10ms latency required for synchronous replication). Use security groups and ACLs as firewalls to limit access to server instances for operational purposes and to control network traffic.
-   Use VPC Application Load Balancers (ALB) to distribute incoming requests to stateful containerized applications.
