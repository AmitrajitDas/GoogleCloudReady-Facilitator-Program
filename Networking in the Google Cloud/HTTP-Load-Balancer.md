## Overview

Google Cloud HTTP(S) load balancing is implemented at the edge of Google's network in Google's points of presence (POP) around the world. User traffic directed to an HTTP(S) load balancer enters the POP closest to the user and is then load balanced over Google's global network to the closest backend that has sufficient capacity available.

Cloud Armor IP allowlist/denylist enable you to restrict or allow access to your HTTP(S) load balancer at the edge of the Google Cloud, as close as possible to the user and to malicious traffic. This prevents malicious users or traffic from consuming resources or entering your virtual private cloud (VPC) networks.

In this lab, you configure an HTTP Load Balancer with global backends, as shown in the diagram below. Then, you stress test the Load Balancer and denylist the stress test IP with Cloud Armor.


## Objectives

In this lab, you learn how to perform the following tasks:

Create HTTP and health check firewall rules

Configure two instance templates

Create two managed instance groups

Configure an HTTP Load Balancer with IPv4 and IPv6

Stress test an HTTP Load Balancer

Denylist an IP address to restrict access to an HTTP Load Balancer



## Configure HTTP and health check firewall rules
Configure firewall rules to allow HTTP traffic to the backends and TCP traffic from the Google Cloud health checker.

Create the HTTP firewall rule
Create a firewall rule to allow HTTP traffic to the backends.

In the Cloud Console, navigate to Navigation menu (mainmenu.png) > VPC network > Firewall.

Notice the existing ICMP, internal, RDP, and SSH firewall rules.

Each Google Cloud project starts with the default network and these firewall rules.

Click Create Firewall Rule.

Set the following values, leave all other values at their defaults:
```
Property	Value (type value or select option as specified)
Name	default-allow-http
Network	default
Targets	Specified target tags
Target tags	http-server
Source filter	IP Ranges
Source IP ranges	0.0.0.0/0
Protocols and ports	Specified protocols and ports, and then check tcp, type: 80
Make sure to include the /0 in the Source IP ranges to specify all networks.
Click Create.
```

Create the health check firewall rules
Health checks determine which instances of a load balancer can receive new connections. For HTTP load balancing, the health check probes to your load balanced instances come from addresses in the ranges 130.211.0.0/22 and 35.191.0.0/16. Your firewall rules must allow these connections.

Still in the Firewall rules page, click Create Firewall Rule.

Set the following values, leave all other values at their defaults:
```
Property	Value (type value or select option as specified)
Name	default-allow-health-check
Network	default
Targets	Specified target tags
Target tags	http-server
Source filter	IP Ranges
Source IP ranges	130.211.0.0/22, 35.191.0.0/16
Protocols and ports	Specified protocols and ports, and then check tcp
Make sure to enter the two Source IP ranges one-by-one and pressing SPACE in between them.
Click Create.
```




## Configure instance templates and create instance groups
A managed instance group uses an instance template to create a group of identical instances. Use these to create the backends of the HTTP Load Balancer.

Configure the instance templates
An instance template is an API resource that you use to create VM instances and managed instance groups. Instance templates define the machine type, boot disk image, subnet, labels, and other instance properties. Create one instance template for us-east1 and one for europe-west1.

In the Cloud Console, navigate to Navigation menu (mainmenu.png) > Compute Engine > Instance templates, and then click Create instance template.
For Name, type us-east1-template.
For Series, select N1.
Click Management, security, disks, networking, sole tenancy.


Click the Management tab.


Under Metadata, specify the following:

Key	Value
startup-script-url	gs://cloud-training/gcpnet/httplb/startup.sh
The startup-script-url specifies a script that executes when instances are started. This script installs Apache and changes the welcome page to include the client IP and the name, region, and zone of the VM instance. Feel free to explore this script here.
Click Networking.

For Network interfaces, set the following values, leave all other values at their defaults:

