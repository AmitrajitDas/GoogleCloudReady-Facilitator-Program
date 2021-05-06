## Overview
In this lab, you create two nginx web servers and control external HTTP access to the web servers using tagged firewall rules. Then, you explore IAM roles and service accounts.

### Objectives
In this lab, you learn how to perform the following tasks:

Create an nginx web server

Create tagged firewall rules

Create a service account with IAM roles

Explore permissions for the Network Admin and Security Admin roles


## Activate Cloud Shell

Cloud Shell is a virtual machine that is loaded with development tools. It offers a persistent 5GB home directory and runs on the Google Cloud. Cloud Shell provides command-line access to your Google Cloud resources.

In the Cloud Console, in the top right toolbar, click the Activate Cloud Shell button.

Cloud Shell icon

Click Continue.


It takes a few moments to provision and connect to the environment. When you are connected, you are already authenticated, and the project is set to your PROJECT_ID. For example:

Cloud Shell Terminal

gcloud is the command-line tool for Google Cloud. It comes pre-installed on Cloud Shell and supports tab-completion.

You can list the active account name with this command:
```
gcloud auth list

```
(Output)

Credentialed accounts:
```
 - <myaccount>@<mydomain>.com (active)
```
(Example output)

Credentialed accounts:
```
 - google1623327_student@qwiklabs.net
``` 
You can list the project ID with this command:

```
gcloud config list project
```
(Output)

[core]
project = <project_ID>
(Example output)

[core]
project = qwiklabs-gcp-44776a13dea667a6




## Create the web servers
Create two web servers (blue and green) in the default VPC network. Then, install nginx on the webservers and modify the welcome page to distinguish the servers.

### Create the blue server
Create the blue server with a network tag.

In the Console, navigate to Navigation menu (mainmenu.png) > Compute Engine > VM instances.
nav_comengine.png

Click Create.

Set the following values, leave all other values at their defaults:

Property	Value (type value or select option as specified)
Name	blue
Region	us-central1 (Iowa)
Zone	us-central1-a
For more information on available regions and zones, refer here.

Click Management, disks, networking, sole tenancy.

Click Networking.

networking_tab.png

For Network tags, type web-server.

Note: Networks use network tags to identify which VM instances are subject to certain firewall rules and network routes. Later in this lab, you create a firewall rule to allow HTTP access for VM instances with the web-server tag. Alternatively, you could check the Allow HTTP traffic checkbox, which would tag this instance as http-server and create the tagged firewall rule for tcp:80 for you.

Click Create.


## Create the green server
Create the green server without a network tag.

Still in the Console, in the VM instances dialog, click Create instance.

Set the following values, leave all other values at their defaults:

Property	Value (type value or select option as specified)
Name	green
Region	us-central1 (Iowa)
Zone	us-central1-a
Click Create.




## Install nginx and customize the welcome page
Install nginx on both VM instances and modify the welcome page to distinguish the servers.

Still in the VM instances dialog, for blue, click SSH to launch a terminal and connect.

In the SSH terminal to blue, run the following command to install nginx:
```
sudo apt-get install nginx-light -y
```
Open the welcome page in the nano editor:
```
sudo nano /var/www/html/index.nginx-debian.html
```
Replace the <h1>Welcome to nginx!</h1> line with <h1>Welcome to the blue server!</h1>.

Press CTRL+o, ENTER, CTRL+x.

Verify the change:
```
cat /var/www/html/index.nginx-debian.html
```
The output should contain the following (do not copy; this is example output):
```
...
<h1>Welcome to the blue server!</h1>
<p>If you see this page, the nginx web server is successfully installed and
working. Further configuration is required.</p>
...
```
Close the SSH terminal to blue:

exit
Repeat the same steps for the green server:

For green, click SSH to launch a terminal and connect.

Install nginx:
```
sudo apt-get install nginx-light -y
```
Open the welcome page in the nano editor:
```
sudo nano /var/www/html/index.nginx-debian.html
```
Replace the <h1>Welcome to nginx!</h1> line with <h1>Welcome to the green server!</h1>.

Press CTRL+o, ENTER, CTRL+x.

Verify the change:
```
cat /var/www/html/index.nginx-debian.html
```
The output should contain the following (do not copy; this is example output):
```
...
<h1>Welcome to the green server!</h1>
<p>If you see this page, the nginx web server is successfully installed and
working. Further configuration is required.</p>
...
```

Close the SSH terminal to green:

exit


