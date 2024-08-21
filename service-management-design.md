---

copyright:
  years: 2024
lastupdated: "2024-08-21"

subcollection: pattern-openshift-vpc-mz-resiliency

keywords:

---

{{site.data.keyword.attribute-definition-list}}

# Service management design considerations
{: #service-design}

To ensure Portworx storage infrastructure's health, performance, and reliability, the Portworx cluster needs to be monitored. For monitoring Portworx, Red Hat OpenShift on {{site.data.keyword.Bluemix_notm}} include built-in tools like Prometheus to manage the health of a single cluster instance. For Portworx on Red Hat OpenShift version 4.14 or higher, Portworx components need to be configured to work with the Red Hat OpenShift Prometheus.

For Portworx logging, Red Hat OpenShift on {{site.data.keyword.Bluemix_notm}} includes built-in tools to manage the health of a single cluster instance. Built-in pod logs are not configured with persistent storage and integration with a cloud database is required to back up the logging data and make it highly available.

Portworx upgrades must follow the instructions that are outlined in the documentation. For more information, see [Upgrade Portworx on IBM Cloud via Helm](https://docs.portworx.com/portworx-enterprise/operations/operate-kubernetes/upgrade/upgrade-px-ibm){: external}.
 {: note}

## Monitoring design
{: #monitoring-design}

The Red Hat OpenShift container platform monitoring stack is based on Prometheus. Portworx cluster can be monitored by using Prometheus. For Portworx on Red Hat OpenShift version 4.14 or higher, Portworx components need to be configured to work with the Red Hat OpenShift Prometheus deployment for monitoring, since the native Portworx Prometheus support is not available on these Red Hat OpenShift versions. To configure the Portworx monitoring solution, see [Install Portworx](https://docs.portworx.com/portworx-enterprise/platform/openshift/ocp-ibm-cloud/ocp-on-ibm-cloud){: external}. 

Portworx monitoring data can be found in the {{site.data.keyword.Bluemix_notm}} Monitoring and [Portworx Grafana dashboard](https://docs.portworx.com/portworx-enterprise/platform/openshift/ocp-ibm-cloud/set-ocp-prometheus#configure-grafana){: external}.
{: note}

## Logging design
{: #logging-design}

Built-in Red Hat OpenShift logging tools are used to and can be installed by using the cluster logging operator. The worker pool should have at least 4 cores, 32 GB memory, 3 worker nodes to run cluster logging stack. To deploy the cluster logging operator and stack on Red Hat OpenShift on {{site.data.keyword.Bluemix_notm}} cluster, see [loggin operator](https://cloud.ibm.com/docs/openshift?topic=openshift-health#oc_logging_operator). The cluster logging instance needs an {{site.data.keyword.Bluemix_notm}} Block Storage storage class.

## Alerting design
{: #alerting-design}

Prometheus AlertManager handles alerts that are sent from the Prometheus server based on the set rules, which can be configured. When a rule is triggered, AlertManager sends a notification to the specified receivers. The receivers are configured by using an AlertManager config file. To configure the Portworx alerting solution see, [Alerting with Portworx](https://docs.portworx.com/portworx-enterprise/operations/operate-other/monitoring/alerting){: external}.
