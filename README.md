# Monitoring and Alerting For Multi-cloud Environments

### The project's goal

The project is to implement a multi-cloud monitoring solution to monitor and manage cloud resources on multiple platforms such as Azure, AWS, etc. A centralized solution will collect and analyze monitoring data, issue personalized alerts and view metrics in real time. The goal is to improve visibility, control, and efficiency in managing cloud resources, while optimizing performance and utilizing costs.

### Features
Prometheus's main features are:

a multi-dimensional data model with time series data identified by metric name and key/value pairs
PromQL, a flexible query language to leverage this dimensionality
no reliance on distributed storage; single server nodes are autonomous
time series collection happens via a pull model over HTTP
pushing time series is supported via an intermediary gateway
targets are discovered via service discovery or static configuration
multiple modes of graphing and dashboarding support

## Components
The Prometheus ecosystem consists of multiple components, many of which are optional:

the main Prometheus server which scrapes and stores time series data
client libraries for instrumenting application code
a push gateway for supporting short-lived jobs
special-purpose exporters for services like HAProxy, StatsD, Graphite, etc.
an alertmanager to handle alerts
various support tools
Most Prometheus components are written in Go, making them easy to build and deploy as static binaries.

## Architecture of Prometheus
This diagram illustrates the architecture of Prometheus and some of its ecosystem components:

