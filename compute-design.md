---

copyright:
  years: 2024
lastupdated: "2024-07-26"

subcollection: pattern-openshift-vpc-mz-resiliency

keywords:

---

{{site.data.keyword.attribute-definition-list}}

# Compute design 
{: #compute-design}

The Multi-zone Resiliency pattern for ROKS includes compute options that are highly available, properly isolated, provide the defined capacity for the applications, and can handle increased workload demands.

Provision VPC Virtual Server Infrastructure (VSI) for the worker nodes as follows:

-   VPC Virtual Server Infrastructure (VSI): select compute and memory profiles, based on capacity and performance requirements. Spread worker nodes hosts evenly across three availability zones (less than 10ms latency required for synchronous replication). 8 cores, 8 GB memory is to be reserved for Portworx, and 10 Gbps bandwidth is recommended.
-   Cluster-Autoscaler: With the cluster-autoscaler add-on, the worker pools in ROKS on IBM Cloud VPC cluster can be scaled automatically to increase or decrease the number of worker nodes in the worker pool based on the sizing needs of the scheduled workloads.
-   Multi-zone deployment: deploy worker nodes across multiple availability zones to protect the application from zone outages. Use a VPC Application Load Balancer (ALB) to front end the services.
