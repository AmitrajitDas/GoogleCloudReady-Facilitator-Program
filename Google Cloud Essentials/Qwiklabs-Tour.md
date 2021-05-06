## Qwiklabs fundamentals

Features and components
Regardless of topic or expertise level, all Qwiklabs share a common interface. The lab that you're taking should look similar to this:

NoteYou are not taking the "Creating a Virtual Machine" lab shown in the image; it is used as an example to highlight common features across Qwiklabs.
Read the following lab component definitions, and then locate them in the interface.

Start Lab (button)
Clicking this button creates a temporary Google Cloud environment, with all the necessary services and credentials enabled, so you can get hands-on practice with the lab's material. This also starts a countdown timer.

Credit
The price of a lab. 1 Credit is usually equivalent to 1 US dollar (discounts are available when you purchase credits in bulk). Some introductory-level labs (like this one) are free. The more specialized labs cost more because they involve heavier computing tasks and demand more Google Cloud resources.

Time
Specifies the amount of time you have to complete a lab. When you click the Start Lab button, the timer will count down until it reaches 00:00:00. When it does, your temporary Google Cloud environment and resources are deleted. Ample time is given to complete a lab, but make sure you don't work on something else while a lab is running: you risk losing all of your hard work!

Score
Many labs include a score. This feature is called "activity tracking" and ensures that you complete specified steps in a lab. To pass a lab with activity tracking, you need to complete all the steps in order. Only then will you receive completion credit.

Paying for a lab
Some labs are free, but others require you to pay. For those, when you click the Start Lab button, a dialog gives you the choice to launch the lab with an access code or credits. If you don't have either, click Buy credits and follow the instructions.

buy_credit.gif

Reading and following instructions
This browser tab contains the lab instructions. When you start a lab, the lab environment—in this case, the Google Cloud Console user interface—opens in a new browser tab. You will need to switch between the two browser tabs to read the instructions and then perform the tasks. Depending on your physical computer setup, you could also move the two tabs to separate monitors.

## Accessing the Cloud Console
Start the lab
Now that you understand the key features and components of a lab, click Start Lab.
It may take a moment for the Google Cloud environment and credentials to spin up. When the timer starts counting down and the Start Lab button changes to a red End Lab button, everything is in place and you're ready to sign in to the Cloud Console.

Note Do not click the End Lab button until you have completed all the tasks in the lab. When you click the button, your temporary credentials are invalidated and you won't be able to access the work you've done throughout the lab. You must click this button when you finish; if you do not, you won't be able to take another lab. (Qwiklabs has protections in place to prevent concurrent enrollment.)
Connection Details pane
Now that your lab instance is up and running, look at the left pane. It contains an Open Google Console button, credentials (username and password), and a Project ID field.

Open_Google_Console.png

Note Your credentials will resemble but not match the image; every lab instance generates new temporary credentials.
Now examine each of these components.

Open Google Console
This button opens the Cloud Console: the web console and central development hub for Google Cloud. You will do the majority of your work in Google Cloud from this interface. All of the Google Cloud Qwiklabs use the Cloud Console in some form.

Project ID
A Google Cloud project is an organizing entity for your Google Cloud resources. It often contains resources and services; for example, it may hold a pool of virtual machines, a set of databases, and a network that connects them together. Projects also contain settings and permissions, which specify security rules and who has access to what resources.

A Project ID is a unique identifier that is used to link Google Cloud resources and APIs to your specific project. Project IDs are unique across Google Cloud: there can be only one qwiklabs-gcp-xxx...., which makes it globally identifiable.

Username and Password
These credentials represent an identity in the Cloud Identity and Access Management (Cloud IAM) service. This identity has access permissions (a role or roles) that allow you to work with Google Cloud resources in the project you've been allocated. These credentials are temporary and will only work for the access time of the lab. When the timer reaches 00:00:00, you will no longer have access your Google Cloud project with those credentials.

### Task 1: Sign in to Google Cloud
Now that you have a better understanding of the Connection Details pane, use its contents to sign in to the Cloud Console.

Click Open Google Console.
This opens the Google Cloud sign-in page in a new browser tab.

If you've ever signed in to a Google application like Gmail, this page should look familiar.

Tip Open the tabs in separate windows, side-by-side.

If the Choose an account page opens, click Use Another Account.Choose an account
Copy the Username from the Connection Details pane, paste it in the Email or phone field, and click Next.
Wait! Make sure to use the googlexxxxxx_student@qwiklabs.net email to sign in, NOT your personal or company email address!

