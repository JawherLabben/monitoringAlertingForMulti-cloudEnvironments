# Monitoring and Alerting For Multi-cloud Environments

### Description

This project aims to implement a multi-cloud monitoring solution for monitoring and managing cloud resources across multiple platforms, such as Azure and AWS. A centralized solution will collect and analyze monitoring data, issue personalized alerts, and display real-time metrics. The goal is to enhance transparency, control, and efficiency in managing cloud resources, while optimizing performance and cost utilization.

The monitoring solution leverages the Prometheus architecture, a powerful open-source monitoring and alerting toolkit. It follows a pull-based model where Prometheus scrapes metrics data from the monitored targets at regular intervals. With the integration of Alertmanager, it provides robust alerting capabilities, including the ability to send alerts to a Slack channel.

In this phase of the project, we will create two virtual machines in the Azure cloud. The first machine will be utilized for monitoring other machines, while the other machine in the Azure and AWS clouds will serve as targets as part of a multi-cloud monitoring setup. The monitoring data collected from these targets will be analyzed and visualized using Grafana, allowing for real-time monitoring and efficient management of cloud resources.

### Features
* Centralized monitoring of cloud resources across multiple platforms
* Real-time metrics and personalized alerts
* Cost optimization for efficient resource utilization
* Support for Azure, AWS, and GCP cloud providers

## Components
The multi-cloud monitoring solution consists of the following key components:

* Monitoring Agent: Collects and sends metrics data from monitored cloud resources to the centralized monitoring system.

* Centralized Monitoring System: Stores and analyzes metrics data, providing real-time visualization and customizable dashboards.

* Alert and Notification System: Issues custom alerts based on predefined thresholds or abnormal conditions, supporting various notification channels. In this project, the notification channel used is Slack, providing real-time alert notifications to the designated Slack channel.
* Visualization and Reporting: Leverages Grafana for data visualization, enabling stakeholders to monitor resource utilization and track performance trends.

* Configuration Management: Centralized interface for managing monitoring agent configurations, alert rules, and visualization settings.

## Prometheus Architecture

The monitoring component of this solution is based on the Prometheus architecture, a powerful open-source monitoring and alerting toolkit. Prometheus follows a pull-based model, where it scrapes metrics data from the monitored targets at regular intervals. The key components of the Prometheus architecture are:

* Prometheus Server: Responsible for collecting and storing metrics data from the monitored targets.
* Metrics Exporters: Agents or plugins that expose metrics in a format that Prometheus can scrape.
* Targets: The machines or services that are monitored and provide metrics to Prometheus.
* Alertmanager: Handles alert notifications based on predefined alerting rules.
* Grafana: A popular data visualization tool used to create dashboards and visualize metrics collected by Prometheus.

This diagram shows the architecture of Prometheus and some of its ecosystem components. 

