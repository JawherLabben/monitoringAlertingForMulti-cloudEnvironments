# Create a virtual machine in the Azure cloud

Follow this step-by-step guide to create a virtual machine in the Azure cloud.


* Step 1:
Sign in to the Azure portal
Go to the Azure portal website (portal.azure.com) and sign in using your Azure account credentials.

* Step 2:
Go to Virtual Machine Services.
After logging in, click Virtual Machines in the left sidebar or use the search bar at the top to find and select Virtual Machine Services. 

* Step 3:
Click "Add" to create a new virtual machine
On the Virtual Machines page, click the Add button to start the process of creating a new virtual machine.

* Step 4:
Choose a base image
In the "Basic" tab, you need to enter some initial information. First, select a subscription, resource group, and region in which to create the virtual machine.

Then select the base image you want for your virtual machine. Azure offers a variety of pre-configured images for different operating systems and applications. You can choose an image from the Azure Marketplace or use your own custom image.

* Step 5:
Provide instance details
In this step you need to specify the details of your virtual machine. B. Administrator Account Name, Username, and Password. Also, choose the appropriate size for your virtual machine based on your required CPU, memory, and disk configuration.

* Step 6:
Network configuration
Azure offers various networking options for virtual machines. You can choose to create a new virtual network or use an existing virtual network. Configure network settings according to your needs, such as subnets, public IP addresses, and network security groups. 

* Step 7:
Configure management options
This step allows you to configure additional management options for your virtual machine. B. Monitoring, diagnostics, startup diagnostics, and availability records (if applicable). Adjust these settings as needed for your specific needs.

* Step 8:
confirm and create
Review all the configuration details you specified so far. If everything is correct, click the Create button to start the virtual machine creation process. Azure will start provisioning and deploying virtual machines based on your specifications. Step 9:
Access and manage virtual machines
After completing the virtual machine creation process, you can access and manage your virtual machine from the Azure portal. In Virtual Machine Services, click the newly created virtual machine to view details, perform management tasks, and remotely access the virtual machine. 

# Create a virtual machine (EC2 instance) in the AWS cloud

Follow the step-by-step guide below to create a virtual machine (EC2 instance) in the AWS cloud.


* Step 1:
Sign in to the AWS Management Console
Go to the AWS Management Console website (console.aws.amazon.com) and sign in using your AWS account credentials.

* Step 2:
Go to EC2 services
After signing in, you will see the dashboard of the AWS Management Console. Find and select the "EC2" service using the search bar above, or search for it in the "Computing" section.

* Step 3:
Click Start Instance to create a new EC2 instance.
From the EC2 Dashboard, click the Launch Instance button to start the process of creating a new virtual machine.

* Step 4:
Choose an Amazon Machine Image (AMI).
In the Select Amazon Machine Image (AMI) step, you need to select the base image you want for your virtual machine. AWS offers a wide range of pre-configured images for various operating systems and applications. You can choose from the AWS Marketplace or use one of the AMIs provided by AWS. 

* Step 5:
Please select an instance type
In this step, you need to select an instance type for your virtual machine. AWS offers a variety of instance types with different combinations of CPU, memory, storage, and network capacity. Choose the instance type that best suits your needs.

* Step 6:
Configure instance details
In this step you can configure additional details for your instance. You can specify the number of instances to launch, configure network settings, assign security groups (firewall rules), and configure storage options. Adjust these settings according to your specific needs.

* Step 7:
Add storage
Configure virtual machine storage options. You can choose the size and type of storage. B. An Amazon Elastic Block Store (EBS) volume. Adjust the settings according to your storage needs.

* Step 8:
configure security groups
This step requires configuring security group settings for the virtual machine. Security groups act as virtual firewalls that control incoming and outgoing traffic. Define rules to allow access to virtual machines based on your security needs.

* Step 9:
Reviews and referrals
Review all the configuration details you specified so far. If everything is correct, click the Start button to start the EC2 instance creation process. AWS will prompt you to select or create an SSH key pair to use for secure remote access to your virtual machine.  

* Step 10:
Access and manage virtual machines
Once the EC2 instance creation process is complete, it can be accessed and managed through the AWS Management Console. Find your newly created instance in the EC2 dashboard and remotely access the virtual machine using the specified public IP address or DNS name. 

# Install Prometheus

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

# Install Node Exporter

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

 ### Install Grafana on Ubuntu 20.04

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
 
 ### Install Pushgateway Prometheus on Ubuntu 20.04
 
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

### Install Alertmanager on Ubuntu 20.04

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

 
# Alertmanager Slack Channel Integration
 
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
 
 
![1](https://github.com/JawherLabben/monitoringAlertingForMulti-cloudEnvironments/assets/75523733/76531ad0-c973-4894-a4e8-9c5869203481)

 


 # configure a data source before importing a template in Grafana
 Before importing a template into Grafana, it is best to have a data source configured so that dashboards can work properly. Here are the steps to configure a data source before importing a template in Grafana:
 
 * Step 1 : Log in to your Grafana instance and go to the main dashboard.
* Step 2 : Log in to your Grafana instance and go to the main dashboard.
* Step 3 : From the settings dropdown menu, select "Data Sources". You will be directed to the data sources page.
* Step 4 : On the data sources page, click on the "Add data source" button to start configuring a new data source.
* Step 5 : Choose the type of data source "Prometheus".
* Step 6: Configure specific data source details (database name, etc.)
* Step 7 : Once you have configured the data source, click on the "Save & Test" button to check the connection to the data source. Grafana will perform a connection test and display a success or failure message.
* Step 8 : If the connection test is successful, you can now proceed with importing the template.

# importing the template 
 After configuring the data source in Grafana, you can proceed with importing a template. Here's how you can import a template after configuring the data source:
 * Step 1 : Log in to your Grafana instance and go to the main dashboard.
 * Step 2 : In the left sidebar, click on the "+" icon and select "Import" from the dropdown menu. This will take you to the import page.
 * Step 3 : On the import page, you will see options to import a dashboard.
 * Step 4 : In the import section, you can either upload a JSON file containing the template or paste the JSON code directly into the editor. If you are going to use our json file, just download the file and import it.
* Step 5 : Once the JSON is loaded, Grafana will display a preview of the template settings and configurations. Review the settings.
* Step 6 : Scroll down to the bottom of the page and choose the data source you configured earlier from the dropdown menu. This will associate the template with the selected data source "Prometheus".
* Step 7 : Customize any other import options as needed, such as choosing a new dashboard title.
* Step 8 : Finally, click on the "Import" button to import the template. If the import is successful, you will see a success message, and the imported dashboard or folder will be available in your Grafana instance.
