---

copyright:
  years: 2024
lastupdated: "2024-07-26"

subcollection: pattern-openshift-vpc-mz-resiliency

keywords:

---

# Architecture decisions for service management
{: #service}

The following sections summarize the architecture decisions for service management for the Red Hat OpenShift multi-zone resiliency pattern.


| **\#**             | **AD**                                        | **Requirement**                                                                                         | **Alternative**                                                                                          | **Decision**                                              | **Rationale**                                                                                                                          |
|--------------------|-----------------------------------------------|---------------------------------------------------------------------------------------------------------|----------------------------------------------------------------------------------------------------------|-----------------------------------------------------------|----------------------------------------------------------------------------------------------------------------------------------------|
| **1. Monitoring**  |                                               |                                                                                                         |                                                                                                          |                                                           |                                                                                                                                        |
| 1.1                | Operational Monitoring of ROKS cluster        | - Monitor ROKS cluster health                                                                           | - OpenShift Monitoring integrated with IBM Cloud Monitoring\n - IBM Cloud Monitoring\n - BYO Monitoring Tool | OpenShift Monitoring integrated with IBM Cloud Monitoring | OpenShift monitoring can also be used to monitor Portworx. For an overall solution, it should be integrated with IBM Cloud Monitoring. |
| 1.2                | Operational Monitoring of Portworx components | - Monitor Portworx components health                                                                    | - OpenShift Monitoring\n - IBM Cloud Monitoring\n - BYO Monitoring Tool                                      | OpenShift Monitoring                                      | Portworx monitoring requires OpenShift monitoring (Prometheus)                                                                         |
| **2. Logging**     |                                               |                                                                                                         |                                                                                                          |                                                           |                                                                                                                                        |
| 2.1                | Logs Monitoring for ROKS                      | - Monitor operational logs to detect issues that might impact the availability of ROKS                  | - OpenShift Logging integrated with IBM Cloud Logging\n - IBM Cloud Logging\n - BYO Logging Tool             | OpenShift Logging integrated with IBM Cloud Logging       | OpenShift logging can also be used for Portworx. For an overall solution, it should be integrated with IBM Cloud Logging.              |
| 2.2                | Logs Monitoring for Portworx components       |                                                                                                         | - OpenShift Logging\n - IBM Cloud Logging\n - BYO Logging Tool                                               | OpenShift Logging                                         | On IBM Cloud, Portworx logging requires OpenShift logging tools                                                                        |
| **3. Alerting**    |                                               |                                                                                                         |                                                                                                          |                                                           |                                                                                                                                        |
| 3.1                | Operational alerts for ROKS cluster           | - Provide a mechanism to identify and send notifications about operational issues found in ROKS cluster | - Prometheus Alertmanager\n - IBM Cloud Monitoring +  IBM Cloud Logging + Event Notifications              | Prometheus Alertmanager                                   | Prometheus Alertmanager can be used for Portworx components                                                                            |
| 3.2                | Operational alerts for Portworx components    | - Provide a mechanism to identify and send notifications about issues found in Portworx components      | - Prometheus Alertmanager\n - IBM Cloud Activity Tracker + IBM Monitoring +  Event Notifications           | Prometheus Alertmanager                                   | Portworx alerting solution is based on Prometheus Alertmanager                                                                         |
{: caption="Table 5. Architecture decisions for management/Orchestration" caption-side="bottom"}
