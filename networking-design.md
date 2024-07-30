---

copyright:
  years: 2024
lastupdated: "2024-07-30"

subcollection: pattern-openshift-vpc-mz-resiliency

keywords:

---

{{site.data.keyword.attribute-definition-list}}

# Network design
{: #network-design}

The multi-zone resiliency pattern for Red Hat OpenShift uses {{site.data.keyword.Bluemix_notm}} VPC infrastructure and network services to segment the management and application workloads and support the application deployment across multiple availability zones.

- Deploy the Red Hat OpenShift within a Virtual Private Cloud (VPC) provisioned across multiple availability zones within a region to provide workload isolation within the public cloud.
- Place each worker node in a separate subnet in each availability zone. lLess than 10ms latency is required for synchronous replication. Use security groups and access control lists (ACLs) as firewalls to limit access to server instances for operational purposes and to control network traffic.
- Use VPC Application Load Balancers (ALB) to distribute incoming requests to stateful containerized applications.