The username that resembles googlexxxxxx_student@qwiklabs.net is a Google account that was created for your use as a Qwiklabs student. It has a specific domain name, which is "qwiklabs.net," and has been assigned IAM roles that allow you to access the Google Cloud Project that you have been provisioned.
Copy the Password from the Connection Details pane, paste it in the Password field, and click Next.

Click Accept to indicate your acknowledgement of Google's terms of service and privacy policy.

On the Protect your account page, click Confirm.

Because this is a temporary account, don't worry about updating recovery phone numbers or emails.

On the Welcome student! page, check Terms of Service to agree to Google Cloud's terms of service, and click Agree and continue.
You've successfully accessed the Cloud Console with your Qwiklabs credentials! Your page should now look like this:


### Task 2: View all projects
You actually have access to more than one Google Cloud project. In fact, in some labs you may be given more than one project in order to complete the assigned tasks.

In the Google Cloud Console title bar, next to your project name, click the drop-down menu.
In the Select a project dialog, click All. The resulting list of projects includes a "Qwiklabs Resources" project.

Do not switch over to the Qwiklabs Resources project at this point! However, you may use it later in other labs.
It's not uncommon for large enterprises or experienced users of Google Cloud to have dozens to thousands of Google Cloud projects. Organizations use Google Cloud in different ways, so projects are a good method for organizing cloud computing services (by team or product, for example.)

The "Qwiklabs Resources" project contains files, datasets, and machine images for certain labs and can be accessed from every Google Cloud lab environment. It's important to note that "Qwiklabs Resources" is shared (read only) with all Qwiklabs users, which means that you cannot delete or modify it.

The Google Cloud project that you are working with is temporary, which means that the project and everything it contains will be deleted when the lab ends. Whenever you start a new lab, you will be given access to one or more new Google Cloud projects, and there (not "Qwiklabs Resources") is where you will run all of the lab steps.



## Navigation menu and services
The Google Cloud Console title bar also contains a button labeled with a three-line icon:

818684f8e0d0a523.png

Clicking this button opens (or hides) the Navigation menu that provides quick access to Google Cloud's core services.

If the menu isn't open, click the button now and scroll through to see the types of services available:
5012e407e81c6d07.png

There are seven categories of Google Cloud services:

Compute: A variety of machine types that support any type of workload. The different computing options let you decide how much control you want over operational details and infrastructure.
Storage: Data storage and database options for structured or unstructured, relational or nonrelational data.
Networking: Services that balance application traffic and provision security rules.
Cloud Operations: A suite of cross-cloud logging, monitoring, trace, and other service reliability tools.
Tools: Services that help developers manage deployments and application build pipelines.
Big Data: Services that allow you to process and analyze large datasets.
Artificial Intelligence: A suite of APIs that run specific artificial intelligence and machine learning tasks on Google Cloud.
This link takes you to documentation that covers each of these categories in more detail.

Roles and permissions
Earlier you learned that, in addition to cloud computing services, Google Cloud also contains a collection of permissions and roles that define who has access to what resources. You can use the Cloud Identity and Access Management (Cloud IAM) service to inspect and modify these roles and permissions.

### Task 3: View your roles and permissions
On the Navigation menu (Navigation menu), click IAM & Admin. This opens a page that contains a list of users and specifies permissions and roles granted to specific accounts.

Find the "@qwiklabs" username you signed in with:
iam.png "Find Qwiklabs Member"

The Member column displays googlexxxxxx_student@qwiklabs.net (which matches the username you signed in with), and the Name column displays googlexxxxxx_student@qwiklabs.net student. The Role column displays Editor, which is one of three basic roles offered by Google Cloud. Basic roles set project-level permissions and, unless otherwise specified, control access and management to all Google Cloud services.

The following table pulls definitions from the roles documentation, which gives a brief overview of viewer, editor, and owner role permissions:

Role Name	Permissions
roles/viewer	Permissions for read-only actions that do not affect state, such as viewing (but not modifying) existing resources or data.
roles/editor	All viewer permissions, plus permissions for actions that modify state, such as changing existing resources.
roles/owner	All editor permissions and permissions for the following actions: manage roles and permissions for a project and all resources within the project; set up billing for a project.
As an editor, you can create, modify, and delete Google Cloud resources. However, you can't add or delete members from Google Cloud projects.



## APIs and services
Google Cloud APIs are a key part of Google Cloud. Like services, the 200+ APIs, in areas that range from business administration to machine learning, all easily integrate with Google Cloud projects and applications.