Property	Value (type value or select option as specified)
Network	default
Subnet	default (us-east1)
Network tags	http-server
The network tag http-server ensures that the HTTP and Health Check firewall rules apply to these instances.
Click Create.
Wait for the instance template to be created.
Now create another instance template for subnet-b by copying us-east1-template:

Click on us-east1-template and then click on Create Similar option from the top.

For Name, type europe-west1-template.

Click Management, security, disks, networking, sole tenancy.

Click Networking.

For Network interfaces, select default (europe-west1) as the Subnet.

Click Create.

Create the managed instance groups
Create a managed instance group in us-east1 and one in europe-west1.

Still in Compute Engine, click Instance groups in the left menu.

instance_groups.png

Click Create instance group.

Set the following values, leave all other values at their defaults:
```
Property	Value (type value or select option as specified)
Name	us-east1-mig
Location	Multiple zones
Region	us-east1
Instance template	us-east1-template
Autoscaling > Autoscaling metrics > Click Pencil icon > Metric type	CPU utilization
Target CPU utilization	80
Minimum number of instances	1
Maximum number of instances	5
Cool-down period	45
Managed instance groups offer autoscaling capabilities that allow you to automatically add or remove instances from a managed instance group based on increases or decreases in load. Autoscaling helps your applications gracefully handle increases in traffic and reduces cost when the need for resources is lower. You just define the autoscaling policy and the autoscaler performs automatic scaling based on the measured load.
Click Done.
```

Click Create.

Now repeat the same procedure for create a second instance group for europe-west1-mig in europe-west1:

Click Create Instance group.

Set the following values, leave all other values at their defaults:

```
Property	Value (type value or select option as specified)
Name	europe-west1-mig
Location	Multiple zones
Region	europe-west1
Instance template	europe-west1-template
Autoscaling > Autoscaling metrics > Click Pencil icon > Metric type	CPU utilization
Target CPU utilization	80
Minimum number of instances	1
Maximum number of instances	5
Cool-down period	45
Click Done.
```

Click Create.


## Verify the backends
Verify that VM instances are being created in both regions and access their HTTP sites.

Still in Compute Engine, click VM instances in the left menu.

Notice the instances that start with us-east1-mig and europe-west1-mig.

These instances are part of the managed instance groups.

Click on the External IP of an instance of us-east1-mig.

You should see the Client IP (your IP address), the Hostname (starts with us-east1-mig) and the Server Location (a zone in us-east1).

Click on the External IP of an instance of europe-west1-mig.

You should see the Client IP (your IP address), the Hostname (starts with europe-west1-mig) and the Server Location (a zone in europe-west1).


The Hostname and Server Location identifies where the HTTP Load Balancer sends traffic.






## Configure the HTTP Load Balancer
Configure the HTTP Load Balancer to balance traffic between the two backends (us-east1-mig in us-east1 and europe-west1-mig in europe-west1).



Start the configuration
In the Cloud Console, click Navigation menu (mainmenu.png) > click Network Services > Load balancing, and then click Create load balancer.

Under HTTP(S) Load Balancing, click on Start configuration.


Select From Internet to my VMs, and click Continue.

Set the Name to http-lb.

Configure the backend
Backend services direct incoming traffic to one or more attached backends. Each backend is composed of an instance group and additional serving capacity metadata.

Click on Backend configuration.

For Backend services & backend buckets, click Create or select backend services & backend buckets, then click Backend services, and then click Create a backend service.

Set the following values, leave all other values at their defaults:

```
Property	Value (select option as specified)
Name	http-backend
Instance group	us-east1-mig
Port numbers	80
Balancing mode	Rate
Maximum RPS	50
Capacity	100
This configuration means that the load balancer attempts to keep each instance of us-east1-mig at or below 50 requests per second (RPS).
Click Done.
```

Click Add backend.

Set the following values, leave all other values at their defaults:

```
Property	Value (select option as specified)
Instance group	europe-west1-mig
Port numbers	80
Balancing mode	Utilization
Maximum backend utilization	80
Capacity	100
This configuration means that the load balancer attempts to keep each instance of europe-west1-mig at or below 80% CPU utilization.
Click Done.
```

