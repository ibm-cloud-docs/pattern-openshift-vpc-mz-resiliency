---

copyright:
  years: 2024
lastupdated: "2024-09-02"

subcollection: pattern-openshift-vpc-mz-resiliency

keywords:

---

# Architecture decisions for service management
{: #service}

The following sections summarize the architecture decisions for service management for the Red Hat OpenShift multi-zone resiliency pattern.


## Architecture decisions for monitoring 
{: #arch-monitoring }

The following are architecture decisions for monitoring for this design.

| Architecture decision | Requirement | Alternative | Decision | Rationale |
| -------------- | -------------- | -------------- | -------------- | -------------- |
| Operational monitoring of Red Hat OpenShift cluster        | Monitor Red Hat OpenShift cluster health                                                                           | - Red Hat OpenShift monitoring integrated with {{site.data.keyword.Bluemix_notm}} monitoring \n - {{site.data.keyword.Bluemix_notm}} Monitoring\n - BYO monitoring tool | Red Hat OpenShift monitoring integrated with {{site.data.keyword.Bluemix_notm}} monitoring | Red Hat OpenShift monitoring can also be used to monitor Portworx. For an overall solution, it should be integrated with {{site.data.keyword.Bluemix_notm}} monitoring. |
| Operational monitoring of Portworx components | - Monitor Portworx components health                                                                    | - Red Hat OpenShift monitoring \n - {{site.data.keyword.Bluemix_notm}} monitoring \n - BYO monitoring tool                                      | Red Hat OpenShift monitoring                                      | Portworx monitoring requires Red Hat OpenShift monitoring for Prometheus                                                                         |
{: caption="Table 1. Architecture decisions for service management monitoring" caption-side="bottom"}

## Architecture decisions for logging 
{: #arch-logging}

The following are architecture decisions for logging for this design.

| Architecture decision | Requirement | Alternative | Decision | Rationale |
| -------------- | -------------- | -------------- | -------------- | -------------- |
| Logs monitoring for Red Hat OpenShift                      | Monitor operational logs to detect issues that might impact the availability of Red Hat OpenShift                  | -  Red Hat OpenShift logging integrated with {{site.data.keyword.Bluemix_notm}} logging \n - {{site.data.keyword.Bluemix_notm}} logging \n - BYO logging tool             | Red Hat OpenShift logging integrated with {{site.data.keyword.Bluemix_notm}} logging       | Red Hat OpenShift logging can also be used for Portworx. For an overall solution, it should be integrated with {{site.data.keyword.Bluemix_notm}} logging.              |
| Logs monitoring for Portworx components       |                                                                                                         | - Red Hat OpenShift logging \n - {{site.data.keyword.Bluemix_notm}} logging \n - BYO logging tool                                               | Red Hat OpenShift logging                                         | On {{site.data.keyword.Bluemix_notm}}, Portworx logging requires Red Hat OpenShift logging tools                                                                        |
{: caption="Table 2. Architecture decisions for service management logging" caption-side="bottom"}

## Architecture decisions for alerting 
{: #arch-alerting }

The following are architecture decisions for alerting for this design.

| Architecture decision | Requirement | Alternative | Decision | Rationale |
| -------------- | -------------- | -------------- | -------------- | -------------- |
 | Operational alerts for Red Hat OpenShift cluster           | Provide a mechanism to identify and send notifications about operational issues that are found in a Red Hat OpenShift cluster | - Prometheus Alertmanager \n - {{site.data.keyword.Bluemix_notm}} monitoring and {{site.data.keyword.Bluemix_notm}} logging and event notifications              | Prometheus Alertmanager                                   | Prometheus Alertmanager can be used for Portworx components                                                                            |
| Operational alerts for Portworx components    | Provide a mechanism to identify and send notifications about issues that are found in Portworx components      | - Prometheus Alertmanager \n - {{site.data.keyword.Bluemix_notm}} Activity Tracker and IBM Monitoring +  Event notifications           | Prometheus Alertmanager                                   | Portworx alerting solution is based on Prometheus Alertmanager                                                                         |
{: caption="Table 3. Architecture decisions for service management alerting" caption-side="bottom"}
