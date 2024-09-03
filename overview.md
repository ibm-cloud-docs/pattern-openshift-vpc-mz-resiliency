---

copyright:
  years: 2024
lastupdated: "2024-08-21"

subcollection: pattern-openshift-vpc-mz-resiliency

keywords:

---

{{site.data.keyword.attribute-definition-list}}

# Overview
{: #overview}

The Red Hat OpenShift on VPC multizone resiliency pattern is intended to provide an {{site.data.keyword.IBM}} solution design for deployment on {{site.data.keyword.Bluemix_short}}.

This document focuses on using cloud platform capabilities to design a resilient Red Hat OpenShift on Virtual Private Cloud solution.

## Pattern objectives
{: #vpc-resiliency-objectives}

The objective of this pattern is to provide a resilient solution design for Red Hat OpenShift Kubernetes Service on a Virtual Private Cloud (VPC) architecture deployment, on {{site.data.keyword.Bluemix_notm}}, that meets high availability requirements for enterprise workloads that require persistent storage. This pattern is intended to:

- Accelerate and simplify solution design by providing a standard {{site.data.keyword.Bluemix_notm}} deployment architecture reference following the [IBM Architecture Design Framework](/docs/architecture-framework).
- Provide a prescriptive, end-2-end enterprise-class solution design, with diagrams, component architecture decisions along with rationale for cloud component selection to meet enterprise requirements.
- Help ensure that requirements can be met from a performance, system availability, and security perspective.

##  Pattern details
{: #vpc-resiliency-details}

The multizone resiliency pattern for {{site.data.keyword.openshiftshort}} deploys containerized enterprise workloads that require persistent storage on VPC, using compute, storage, and network cloud resources as well as other cloud services provisioned across multiple availability zones within a single region.

The multizone Red Hat OpenShift cluster pattern provides 99.99% infrastructure availability. For high availability of stateful application, close coordination between application teams, infrastructure teams (including availability of storage), and database teams is essential to design highly available stateful applications on a multi-zone Red Hat OpenShift cluster.

This pattern uses a multi-zone workload cluster that is configured with Portworx-Store to provide persistent storage for databases and other stateful application components and a Portworx-Backup cluster for application data backup and recovery.

Deploying Red Hat Openshift cluster across three availability zones is the recommended option for a highly available cluster to be used for highly available stateful applications. {{site.data.keyword.Bluemix_notm}}-managed control plane nodes are automatically distributed across availability zones on {{site.data.keyword.Bluemix_notm}}. The network communication across {{site.data.keyword.Bluemix_notm}} availability zones has low enough latency to satisfy Red Hat OpenShift etcd and Portworx requirements. Portworx provides a robust data service platform for persistent storage with replication and high availability features across multiple availability zones to run stateful containerized applications. This pattern is not to be used across {{site.data.keyword.Bluemix_notm}} regions, which have higher latency for region-to-region network communication.

The multizone resiliency pattern for {{site.data.keyword.openshiftshort}} can be used to support business continuity policies or regulatory requirements with country boundaries or geo data residence constraints. It does not support out-of-region disaster recovery. 

To address disaster recovery policies or business continuity policies with geo or distance compliance requirements, see [Cross-Region Resiliency Pattern]().
{: note}