For Health Check, select Create a health check.


Set the following values, leave all other values at their defaults:

```
Property	Value (select option as specified)
Name	http-health-check
Protocol	TCP
Port	80
```
Health checks determine which instances receive new connections. This HTTP health check polls instances every 5 seconds, waits up to 5 seconds for a response and treats 2 successful or 2 failed attempts as healthy or unhealthy, respectively.
Click Save and Continue.
Check the Enable Logging box.
Set the Sample Rate to 1:

Click Create to create the backend service.

Configure the frontend
The host and path rules determine how your traffic will be directed. For example, you could direct video traffic to one backend and static traffic do another backend. However, you are not configuring the Host and path rules in this lab.

Click on Frontend configuration.

Specify the following, leaving all other values at their defaults:

```
Property	Value (type value or select option as specified)
Protocol	HTTP
IP version	IPv4
IP address	Ephemeral
Port	80
Click Done.
```

Click Add Frontend IP and port.

Specify the following, leaving all other values at their defaults:

```
Property	Value (type value or select option as specified)
Protocol	HTTP
IP version	IPv6
IP address	Ephemeral
Port	80
Click Done.
```


HTTP(S) load balancing supports both IPv4 and IPv6 addresses for client traffic. Client IPv6 requests are terminated at the global load balancing layer, then proxied over IPv4 to your backends.
Review and create the HTTP Load Balancer
Click on Review and finalize.
review.png

Review the Backend services and Frontend.
review_finalize.png

Click on Create.
Wait for the load balancer to be created.
Click on the name of the load balancer (http-lb).
Note the IPv4 and IPv6 addresses of the load balancer for the next task. They will be referred to as [LB_IP_v4] and [LB_IP_v6], respectively.
The IPv6 address is the one in hexadecimal format.


## Test the HTTP Load Balancer
Now that you created the HTTP Load Balancer for your backends, verify that traffic is forwarded to the backend service.


Access the HTTP Load Balancer
To test IPv4 access to the HTTP Load Balancer, open a new tab in your browser and navigate to http://[LB_IP_v4]. Make sure to replace [LB_IP_v4] with the IPv4 address of the load balancer.

It might take up to 5 minutes to access the HTTP Load Balancer. In the meantime, you might get a 404 or 502 error. Keep trying until you see the page of one of the backends.
Depending on your proximity to us-east1 and europe-west1, you traffic is either forwarded to a us-east1-mig or europe-west1-mig instance.
If you have a local IPv6 address, try the IPv6 address of the HTTP Load Balancer by navigating to http://[LB_IP_v6]. Make sure to replace [LB_IP_v6] with the IPv6 address of the load balancer.

Stress test the HTTP Load Balancer
Create a new VM to simulate a load on the HTTP Load Balancer using siege. Then, determine if traffic is balanced across both backends when the load is high.

In the Console, navigate to Navigation menu (mainmenu.png) > Compute Engine > VM instances.

Click Create instance.

Set the following values, leave all other values at their defaults:
```
Property	Value (type value or select option as specified)
Name	siege-vm
Region	us-west1
Zone	us-west1-c
Series	N1
Given that us-west1 is closer to us-east1 than to europe-west1, traffic should be forwarded only to us-east1-mig (unless the load is too high).
```
Click Create.

Wait for the siege-vm instance to be created.

For siege-vm, click SSH to launch a terminal and connect.

Run the following command, to install siege:
```
sudo apt-get -y install siege
```
To store the IPv4 address of the HTTP Load Balancer in an environment variable, run the following command, replacing [LB_IP_v4] with the IPv4 address:

export LB_IP=[LB_IP_v4]
To simulate a load, run the following command:
```
siege -c 250 http://$LB_IP
```
The output should look like this (do not copy; this is example output):