APIs are application programming interfaces that you can call directly or via our client libraries. Cloud APIs use resource-oriented design principles as described in our API Design Guide.

When Qwiklabs provisions a new Google Cloud project for a lab instance, it enables most APIs automatically so you can quickly start work on the lab's tasks. When you create your own Google Cloud projects outside of Qwiklabs, you will have to enable certain APIs yourself.

Most Cloud APIs provide you with detailed information on your project’s usage of that API, including traffic levels, error rates, and even latencies, which helps you quickly triage problems with applications that use Google services.

### Task 4: View available APIs
On the Navigation menu (Navigation menu), click APIs & Services > Library. The left pane, under the header CATEGORY, displays the different categories available.
apis.gif

In the API search bar, type Dialogflow, and then click Dialogflow API. The Dialogflow description page opens.
dialogflow.png

The Dialogflow API allows you to build rich conversational applications (e.g., for Google Assistant) without having to understand the underlying machine learning and natural language schema.

Click Enable. The Dialogflow description page opens.

Click the back button in your browser to verify that the API is now enabled.

enabled.png

Click Try this API. A new browser tab displays documentation for the Dialogflow API. Explore this information, and close the tab when you're finished.

To return to the main page of the Cloud Console, on the Navigation menu, click Home.

If you're interested in learning more about APIs, open the new hands-on tool in Google Cloud called Google APIs Explorer. Google also has a lab, APIs Explorer: Qwik Start, that will give you hands-on experience with the tool, using a simple example.




## Cloud Shell
Now that you understand the key features of Google Cloud and the Cloud Console, you will get hands-on practice with Cloud Shell. Cloud Shell is an in-browser command prompt execution environment that allows you to enter commands at a terminal prompt in order to manage resources and services in your Google Cloud project.

Cloud Shell lets you run all of your shell commands without leaving the Console and includes pre-installed command line tools.

Note: When an instruction tells you to run or execute a command, copy and paste (or type) the command into Cloud Shell at the end of the last line of text, and then press ENTER.
Work in Cloud Shell
In the Google Cloud Console title bar, click Activate Cloud Shell, and then click Continue if prompted.
819af82459550c67.png

After Cloud Shell is provisioned, a new pane opens at the bottom of the console with messages and prompts like this:

Welcome to Cloud Shell! Type "help" to get started.
Your Cloud Platform project in this session is set to qwiklabs-gcp-76ad0f1342e20013.
Use "gcloud config set project [PROJECT_ID]" to change to a different project.
gcpstaging23396_student@cloudshell:~ (qwiklabs-gcp-76ad0f1342e20013)$
You now have a Cloud Shell session up and running.

Run the following command and then click Authorize if prompted.

```
gcloud auth list

```
You should receive an output similar to this, where ACTIVE ACCOUNT is set to your Cloud IAM identity (gcpstagingxxxxx_student@qwiklabs.net):

Credentialed Accounts
ACTIVE  ACCOUNT
*       gcpstaging23396_student@qwiklabs.net
To set the active account, run:
    $ gcloud config set account `ACCOUNT`
As mentioned earlier, Cloud Shell is preinstalled with specific command line tools. The main Google Cloud toolkit is gcloud, which is used for many tasks on the platform, such as resource management and user authentication.

You just ran a gcloud command—auth list—which lists the credentialed accounts in your Google Cloud project. This account name matches the Qwiklabs username you used to sign in to the Cloud Console.

In addition to pre-installed toolkits, Cloud Shell also comes with the standard unix command line interface (CLI) tools and text editors like nano. You can use these to create and edit files inside Cloud Shell.

To create a file called test.txt, run the following touch command:
```
touch test.txt
```
touch won't generate any output.

Run the unix command ls to list the files in your current directory:
```
ls
```
You should receive the following output:

README-cloudshell.txt  test.txt
Your new test.txt file has been added to your working directory.

To edit a file, type nano, followed by the filename you want to edit in Cloud Shell:

nano test.txt
This will open the empty file in the Nano text editor:
```
nano.png
```

Type in a message like the following, but do not press ENTER:

Google Cloud and Qwiklabs are the best!
To save the file with your new message, press CNTRL+X, press Y, and then press ENTER.
Another helpful command is cat, which will output the contents of a file.

To confirm that your file was updated correctly, run the following command:
```
cat test.txt
```

The text you entered in step 6 should be displayed in your Cloud Shell session.

With just a few commands, you were able to create, edit, and output the contents of a file all in Cloud Shell (without having to leave your browser.)