## Create the firewall rule
Create the tagged firewall rule and test HTTP connectivity.

Create the tagged firewall rule
Create a firewall rule that applies to VM instances with the web-server network tag.

In the Console, navigate to Navigation menu (mainmenu.png) > VPC network > Firewall.
Notice the default-allow-internal firewall rule.
The default-allow-internal firewall rule allows traffic on all protocols/ports within the default network. You want to create a firewall rule to allow traffic from outside this network to only the blue server, by using the network tag web-server.

Click Create Firewall Rule.

Set the following values, leave all other values at their defaults and click Create:

Property	Value (type value or select option as specified)
```
Name	allow-http-web-server
Network	default
Targets	Specified target tags
Target tags	web-server
Source filter	IP Ranges
Source IP ranges	0.0.0.0/0
Protocols and ports	Specified protocols and ports, and then check tcp, type: 80; and check Other protocols, type: icmp.
```
Make sure to include the /0 in the Source IP ranges to specify all networks.

Click Create.


### Create a test-vm
Create a test-vm instance using the Cloud Shell command line.

Create a test-vm instance, in the us-central1-a zone:
```
gcloud compute instances create test-vm --machine-type=f1-micro --subnet=default --zone=us-central1-a
```
The output should look like this (do not copy; this is example output):
```
NAME     ZONE           MACHINE_TYPE  PREEMPTIBLE  INTERNAL_IP  EXTERNAL_IP    STATUS
test-vm  us-central1-a  f1-micro                   10.142.0.4   35.237.134.68  RUNNING
You can easily create VM instances from the Console or the gcloud command line.
```


## Test HTTP connectivity
From test-vm curl the internal and external IP addresses of blue and green.

In the Console, navigate to Navigation menu (mainmenu.png) > Compute Engine > VM instances.

Note the internal and external IP addresses of blue and green.

For test-vm, click SSH to launch a terminal and connect.

To test HTTP connectivity to blue's internal IP, run the following command, replacing blue's internal IP:
```
curl <Enter blue's internal IP here>
```
You should see the Welcome to the blue server! header.

To test HTTP connectivity to green's internal IP, run the following command, replacing green's internal IP:
```
curl -c 3 <Enter green's internal IP here>
```
You should see the Welcome to the green server! header.

You are able to HTTP access both servers using their internal IP addresses. The connection on tcp:80 is allowed by the default-allow-internal firewall rule, as test-vm is on the same VPC network as the web servers default network).
To test HTTP connectivity to blue's external IP, run the following command, replacing blue's external IP:
```
curl <Enter blue's external IP here>
```
You should see the Welcome to the blue server! header.

To test HTTP connectivity to green's external IP, run the following command, replacing green's external IP:
```
curl -c 3 <Enter green's external IP here>
```
This should not work! The request hangs.

Press CTRL+c to stop the HTTP request.
As expected, you are only able to HTTP access the external IP address of the blue server as the allow-http-web-server only applies to VM instances with the web-server tag.

You can verify the same behavior from your browser by opening a new tab and navigating to http://[External IP of server].


## Explore the Network and Security Admin roles
Cloud IAM lets you authorize who can take action on specific resources, giving you full control and visibility to manage cloud resources centrally. The following roles are used in conjunction with single-project networking to independently control administrative access to each VPC Network:

Network Admin: Permissions to create, modify, and delete networking resources, except for firewall rules and SSL certificates.
Security Admin: Permissions to create, modify, and delete firewall rules and SSL certificates.
Explore these roles by applying them to a service account, which is a special Google account that belongs to your VM instance, instead of to an individual end user. Rather than creating a new user, you will authorize test-vm to use the service account to demonstrate the permissions of the Network Admin and Security Admin roles.

Verify current permissions
Currently, test-vm uses the Compute Engine default service account, which is enabled on all instances created by Cloud Shell command-line and the Cloud Console.

Try to list or delete the available firewall rules from test-vm.

Return to the SSH terminal of the test-vm instance.

Try to list the available firewall rules:
```
gcloud compute firewall-rules list
```
The output should look like this (do not copy; this is example output):

ERROR: (gcloud.compute.firewall-rules.list) Some requests did not succeed:
 - Insufficient Permission
This should not work!

Try to delete the allow-http-web-server firewall rule:
```
gcloud compute firewall-rules delete allow-http-web-server
```
Enter Y, if asked to continue.
The output should look like this (do not copy; this is example output):
```
ERROR: (gcloud.compute.firewall-rules.delete) Could not fetch resource:
 - Insufficient Permission
 ```
