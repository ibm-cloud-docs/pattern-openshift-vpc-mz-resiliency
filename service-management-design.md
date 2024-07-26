---

copyright:
  years: 2024
lastupdated: "2024-07-26"

subcollection: pattern-openshift-vpc-mz-resiliency

keywords:

---

{{site.data.keyword.attribute-definition-list}}

# Service Management Design Considerations
{: #service-design}

To ensure Portworx storage infrastructure's health, performance, and reliability, the Portworx cluster needs to be monitored. For monitoring Portworx, ROKS on IBM Cloud include built-in tools (Prometheus) to manage the health of single cluster instance. For Portworx on Red Hat OpenShift version 4.14 or above, Portworx components need to be configured to work with the OpenShift Prometheus.

For Portworx logging, ROKS on IBM Cloud include built-in tools to manage the health of single cluster instance. Built-in pod logs are not configured with persistent storage and integration with a cloud database is required to back up the logging data and make it highly available.

> [!NOTE]  
> Portworx upgrades should follow the instructions at <https://docs.portworx.com/portworx-enterprise/operations/operate-kubernetes/upgrade/upgrade-px-ibm.>

## Monitoring Design

The OpenShift Container Platform monitoring stack is based on the Prometheus. Portworx cluster can be monitored using Prometheus. For Portworx on Red Hat OpenShift version 4.14 or above, Portworx components need to be configured to work with the OpenShift Prometheus deployment for monitoring, since the native Portworx Prometheus support is not available on these OpenShift versions. To configure the Portworx monitoring solution [see here](https://docs.portworx.com/portworx-enterprise/install-portworx/monitoring/monitor-portworx-cluster). 

> [!Note]
> Portworx Monitoring data can also be shown in IBM Cloud Monitoring and Portworx Grafana dashboard.

## Logging Design

Built-in Red Hat OpenShift logging tools are used and can be installed using the cluster logging operator. The worker pool should have at least 4 cores and 32 GB memory and 3 worker nodes to run cluster logging stack. To deploy the cluster logging operator and stack on ROKS on IBM Cloud cluster [see here](https://cloud.ibm.com/docs/openshift?topic=openshift-health\#oc_logging_operator). The cluster logging instance needs an IBM Cloud Block Storage storage class.

## Alerting Design

Prometheus Alertmanager handles alerts sent from the Prometheus server based on the set rules, which can be configured. When a rule is triggered, Alertmanager sends a notification to the specified receivers. The receivers are configured using an Alertmanager config file. To configure the Portworx alerting solution [see here](https://docs.portworx.com/portworx-enterprise/install-portworx/monitoring/monitor-portworx-cluster).
