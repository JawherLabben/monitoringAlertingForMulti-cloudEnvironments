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

![node_exporter service](https://github.com/JawherLabben/monitoringAlertingForMulti-cloudEnvironments/assets/75523733/6c97f53e-0a7c-4bcc-a69b-8b38b0e401b5)


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