![architecture](https://github.com/JawherLabben/monitoringAlertingForMulti-cloudEnvironments/assets/75523733/6bff693a-1acd-4891-be5a-64f6754ee1f8)

Prometheus scrapes metrics from instrumented jobs, either directly or via an intermediary push gateway for short-lived jobs. It stores all scraped samples locally and runs rules over this data to either aggregate and record new time series from existing data or generate alerts. Grafana or other API consumers can be used to visualize the collected data.

## create a virtual machine in the Azure cloud

To create a virtual machine in the Azure cloud, follow these step-by-step instructions:

Step 1: Sign in to the Azure Portal
Go to the Azure portal website (portal.azure.com) and sign in using your Azure account credentials.

Step 2: Navigate to the Virtual Machines service
Once signed in, click on "Virtual Machines" in the left-hand sidebar or use the search bar at the top to find and select the "Virtual Machines" service.

Step 3: Click on "Add" to create a new virtual machine
On the Virtual Machines page, click on the "Add" button to start the process of creating a new virtual machine.

Step 4: Select a base image
In the Basics tab, you need to provide some initial information. Begin by selecting the subscription, resource group, and region where you want to create the virtual machine.

Next, choose the desired base image for your virtual machine. Azure provides a variety of pre-configured images for different operating systems and applications. You can select an image from the Azure Marketplace or use your own custom image.

Step 5: Provide instance details
In this step, you'll need to specify the details for your virtual machine, such as the name, username, and password for the administrator account. Additionally, choose the appropriate size for your virtual machine based on the required CPU, memory, and disk configurations.

Step 6: Configure networking
Azure offers various networking options for virtual machines. You can choose to create a new virtual network or use an existing one. Configure the networking settings according to your requirements, including subnet, public IP address, network security groups, etc.

Step 7: Configure management options
In this step, you can configure additional management options for your virtual machine, such as monitoring, diagnostics, boot diagnostics, and availability sets (if applicable). Adjust these settings as needed based on your specific needs.

Step 8: Review and create
Review all the configuration details you have provided so far. If everything looks correct, click on the "Create" button to start the virtual machine creation process. Azure will now begin provisioning and deploying the virtual machine based on your specifications.

Step 9: Access and manage the virtual machine
Once the virtual machine creation process is complete, you can access and manage it through the Azure portal. From the Virtual Machines service, locate your newly created virtual machine and click on it to view its details, perform administrative tasks, and access the virtual machine remotely.

## Create a virtual machine (EC2 instance) in the AWS cloud

To create a virtual machine (EC2 instance) in the AWS cloud, follow these step-by-step instructions:

Step 1: Sign in to the AWS Management Console
Go to the AWS Management Console website (console.aws.amazon.com) and sign in using your AWS account credentials.

Step 2: Navigate to the EC2 service
Once signed in, you'll be on the AWS Management Console dashboard. Use the search bar at the top to find and select the "EC2" service or locate it under the "Compute" section.

Step 3: Click on "Launch instance" to create a new EC2 instance
On the EC2 Dashboard, click on the "Launch instance" button to start the process of creating a new virtual machine.

Step 4: Select an Amazon Machine Image (AMI)
In the "Choose an Amazon Machine Image (AMI)" step, you'll need to select the desired base image for your virtual machine. AWS provides a wide range of pre-configured images for various operating systems and applications. You can choose from the AWS Marketplace or use one of the AWS-provided AMIs.

Step 5: Choose an instance type
In this step, you'll need to select the instance type for your virtual machine. AWS offers a variety of instance types with different combinations of CPU, memory, storage, and networking capacity. Choose the instance type that best suits your requirements.

Step 6: Configure instance details
In this step, you can configure additional details for your instance. You can specify the number of instances to launch, configure network settings, assign a security group (firewall rules), and configure storage options. Adjust these settings based on your specific needs.

Step 7: Add storage
Configure the storage options for your virtual machine. You can choose the size and type of storage, such as Amazon Elastic Block Store (EBS) volumes. Adjust the settings based on your storage requirements.

Step 8: Configure security groups
In this step, you'll need to configure the security group settings for your virtual machine. A security group acts as a virtual firewall to control inbound and outbound traffic. Define the rules to allow access to the virtual machine based on your security requirements.

Step 9: Review and launch
Review all the configuration details you have provided so far. If everything looks correct, click on the "Launch" button to start the EC2 instance creation process. AWS will prompt you to select or create an SSH key pair that will be used for secure remote access to the virtual machine.

Step 10: Access and manage the virtual machine
Once the EC2 instance creation process is complete, you can access and manage it through the AWS Management Console. From the EC2 Dashboard, locate your newly created instance and use the provided public IP address or DNS name to remotely access the virtual machine.


## Install Prometheus

 To begin with, we will create a dedicated Linux user or system account for Prometheus. This practice provides two significant benefits:
  * It is a security measure that limits the impact of any security incidents associated with the service.
  * It simplifies system administration by providing better organization and tracking of resources.
```
sudo useradd \
    --system \
    --no-create-home \
    --shell /bin/false prometheus
```
We can verify and download the latest version of Prometheus from the official download page "https://prometheus.io/download/"

Use the curl or wget command to download Prometheus

"wget https://github.com/prometheus/prometheus/releases/download/v2.32.1/prometheus-2.32.1.linux-amd64.tar.gz"

Next, we should extract all of the Prometheus files from the archive.
```
"tar -xvf prometheus-2.32.1.linux-amd64.tar.gz"
```

Usually, we would have a disk mounted to the data directory. We will create a directory named /data. Additionally, we need to create a folder for storing Prometheus configuration files.
```
"sudo mkdir -p /data /etc/prometheus"
```
Let's change the directory to Prometheus and move some files.
```
"cd prometheus-2.32.1.linux-amd64"
```
We should move the Prometheus binary and promtool to the /usr/local/bin/ directory. promtool is used for validating configuration files and Prometheus rules.
```
"sudo mv prometheus promtool /usr/local/bin/"
```
Finally, we need to move the sample Prometheus configuration file to the directory we created earlier for Prometheus configuration files.
```
"sudo mv prometheus.yml /etc/prometheus/prometheus.yml"
```
To ensure proper permissions, it is important to set the correct ownership for both the /etc/prometheus/ directory and the data directory.

sudo chown -R prometheus:prometheus /etc/prometheus/ /data/

To clean up after installation, you can delete the Prometheus archive and folder using the "rm" command.

To ensure that the Prometheus binary is executable, execute the following command:
```
prometheus --version
```

## Install Node Exporter

Next, we're going to set up and configured Node Exporter to collect Linux system metrics like CPU load and disk I/O. Node Exporter will expose these as Prometheus-style metrics. Since the installation process is very similar, I'm not going to cover as deep as Prometheus.

First, let's create a system user for Node Exporter by running the following command:

```
sudo useradd \
    --system \
    --no-create-home \
    --shell /bin/false node_exporter
```
You can download Node Exporter from the URL : https://prometheus.io/download/ .

Use wget command to download binary.
```
wget https://github.com/prometheus/node_exporter/releases/download/v1.3.1/node_exporter-1.3.1.linux-amd64.tar.gz
```
Extract node exporter from the archive.
```
tar -xvf node_exporter-1.3.1.linux-amd64.tar.gz
```
Move binary to the /usr/local/bin.

```
sudo mv \
  node_exporter-1.3.1.linux-amd64/node_exporter \
  /usr/local/bin/
```
 Clean up, delete node_exporter archive and a folder.
```
rm -rf node_exporter*
```
Verify that you can run the binary.
```
node_exporter --version
```

Next, create similar systemd unit file.
```
sudo vim /etc/systemd/system/node_exporter.service
```



```

[Unit]
Description=Node Exporter
Wants=network-online.target
After=network-online.target

StartLimitIntervalSec=500
StartLimitBurst=5

[Service]
User=node_exporter
Group=node_exporter
Type=simple
Restart=on-failure
RestartSec=5s
ExecStart=/usr/local/bin/node_exporter \
    --collector.logind

[Install]
WantedBy=multi-user.target

```


Replace Prometheus user and group to node_exporter, and update ExecStart command.
To automatically start the Node Exporter after reboot, enable the service

sudo systemctl enable node_exporter

Then start the Node Exporter.
```
sudo systemctl start node_exporter
```

Check the status of Node Exporter with the following command:

```
sudo systemctl status node_exporter
```

If you have any issues, check logs with journalctl


```
journalctl -u node_exporter -f --no-pager
```
To create a static target, you need to add job_name with static_configs.

```
sudo vim /etc/prometheus/prometheus.yml
```

prometheus.yml

...
  - job_name: node_export
    static_configs:
      - targets: ["localhost:9100"]


By default, Node Exporter will be exposed on port 9100.
Since we enabled lifecycle management via API calls, we can reload Prometheus config without restarting the service and causing the downtime.
Before, restarting check if the config is valid

```
promtool check config /etc/prometheus/prometheus.yml
```
Then, you can use a POST request to reload the config.

```
curl -X POST http://localhost:9090/-/reload
```
Check the targets section http://<ip>:9090/targets

 ## Install Grafana on Ubuntu 20.04

To visualize metrics we can use Grafana. There are many different data sources that Grafana supports, one of them is Prometheus.
First, let's make sure that all the dependencies are installed
```
 sudo apt-get install -y apt-transport-https software-properties-common
```
Next, add GPG key.

```
 wget -q -O - https://packages.grafana.com/gpg.key | sudo apt-key add -
```
 
 Add this repository for stable releases.

```
 echo "deb https://packages.grafana.com/oss/deb stable main" | sudo tee -a /etc/apt/sources.list.d/grafana.list
```

 
 After you add the repository, update and install Garafana.

```
sudo apt-get update
sudo apt-get -y install grafana
```
 To automatically start the Grafana after reboot, enable the service.
 
```
 sudo systemctl enable grafana-server
```
 Then start the Grafana.
```
 sudo systemctl start grafana-server
```
To check the status of Grafana, run the following command:
```
 sudo systemctl status grafana-server
``` 
 
 Go to http://<ip>:3000 and log in to the Grafana using default credentials. The username is admin, and the password is admin as well.
When you log in for the first time, you get the option to change the password. Let's use devops123 for the new password.
To visualize metrics, you need to add a data source first. Click Add data source and select Prometheus. For the URL, enter http://localhost:9090 and click Save and test. You can see Data source is working.
Usually, in production environments, you would store all the configurations in Git. Let me show you another way to add a data source as a code. Let's remove the data source from UI.

 
 Create a new datasources.yaml file.
```
 sudo vim /etc/grafana/provisioning/datasources/datasources.yaml
```
 datasources.yaml

apiVersion: 1

datasources:
  - name: Prometheus
    type: prometheus
    url: http://localhost:9090
    isDefault: true
 
 
Optionally, you can make this data source a default one.
 * Optionally, you can make this data source a default one.
 
```
sudo systemctl restart grafana-server
```
 
Go back to Grafana and refresh the page. You should see the Prometheus data source.
We can import existing Grafana dashboards or create your own. Let's create a simple graph.
Go back to the Prometheus, and let's explore what metrics we have. Start typing scrape_duration_seconds and click Execute. This metric will show you the duration of the scrape of each Prometheus target. At this point, we have node_exporter and prometheus targets. We're going to use this metric to create a simple graph in Grafana.
Go to Grafana and click create Dashboard and then add a new panel.
Give a title Scrape Duration and paste scrape_duration_seconds metric. You can also reduce the time interval to 1 hour.
For the legend, we can use the job label and for the unit - seconds. There are a lot of configuration parameters that you can use. Let's keep it simple and click apply and save dashboard as Prometheus.
Since we already have Node Exporter, we can import an open-source dashboard to visualize CPU, Memory, Network, and a bunch of other metrics. You can search for node exporter on the Grafana website https://grafana.com/grafana/dashboards/.
Copy 1860 ID to Clipboard.
Now, in Grafana, you can click Import and paste this ID. Then load the dashboard. Select Prometheus datasource and click import.
You have all sorts of metrics here that come from node exporter
 
 ## Install Pushgateway Prometheus on Ubuntu 20.04
 
 Next component that I want to install is Pushgateway. The Pushgateway is a service that allows you to push metrics from jobs that cannot be scrapped. For example, you can have Jenkins jobs or some kind of cron jobs. You can't scrape them since they are running for a limited time only.
The installation process is very similar to Prometheus and Node exporter.
Create a dedicated user first
 
```
 sudo useradd \
    --system \
    --no-create-home \
    --shell /bin/false pushgateway
``` 
 Download archive with Pushgateway from https://prometheus.io/download/.

 ```
 wget https://github.com/prometheus/pushgateway/releases/download/v1.4.2/pushgateway-1.4.2.linux-amd64.tar.gz
```
 
 Extract all the files.
 ```
tar -xvf pushgateway-1.4.2.linux-amd64.tar.gz
```
 
 
 Move pushgateway binary to to /usr/local/bin.
```
 sudo mv pushgateway-1.4.2.linux-amd64/pushgateway /usr/local/bin/
```
 
 
 
 
 
 Clean up.
```
rm -rf pushgateway*
```
 Check if Pushgateway can be executed.
 
```
 pushgateway --version
```

 Also, you can get configuration options by running help.
```
 pushgateway --help
```

 Create a systemd service.
```
 sudo vim /etc/systemd/system/pushgateway.service
```
 
 
 
 pushgateway.service
```
[Unit]
Description=Pushgateway
Wants=network-online.target
After=network-online.target

StartLimitIntervalSec=500
StartLimitBurst=5

[Service]
User=pushgateway
Group=pushgateway
Type=simple
Restart=on-failure
RestartSec=5s
ExecStart=/usr/local/bin/pushgateway

[Install]
WantedBy=multi-user.target
```

 
 Enable the service.
```
sudo systemctl enable pushgateway
```
 Start Pushgateway.
 
 ```
sudo systemctl start pushgateway
```
 
 Check the status.
```
 sudo systemctl status pushgateway
```
 

 
 Pushgateway can be reachible at http://<ip>:9091.

Let's add Pushgateway as a target to Prometheus.
```
 sudo vim /etc/prometheus/prometheus.yml
``` 
 
 prometheus.yml
```
 ...
  - job_name: pushgateway
    honor_labels: true
    static_configs:
      - targets: ["localhost:9091"]
```
 
 Check Prometheus configuration. If it's valid, reload the config.
```
promtool check config /etc/prometheus/prometheus.yml
curl -X POST http://localhost:9090/-/reload
``` 
 Make sure that the target is up and healthy http://<ip>:9090.
To send metrics to the Pushgateway, you just need to send a POST request to the following endpoint http://localhost:9091/metrics/job/backup. Where backup is an arbitrary name that will show up as a label.
Use curl and pipe the string with echo to Pushgateway. Let's imagine that the Jenkins job that we named backup took almost 16 seconds to complete.

```
echo "jenkins_job_duration_seconds 15.98" | curl --data-binary @- http://localhost:9091/metrics/job/backup
```


 You can find this metric in Prometheus. Refresh the page and start typing jenkins_job_duration_seconds.

## Install Alertmanager on Ubuntu 20.04

 To send alerts, we're going to use Alertmanager. It takes care of deduplicating, grouping, and routing them to the correct receiver integration such as email, PagerDuty, or in our case Slack. You can set up multiple Alertmanagers to achieve high availability. For this demo, I will install a single one.

First, let's create a system user for Alertmanager.
 
``` 
 sudo useradd \
    --system \
    --no-create-home \
    --shell /bin/false alertmanager
```
 
 
Then, download Alertmanager from the same downloads page.

 ```
wget https://github.com/prometheus/alertmanager/releases/download/v0.23.0/alertmanager-0.23.0.linux-amd64.tar.gz
```
 Extract Alertmanager binary.
```
tar -xvf alertmanager-0.23.0.linux-amd64.tar.gz
```
 
 For Alertmanager, we need storage. It is mandatory (it defaults to "data/") and is used to store Alertmanager's notification states and silences. Without this state (or if you wipe it), Alertmanager would not know across restarts what silences were created or what notifications were already sent.
```
 sudo mkdir -p /alertmanager-data /etc/alertmanager
```
Now, let's move Alermanager's binary to the local bin and copy sample config.

```
sudo mv alertmanager-0.23.0.linux-amd64/alertmanager /usr/local/bin/
sudo mv alertmanager-0.23.0.linux-amd64/alertmanager.yml /etc/alertmanager/
```
 
 Remove downloaded archive and a folder.
```

rm -rf alertmanager*
```

Check if we can run Alertmanager.
```
alertmanager --version
```

You can also get help and all supported configuration options by running Alertmanager help.
```
alertmanager --help
 
```

 
Next is the systemd service definition.
```
sudo vim /etc/systemd/system/alertmanager.service
```
 alertmanager.service
```
[Unit]
Description=Alertmanager
Wants=network-online.target
After=network-online.target

StartLimitIntervalSec=500
StartLimitBurst=5

[Service]
User=alertmanager
Group=alertmanager
Type=simple
Restart=on-failure
RestartSec=5s
ExecStart=/usr/local/bin/alertmanager \
  --storage.path=/alertmanager-data \
  --config.file=/etc/alertmanager/alertmanager.yml

[Install]
WantedBy=multi-user.target
```
 
Enable alertmanager.
```
sudo systemctl enable alertmanager
```

Start Alertmanager.
```
sudo systemctl start alertmanager
```

 Check the status.
```
sudo systemctl status alertmanager
```

 
Alertmanager will be exposed on port 9093 http://<ip>:9093.

It's time to create a simple alert. In almost all Prometheus setups, you have an alert that is always active. It is used to validate the monitoring system itself. For example, it can be integrated with the deadmanssnitch service. If something goes wrong with the Prometheus or Alertmanager and, you will get an emergency notification that your monitoring system is down. It's a very useful service, especially in production environments.

Let's create alert but without integration with DeadMansSnitch.
```
 sudo vim /etc/prometheus/dead-mans-snitch-rule.yml
```
 
dead-mans-snitch-rule.yml
```

---
groups:
- name: dead-mans-snitch
  rules:
  - alert: DeadMansSnitch
    annotations:
      message: This alert is integrated with DeadMansSnitch.
    expr: vector(1)
```

You also need to update the Prometheus config to specify the location of Alertmanager and specify the path to the new rule.
```
sudo vim /etc/prometheus/prometheus.yml
```
 
prometheus.yml
```
...
alerting:
  alertmanagers:
    - static_configs:
        - targets:
          - localhost:9093
rule_files:
  - dead-mans-snitch-rule.yml
 ```
 It's always a good idea to check Prometheus config before restarting.

 
promtool check config /etc/prometheus/prometheus.yml
 ```
sudo systemctl restart prometheus
sudo systemctl status prometheus
 ```

 
## Alertmanager Slack Channel Integration
 
 Alertmanager can be configured to send emails, can be integrated with PagerDuty and many other services. For this demo, I will integrate Alertmanager with Slack. We're going to create a slack channel where all the alerts will be sent.

Let's create alerts Slack channel.

Create a new Slack app from scratch. Give it a name Prometheus and select a workspace.
You can modify the app from the basic information. Let's upload the Prometheus icon.
Next, we need to enable incoming webhooks. Then add webhook to the workspace.
The last thing, we need to copy Webhook URL and use it in Alertmanager config.
Now, update alertmanager.yml config to include a new route to send alerts to the Slack
 
  ```
 sudo vim /etc/alertmanager/alertmanager.yml
 ```

 ```
 ---
route:
  group_by: ['alertname']
  group_wait: 30s
  group_interval: 5m
  repeat_interval: 1h
  receiver: 'web.hook'
  routes:
  - receiver: slack-notifications
    match:
      severity: warning
receivers:
- name: 'web.hook'
  webhook_configs:
  - url: 'http://127.0.0.1:5001/'
- name: slack-notifications
  slack_configs:
  - channel: "#alerts"
    send_resolved: true
    api_url: "https://hooks.slack.com/services/<id>"
    title: "{{ .GroupLabels.alertname }}"
    text: "{{ range .Alerts }}{{ .Annotations.message }}\n{{ end }}"
inhibit_rules:
  - source_match:
      severity: 'critical'
    target_match:
      severity: 'warning'
    equal: ['alertname', 'dev', 'instance']
```
 
 
 Restart alertmanager.
```
sudo systemctl restart alertmanager
sudo systemctl status alertmanager

```

 Create a new alert rule to test Slack integration.
```
sudo vim /etc/prometheus/batch-job-rules.yml
```
 
 
batch-job-rules.yml
```

---
groups:
- name: batch-job-rules
  rules:
  - alert: JenkinsJobExceededThreshold
    annotations:
      message: Jenkins job exceeded a threshold of 30 seconds.
    expr: jenkins_job_duration_seconds{job="backup"} > 30
    for: 1m
    labels:
      severity: warning
```

 
 Add a new rule to Prometheus.
```
 sudo vim /etc/prometheus/prometheus.yml
```
 
 
prometheus.yml
```
...
rule_files:
  - dead-mans-snitch-rule.yml
  - batch-job-rules.yml
 
 ```
 
 Check the config and reload Prometheus.
```
promtool check config /etc/prometheus/prometheus.yml
curl -X POST -u admin:devops123 http://localhost:9090/-/reload
```
 
 Trigger the alert by sending the new metric to Prometheus Pushgateway.
```
echo "jenkins_job_duration_seconds 31.87" | curl --data-binary @- http://localhost:9091/metrics/job/backup
```
 
In a minute or so, you should get a message in Slack.
If we send a new metric with a duration of less than 30 seconds, Prometheus will resolve the alert
```
echo "jenkins_job_duration_seconds 11.87" | curl --data-binary @- http://localhost:9091/metrics/job/backup
```
 
## Metrics
### What are metrics
 
In layperson terms, metrics are numeric measurements. Time series means that changes are recorded over time. What users want to measure differs from application to application. For a web server it might be request times, for a database it might be number of active connections or number of active queries etc.

Metrics play an important role in understanding why your application is working in a certain way. Let's assume you are running a web application and find that the application is slow. You will need some information to find out what is happening with your application. For example the application can become slow when the number of requests are high. If you have the request count metric you can spot the reason and increase the number of servers to handle the load.
 
 
### Metric name and help template system


Metric name recommendation: `{name}_{metric}_{aggregation}_{unit}`

Help recommendation: `Azure metrics for {metric} with aggregation {aggregation} as {unit}`

Following templates are available:

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