![architecture](https://github.com/JawherLabben/monitoringAlertingForMulti-cloudEnvironments/assets/75523733/6bff693a-1acd-4891-be5a-64f6754ee1f8)

Prometheus extracts metrics directly from instrumented jobs, or through intermediate push gateways for short-lived jobs. Store all collected samples locally and run rules on that data to aggregate and record new time series from existing data or generate alerts. You can visualize the collected data using Grafana or any other API consumer.

## Getting Started

### Prerequisites
Cloud provider accounts (Azure, AWS, GCP) with appropriate access credentials


* Create a virtual machine in the Azure cloud
* Create a virtual machine (EC2 instance) in the AWS cloud
* Install Prometheus
* Install Node Exporter
* Install Grafana on Ubuntu 20.04
* Install Pushgateway Prometheus on Ubuntu 20.04
* Install Alertmanager on Ubuntu 20.04
* Alertmanager Slack Channel Integration
 
![1](https://github.com/JawherLabben/monitoringAlertingForMulti-cloudEnvironments/assets/75523733/76531ad0-c973-4894-a4e8-9c5869203481)

 
## Metrics
### What are metrics
 
In layperson terms, metrics are numeric measurements. Time series means that changes are recorded over time. What users want to measure differs from application to application. For a web server it might be request times, for a database it might be number of active connections or number of active queries etc.

Metrics play an important role in understanding why your application is working in a certain way. Let's assume you are running a web application and find that the application is slow. You will need some information to find out what is happening with your application. For example the application can become slow when the number of requests are high. If you have the request count metric you can spot the reason and increase the number of servers to handle the load.

 ###
 Before importing a template into Grafana, it is best to have a data source configured so that dashboards can work properly. Here are the steps to configure a data source before importing a template in Grafana:
 
 * Step 1 : Log in to your Grafana instance and go to the main dashboard.
* Step 2 : Log in to your Grafana instance and go to the main dashboard.
* Step 3 : From the settings dropdown menu, select "Data Sources". You will be directed to the data sources page.
* Step 4 : On the data sources page, click on the "Add data source" button to start configuring a new data source.
* Step 5 : Choose the type of data source "Prometheus".
* Step 6: Configure specific data source details (database name, etc.)
* Step 7 : Once you have configured the data source, click on the "Save & Test" button to check the connection to the data source. Grafana will perform a connection test and display a success or failure message.
* Step 8 : If the connection test is successful, you can now proceed with importing the template.

### importing the template 
 After configuring the data source in Grafana, you can proceed with importing a template. Here's how you can import a template after configuring the data source:
 * Step 1 : Log in to your Grafana instance and go to the main dashboard.
 * Step 2 : In the left sidebar, click on the "+" icon and select "Import" from the dropdown menu. This will take you to the import page.
 * Step 3 : On the import page, you will see options to import a dashboard.
 * Step 4 : In the import section, you can either upload a JSON file containing the template or paste the JSON code directly into the editor. If you are going to use our json file, just download the file and import it.
* Step 5 : Once the JSON is loaded, Grafana will display a preview of the template settings and configurations. Review the settings.
* Step 6 : Scroll down to the bottom of the page and choose the data source you configured earlier from the dropdown menu. This will associate the template with the selected data source "Prometheus".
* Step 7 : Customize any other import options as needed, such as choosing a new dashboard title.
* Step 8 : Finally, click on the "Import" button to import the template. If the import is successful, you will see a success message, and the imported dashboard or folder will be available in your Grafana instance.





 
### Metric name and help template system


Metric name recommendation: `{name}_{metric}_{aggregation}_{unit}`

Help recommendation: `Azure metrics for {metric} with aggregation {aggregation} as {unit}`


| Template        | Description                                                                               |
|-----------------|-------------------------------------------------------------------------------------------|
| `{name}`        | Name of template specified by request parameter `name`                                    |
| `{type}`        | The ResourceType or MetricNamespace specified in the request (not applicable to all APIs) |
| `{metric}`      | Name of Azure monitor metric                                                              |
| `{dimension}`   | Dimension value of Azure monitor metric (if dimension is used)                            |
| `{unit}`        | Unit name of Azure monitor metric (eg `count`, `percent`, ...)                            |
| `{aggregation}` | Aggregation of Azure monitor metric (eg `total`, `average`)                               |
| `{interval}`    | Interval of requested Azure monitor metric                                                |
| `{timespan}`    | Timespan of requested Azure monitor metric                                                |

## Rows Metrics

| Rows metrics                             | Description                                                                     |
|------------------------------------------|---------------------------------------------------------------------------------|
| `Quick CPU / Mem / Disk`                 | `Monitoring CPU, memory, and disk usage`                                        |   
| `Basic CPU / Mem / Net / Disk`           | `Displays basic metrics for CPU, memory, network, and disk usage`               |
| `CPU / Memory / Net / Disk(8 panels)`    | `CPU, Memory, and Network performance metrics`                                  |
| `Memory Meminfo`                         | `Displays system memory statistics (free, buffer, cache, etc.) `                |
| `Memory Vmstat`                          | `displays the time differences between servers and clients  `                   |              
| `System Processes`                       | `System processes running on the server `                                       |             
| ` System Misc`                           | ` Miscellaneous system-related  `                                               |             
| ` Hardware Misc`                         | ` Miscellaneous Hardware-related  `                                             |              
| ` Storage Disk`                          | `  metrics related to storage disks such as disk space utilization `            |             
| ` Storage Filesystem`                    | ` shows metrics related to the file systems  `                                  |              
| ` Network Traffic`                       | ` various network metrics  `                                                    |             
| ` Network Sockstat`                      | ` statistics of network sockets opened by the kernel  `                         |              
| ` Network Netstat`                       | ` (Established, waiting, current, etc) network connections on the machine  `    |       
| ` Node Exporter`                         | ` (Established, waiting, current, etc) network connections on the machine  `    |   


![2023-05-03_163336](https://github.com/JawherLabben/monitoringAlertingForMulti-cloudEnvironments/assets/75523733/b81f5279-7182-4ac5-96b8-68aea5a3dbe7)

 
 ## Contributing
 If you have ideas, suggestions, or bug reports, contributions are welcome at any time!