This should not work!

The Compute Engine default service account does not have the right permissions to allow you to list or delete firewall rules. The same applies to other users who do not have the right roles.

Create a service account
Create a service account and apply the Network Admin role.

In the Console, navigate to Navigation menu (mainmenu.png) > IAM & admin > Service Accounts.

Notice the Compute Engine default service account.

Click Create service account.

Set the Service account name to Network-admin and click CREATE.

For Select a role, select Compute Engine > Compute Network Admin and click CONTINUE then click DONE.

After creating service account 'Network-admin', click on three dots at the right corner and click Create Key in the dropdown, then Create to download your JSON output.

Click Close.

A JSON key file download to your local computer. Find this key file, you will upload it in to the VM in a later step.

Rename the JSON key file on your local machine to credentials.json


## Authorize test-vm and verify permissions
Authorize test-vm to use the Network-admin service account.

Return to the SSH terminal of the test-vm instance.

To upload credentials.json through the SSH VM terminal, click on the gear icon in the upper-right corner, and then click Upload file.

Select credentials.json and upload it.

Click Close in the File Transfer window.

Authorize the VM with the credentials you just uploaded:
```
gcloud auth activate-service-account --key-file credentials.json
```
The image you are using has the Cloud SDK pre-installed; therefore, you don’t need to initialize the Cloud SDK. If you are attempting this lab in a different environment, make sure you have followed the procedures regarding installing the Cloud SDK.

Try to list the available firewall rules:
```
gcloud compute firewall-rules list
```
The output should look like this (do not copy; this is example output):
```
NAME                    NETWORK  DIRECTION  PRIORITY  ALLOW     DENY
allow-http-web-server   default  INGRESS    1000      tcp:80
default-allow-icmp      default  INGRESS    65534     icmp
default-allow-internal  default  INGRESS    65534     all
default-allow-rdp       default  INGRESS    65534     tcp:3389
default-allow-ssh       default  INGRESS    65534     tcp:22
```
This should work!

Try to delete the allow-http-web-server firewall rule:
```
gcloud compute firewall-rules delete allow-http-web-server
```
Enter Y, if asked to continue.
The output should look like this (do not copy; this is example output):

ERROR: (gcloud.compute.firewall-rules.delete) Could not fetch resource:
 - Required 'compute.firewalls.delete' permission for 'projects/[PROJECT_ID]/global/firewalls/allow-http-web-server'
This should not work!

As expected, the Network Admin role has permissions to list but not modify/delete firewall rules.


## Update service account and verify permissions
Update the Network-admin service account by providing it the Security Admin role.


In the Console, navigate to Navigation menu (mainmenu.png) > IAM & admin > IAM.

Find the Network-admin account. Focus on the Name column to identify this account.

Click on the pencil icon for the Network-admin account.

Change Role to Compute Engine > Compute Security Admin.

Click Save.

Return to the SSH terminal of the test-vm instance.

Try to list the available firewall rules:
```
gcloud compute firewall-rules list
```
The output should look like this (do not copy; this is example output):
```
NAME                    NETWORK  DIRECTION  PRIORITY  ALLOW     DENY
allow-http-web-server   default  INGRESS    1000      tcp:80
default-allow-icmp      default  INGRESS    65534     icmp
default-allow-internal  default  INGRESS    65534     all
default-allow-rdp       default  INGRESS    65534     tcp:3389
default-allow-ssh       default  INGRESS    65534     tcp:22
```
This should work!

Try to delete the allow-http-web-server firewall rule:
```
gcloud compute firewall-rules delete allow-http-web-server
```
Enter Y, if asked to continue.
The output should look like this (do not copy; this is example output):
```
Deleted [https://www.googleapis.com/compute/v1/projects/qwiklabs-gcp-00e186e4b1cec086/global/firewalls/allow-http-web-server].
```
This should work!

As expected, the Security Admin role has permissions to list and delete firewall rules.


## Verify the deletion of the firewall rule
Verify that you can no longer HTTP access the external IP of the blue server, because you deleted the allow-http-web-server firewall rule.

Return to the SSH terminal of the test-vm instance.

To test HTTP connectivity to blue's external IP, run the following command, replacing blue's external IP:
```
curl -c 3 <Enter blue's external IP here>
```
This should not work!

Press CTRL+c to stop the HTTP request.
Provide the Security Admin role to the right user or service account to avoid any unwanted changes to your firewall rules!