New configuration template added to /home/cloudcurriculumdeveloper/.siege
Run siege -C to view the current settings in that file
[alert] Zip encoding disabled; siege requires zlib support to enable it: No such file or directory
** SIEGE 4.0.2
** Preparing 250 concurrent users for battle.
The server is now under siege...
In the Cloud Console, on the Navigation menu (mainmenu.png), click Network Services > Load balancing.
Click Backends.
Click http-backend.
Monitor the Frontend Location (Total inbound traffic) between North America and the two backends for 2 to 3 minutes.
At first, traffic should just be directed to us-east1-mig but as the RPS increases, traffic is also directed to europe-west1-mig.


This demonstrates that by default traffic is forwarded to the closest backend but if the load is very high, traffic can be distributed across the backends.

Return to the SSH terminal of siege-vm.

Press CTRL+C to stop siege.




## Denylist the siege-vm
Use Cloud Armor to denylist the siege-vm from accessing the HTTP Load Balancer.

Create the security policy
Create a Cloud Armor security policy with a denylist rule for the siege-vm.

In the Console, navigate to Navigation menu (mainmenu.png) > Compute Engine > VM instances.
Note the External IP of the siege-vm. This will be referred to as [SIEGE_IP].
There are ways to identify the external IP address of a client trying to access your HTTP Load Balancer. For example, you could examine traffic captured by VPC Flow Logs in BigQuery to determine a high volume of incoming requests.
In the Cloud Console, navigate to Navigation menu > Network Security > Cloud Armor.

Click Create policy.

Set the following values, leave all other values at their defaults:

Property	Value (type value or select option as specified)
Name	denylist-siege
Default rule action	Allow
Click Next step.

Click Add rule.

Set the following values, leave all other values at their defaults:
```
Property	Value (type value or select option as specified)
Condition	Enter the SIEGE_IP
Action	Deny
Deny status	403 (Forbidden)
Priority	1000
```

Click Done.

armor_rule.png

Click Next step.

Click Add Target.

For Type, select Load balancer backend service.

For Target, select http-backend.

Click Done.

Click Create policy.

Alternatively, you could set the default rule to Deny and only allowlist/allow traffic from authorized users/IP addresses.
Wait for the policy to be created before moving to the next step.
Click Check my progress to verify the objective.
Denylist the siege-vm

Verify the security policy
Verify that the siege-vm cannot access the HTTP Load Balancer.

Return to the SSH terminal of siege-vm.

To access the load balancer, run the following:
```
curl http://$LB_IP
```
The output should look like this (do not copy; this is example output):
```
<!doctype html><meta charset="utf-8"><meta name=viewport content="width=device-width, initial-scale=1"><title>403</
title>403 Forbidden
```
It might take a couple of minutes for the security policy to take affect. If you are able to access the backends, keep trying until you get the 403 Forbidden error.
Open a new tab in your browser and navigate to http://[LB_IP_v4]. Make sure to replace [LB_IP_v4] with the IPv4 address of the load balancer.
You can access the HTTP Load Balancer from your browser because of the default rule to allow traffic; however, you cannot access it from the siege-vm because of the deny rule that you implemented.
Back in the SSH terminal of siege-vm, to simulate a load, run the following command:
```
siege -c 250 http://$LB_IP
```
The output should look like this (do not copy; this is example output):
```
[alert] Zip encoding disabled; siege requires zlib support to enable it
** SIEGE 4.0.2
** Preparing 250 concurrent users for battle.
The server is now under siege...
```
Explore the security policy logs to determine if this traffic is also blocked.

In the Console, navigate to Navigation menu > Network Security > Cloud Armor.
Click denylist-siege.
Click Logs.
Click View policy logs.
On the Logging page, make sure to clear all the text in the Query preview. Select resource to Cloud HTTP Load Balancer > http-lb-forwarding-rule > http-lb then click Add.
Now click Run Query.
Expand a log entry in Query results.
Expand httpRequest.
The request should be from the siege-vm IP address. If not, expand another log entry.

Expand jsonPayload.
Expand enforcedSecurityPolicy.
Notice that the configuredAction is to DENY with the name denylist-siege.
query-results.png

Cloud Armor security policies create logs that can be explored to determine when traffic is denied and when it is allowed, along with the source of the traffic.
