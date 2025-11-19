# maven_web
# maven_java
***************************************************************************************************************************************************************
I.Steps for MavenJava Automation:
Maven Java Automation Steps:
 Step 1: Open Jenkins (localhost:8080)
   	 ├── Click on "New Item" (left side menu
Step 2: Create Freestyle Project (e.g., MavenJava_Build)
        	├── Enter project name (e.g., MavenJava_Build)
        	├── Click "OK"
└── Configure the project:
            		├── Description: "Java Build demo"
            		├── Source Code Management:
            			└── Git repository URL: [GitMavenJava repo URL]
            		├── Branches to build: */Main   or  */master
  		└── Build Steps:
               	     ├── Add Build Step -> "Invoke top-level Maven targets"
                  		└── Maven version: MAVEN_HOME
                 		└── Goals: clean
                	├── Add Build Step -> "Invoke top-level Maven targets"
                		└── Maven version: MAVEN_HOME
                		└── Goals: install
                	└── Post-build Actions:
                    	       ├── Add Post Build Action -> "Archive the artifacts"
                    			└── Files to archive: **/*
                    	     ├── Add Post Build Action -> "Build other projects"
                    			└── Projects to build: MavenJava_Test
                    			└── Trigger: Only if build is stable
                    	     └── Apply and Save


    └── Step 3: Create Freestyle Project (e.g., MavenJava_Test)
        	├── Enter project name (e.g., MavenJava_Test)
        	├── Click "OK"
              └── Configure the project:
             ├── Description: "Test demo"
             ├── Build Environment:
            		└── Check: "Delete the workspace before build starts"
            ├── Add Build Step -> "Copy artifacts from another project"
            		└── Project name: MavenJava_Build
            		└── Build: Stable build only  // tick at this
            		└── Artifacts to copy: **/*
            ├── Add Build Step -> "Invoke top-level Maven targets"
            		└── Maven version: MAVEN_HOME
            		└── Goals: test
            		└── Post-build Actions:
                ├── Add Post Build Action -> "Archive the artifacts"
                	└── Files to archive: **/*
                	└── Apply and Save

    └── Step 4: Create Pipeline View for Maven Java project
        ├── Click "+" beside "All" on the dashboard
        ├── Enter name: MavenJava_Pipeline
        ├── Select "Build pipeline view"         // tick here
         |--- create
        └── Pipeline Flow:
            ├── Layout: Based on upstream/downstream relationship
            ├── Initial job: MavenJava_Build
            └── Apply and Save OK

    └── Step 5: Run the Pipeline and Check Output
        ├── Click on the trigger to run the pipeline
        ├── click on the small black box to open the console to check if the build is success
            Note : 
1.If build is success and the test project is also automatically triggered with name       
                      “MavenJava_Test”
2.The pipeline is successful if it is in green color as shown ->check the console of the test project
3.The test project is successful and all the artifacts are archived successfully
II. Maven Web Automation Steps:
└── Step 1: Open Jenkins (localhost:8080)
    ├── Click on "New Item" (left side menu)
    
    └── Step 2: Create Freestyle Project (e.g., MavenWeb_Build)
        ├── Enter project name (e.g., MavenWeb_Build)
        ├── Click "OK"
        └── Configure the project:
            ├── Description: "Web Build demo"
            ├── Source Code Management:
            		└── Git repository URL: [GitMavenWeb repo URL]
            ├── Branches to build: */Main or master
            └── Build Steps:
                ├── Add Build Step -> "Invoke top-level Maven targets"
                	└── Maven version: MAVEN_HOME
                	 └── Goals: clean
                ├── Add Build Step -> "Invoke top-level Maven targets"
                	└── Maven version: MAVEN_HOME
                	└── Goals: install
                └── Post-build Actions:
                    ├── Add Post Build Action -> "Archive the artifacts"
                   	 └── Files to archive: **/*
                    ├── Add Post Build Action -> "Build other projects"
                    	└── Projects to build: MavenWeb_Test
                    	└── Trigger: Only if build is stable
                    └── Apply and Save

    └── Step 3: Create Freestyle Project (e.g., MavenWeb_Test)
        ├── Enter project name (e.g., MavenWeb_Test)
        ├── Click "OK"
        └── Configure the project:
            ├── Description: "Test demo"
            ├── Build Environment:
            		└── Check: "Delete the workspace before build starts"
            ├── Add Build Step -> "Copy artifacts from another project"
            		└── Project name: MavenWeb_Build
            		└── Build: Stable build only
            		└── Artifacts to copy: **/*
            ├── Add Build Step -> "Invoke top-level Maven targets"
           		└── Maven version: MAVEN_HOME
            		└── Goals: test
            └── Post-build Actions:
                ├── Add Post Build Action -> "Archive the artifacts"
                	└── Files to archive: **/*
                ├── Add Post Build Action -> "Build other projects"
                	└── Projects to build: MavenWeb_Deploy
                └── Apply and Save

    └── Step 4: Create Freestyle Project (e.g., MavenWeb_Deploy)
        ├── Enter project name (e.g., MavenWeb_Deploy)
        ├── Click "OK"
        └── Configure the project:
            ├── Description: "Web Code Deployment"
            ├── Build Environment:
            		└── Check: "Delete the workspace before build starts"
            ├── Add Build Step -> "Copy artifacts from another project"
            		└── Project name: MavenWeb_Test
            		└── Build: Stable build only
               	└── Artifacts to copy: **/*
            └── Post-build Actions:
                ├── Add Post Build Action -> "Deploy WAR/EAR to a container"
   └── WAR/EAR File: **/*.war
   └── Context path: Webpath
 └── Add container -> Tomcat 9.x remote
└── Credentials: Username: admin, Password: 1234
── Tomcat URL: https://localhost:8085/
                └── Apply and Save

    └── Step 5: Create Pipeline View for MavenWeb
        ├── Click "+" beside "All" on the dashboard
        ├── Enter name: MavenWeb_Pipeline
        ├── Select "Build pipeline view"
        └── Pipeline Flow:
            ├── Layout: Based on upstream/downstream relationship
            ├── Initial job: MavenWeb_Build
            └── Apply and Save

    └── Step 6: Run the Pipeline and Check Output
        ├── Click on the trigger “RUN” to run the pipeline
            Note: 
**************************************************************************************************************************************************************
General :
 	Description :-  provide the description of the project
	Build triggers:  here we can provide with build triggers of you choice
Advance project option:
	Definition: 
		Choose pipeline script
 
Here code for pipeline -script
	
Copy the code there
-------------------------------------------------------------------------------------------------------------------------
pipeline {
agent any  // Agent: Defines the machine (or slave) that runs the tasks 
   tools{
        maven 'MAVEN-HOME'
    }
    stages {         //The script is divided into stages, each representing a specific step, like building,
        stage('git repo & clean') {
            steps {
                //bat "rmdir  /s /q mavenjava"
                bat "git clone provide your github link"
                bat "mvn clean -f mavenjava"
            }
        }
        stage('install') {
            steps {
                bat "mvn install -f mavenjava" #project name#
            }
        }
        stage('test') {
            steps {
                bat "mvn test -f mavenjava"
            }
        }
        stage('package') {
            steps {
                bat "mvn package -f mavenjava"
            }
        }
    }
}
**************************************************************************************************************************************************************
Step 1: Install Prerequisites
Before installing Minikube, ensure the following are installed:
1.Virtualization Support:
oVerify virtualization is enabled: 
2.Hypervisor:
oMinikube supports multiple hypervisors (e.g., Hyper-V, VirtualBox, or Docker as a driver).
oInstall one of the following: 
Hyper-V (pre-installed on Windows 10/11 Pro or Enterprise).
Docker Desktop (if you want to use Docker as the driver).

Step 2: Download Minikube
1.Open a PowerShell or Command Prompt with administrator privileges.
2.Download the latest Minikube executable using this command:
3.curl -LO https://storage.googleapis.com/minikube/releases/latest/minikube-installer.exe
4.Install Minikube by running the installer:
5..\minikube-installer.exe

Step 3: Add Minikube to PATH
If Minikube is not automatically added to your PATH during installation:
1.Open System Properties → Environment Variables.
2.Add the directory where Minikube is installed (e.g., C:\Program Files\Minikube) to your PATH variable.
Step 4: Start Minikube
1.Open a terminal (PowerShell or CMD). Do the following commands
2.Start Minikube with a specified driver (e.g., Hyper-V, Docker, or VirtualBox). For example:
oHyper-V: 
 minikube start --driver=hyperv
oDocker: 
 minikube start --driver=docker
3.Verify Minikube is running:
4.minikube status

Step 5: Interact with Minikube

kubectl is a command-line tool used in Kubernetes to interact with and manage Kubernetes clusters.
Once Minikube is running:
1.Use kubectl to interact with the cluster.
oInstall kubectl if not already installed: 
 minikube kubectl -- get pods -A
oOr download it separately from the official Kubernetes site.
2.Open the Minikube dashboard (optional):
3.minikube dashboard

Optional: Check Your Installation
Run the following to verify the installation:
Minikube version
	kubectl version --client

Troubleshooting
1.If Minikube fails to start:
oEnsure your hypervisor (Hyper-V/Docker/VirtualBox) is installed and running.
oCheck the Minikube logs: 
 minikube logs
2.Updating Minikube:
3.minikube update-check
4.minikube update

Minikube Automation Steps
Step 1: Start Minikube Cluster
Open your terminal and run the command:
minikube start
Step 2: Create and Manage Deployment
1.Create  an application in Kubernetes:

oCommand:
kubectl create deployment mynginx --image=nginx

if already created then 

kubectl set image deployment/myngnix nginx=nginx:latest

oVerify the deployment using: Kubernetes responds by showing you a list that includes the names of your deployment groups
kubectl get deployments
Ensure mynginx appears in the output.
Check the following commands:
kubectl get pods
kubectl describe pods
2.Expose Deployment as a Service:
oCommand:
kubectl expose deployment mynginx --type=NodePort --port=80 --target-port=80
Step 3: Scale the Deployment
Command:Scales the Nginx deployment to 4 replicas (pods).
kubectl scale deployment mynginx --replicas=4
kubectl get service myngnix
Step 4: Access the Nginx App
1.Using Port Forwarding:
oCommand:
kubectl port-forward svc/mynginx 8081:80

Access the app via http://localhost:8081.
If Error, use this option, Using Minikube Tunnel:
oStart the tunnel:
minikube tunnel
oRetrieve the service URL:
minikube service mynginx --url
oOpen the provided URL in your browser.
oOpen the kubernets dashboard
Open the minikube dashboard	
Minikube dashboard
Step 5: Stop and Clean Up
1.Stop Nginx Deployment:
oCommands:
kubectl delete deployment mynginx
kubectl delete service mynginx
2.Stop Minikube (Optional):
oCommand:
minikube stop
3.Delete Minikube Cluster (Optional):
oCommand:minikube delete
Nagios Automation Steps
Step 1: Pull the Nagios Docker Image
Open a terminal and run:
docker pull jasonrivers/nagios:latest
Step 2: Run Nagios
Command:
docker run --name nagiosdemo -p 8888:80 jasonrivers/nagios:latest
Step 3: Access Nagios Dashboard
Open your browser and navigate to:
http://localhost:8888
oLogin Credentials:
Username: nagiosadmin
Password: nagios
oOnce logged in, explore:
Hosts: View systems being monitored.
Services: Check tasks being monitored (e.g., CPU usage).
Alerts: Access recent notifications.
Step 4: Monitoring Host Details
1.Navigate to the Host Information Page:
oSelect a host from the Hosts menu.
2.Key Details:
oHost Status: Indicates if the system is UP or DOWN.
oMetrics: View CPU usage, memory status, and network activity.
oActions: Reschedule checks, disable notifications, or schedule downtime.
Step 5: Stop and Remove Nagios
1.Stop the Container:
oCommand:
docker stop nagiosdemo
2.Delete the Container:
oCommand:
docker rm nagiosdemo
3.Remove the Image (Optional):
oList images:
docker images
oDelete the Nagios image:
docker rmijasonrivers/nagios:latest

4. Observe the docker containers in DockerHub, we can see the latest Nagios Installed running on port:8888
**************************************************************************************************************************************************************
Setting Up  Jenkins CI------using GitHub Webhook with Jenkins 

Step 1: Configure Webhook in GitHub
1.Go to your GitHub repository.
2.Navigate to Settings → Webhooks.
3.Click “Add webhook”.
4.In the Payload URL field:
oEnter the Jenkins webhook URL in the format:
http://<jenkins-server-url>/github-webhook/
Note: If Jenkins is running on localhost, GitHub cannot access it directly.
Use ngrok to expose your local Jenkins to the internet:

ongrok.exe http <Jenkins local host:8080>
Use the generated ngrok URL, e.g.:
http://abc123.ngrok.io/github-webhook/
5.Set Content type to:
application/json
6.Under “Which events would you like to trigger this webhook?”, select:
o Just the push event
7.Click “Add webhook” to save.

 Step 2: Configure Jenkins to Accept GitHub Webhooks
1.Open Jenkins Dashboard.
2.Select the job (freestyle or pipeline) you’ve already created.
3.Click Configure.
4.Scroll down to the Build Triggers section.
5.Check the box: ✅GitHub hook trigger for GITScm polling
6.Click Save.

 Step 3: Test the Setup
1.Make any code update in your local repo and push it to GitHub.
2.Once pushed, GitHub will trigger the webhook.
3.Jenkins will automatically detect the change and start the build pipeline.

outcome
You’ve successfully connected GitHub and Jenkins using webhooks.
Every time you push code to GitHub, Jenkins will automatically start building your project without manual intervention.

Set-uping the ngrok

How to Install and Use ngrok

Step 1. Download ngrok
https://ngrok.com/download
Download and extract it for your OS (Windows, macOS, or Linux).

Step 2. Connect Your ngrok Account (optional but useful)
After you sign up (free), ngrok gives you an auth token.
CREATE AUTHENTICATOR [https://dashboard.ngrok.com/get-started/your-authtoken]
Run this command (replace <your_token> with yours):
ngrok config add-authtoken <your_token>
This ensures stable sessions and more control.

Step 3. Start a Tunnel for Jenkins

Assuming Jenkins runs locally on port 8085:
ngrok http 8085
You’ll see output like:

Session Status                online
Forwarding                    https://1234abcd.ngrok.io -> http://localhost:8080
Copy the HTTPS URL (https://1234abcd.ngrok.io) — this is your public Jenkins URL for webhooks.
Step 4. Use it in GitHub Webhook

In your GitHub repo → Settings → Webhooks:
Payload URL: 		 [paste the url generated by ngrok]

https://1234abcd.ngrok.io/github-webhook/    [please include this – remaining all default]
Now, whenever you push code, GitHub sends an event to that URL, which ngrok forwards to your local Jenkins.

------------------------------------------------------------------------------------------------------------------------------------------
Setting Up   Jenkins Email Notification Setup (Using Gmail with App Password)
Creation of app password
1. Gmail: Enable App Password (for 2-Step Verification)
i. Go to: https://myaccount.google.com
ii. Enable 2-Step Verification
Navigate to:
oSecurity → 2-Step Verification
oTurn it ON
oComplete the OTP verification process (via phone/email)
iii. Generate App Password for Jenkins
Go to:
oSecurity → App passwords
Select:
oApp: Other (Custom name)
oName: Jenkins-Demo
Click Generate
Copy the 16-digit app password
oSave it in a secure location (e.g., Notepad)
                 2.  Jenkins Plugin Installation
i. Open Jenkins Dashboard
ii. Navigate to:
Manage Jenkins → Manage Plugins
iii. Install Plugin:
Search for and install:
oEmail Extension Plugin

3. Configure Jenkins Global Email Settings
i. Go to:
Manage Jenkins → Configure System

A. E-mail Notification Section
Field	Value
SMTP Server	smtp.gmail.com
Use SMTP Auth	✅ Enabled
User Name	Your Gmail ID (e.g.,bhargavikumbham@gmail.com)
Password	Paste the 16-digit App Password
Use SSL	✅ Enabled
SMTP Port	465
Reply-To Address	Your Gmail ID (same as above)
➤ Test Configuration
Click: Test configuration by sending test e-mail
Provide a valid email address to receive a test mail
✅ Should receive email from Jenkins

B. Extended E-mail Notification Section
Field	Value
SMTP Server	smtp.gmail.com
SMTP Port	465
Use SSL	✅ Enabled
Credentials	Add Gmail ID and App Password as Jenkins credentials
Default Content Type	text/html or leave default
Default Recipients	Leave empty or provide default emails
Triggers	Select as per needs (e.g., Failure)

4.  Configure Email Notifications for a Jenkins Job
i. Go to:
Jenkins → Select a Job → Configure

ii. In the Post-build Actions section:
Click: Add post-build action → Editable Email Notification
A. Fill in the fields:
Field	Value
Project Recipient List	Add recipient email addresses (comma-separated)
Content Type	Default (text/plain) or text/html
Triggers	Select events (e.g., Failure, Success, etc.)
Attachments	(Optional) Add logs, reports, etc.

iii. Click Save

 Now your Jenkins job is set up to send email notifications based on the build status!
**************************************************************************************************************************************************************
Deploying an application into cloud
Steps for Deploying  application into the cloud
I.Create application and  Push into github
II.Create the virtual machine and connect to it.
III.Clone the application from github, Write the Dockerfile
IV.Create the image
V.Run the image and access it public ip of virtual machine


I.   Create Maven-web-java project in eclipse & push into github

II.Create the virtual machine (EC2--instance) in aws and connect to 
Using Amazon EC2 eliminates your need to invest in hardware up front, so you can develop and deploy applications faster. 
Ex : Launch ubuntu instnace
Step 1: Login to AWS /canvas account
Step 2: Services --  EC2
Step 3: Choose region which is near ?  
	Services -- EC2 --- Launch Instance 
	Stage 1  --Name (Giving name to the machine) ubuntu
	Stage 2  -- Select AMI  ( Note: Select free tier eligible ) ubuntu server
	Stage 3   --  Architecture as 64-bit	  
	Stage 4  --  Instance type ----  t2.micro(default 1 CPU,1 GB RAM)
	Stage 5  --  Create a new keypair---a keypair will downloaded  with extension .pem
                              Store key in folder AWS
	Stage 6  -- Network Setting ----Create Security group  --  ( It deals with ports )
	(Note for understanding We have 0 to 65535 ports. Every port is dedicated to special purpose)		
Do this step : HERE select http and https
   Stage 7 -- Storage - 8GB ( Observation - we have root - it is same as C Drive)
         Stage 8 --- click on launch instance
    Stage 9: Number of instances ---1
+++++++++++++++++++++++++++++++
Observation - One machines created
Do this step:---once it is created select that instance and click on connect
Here copy the ssh – i command from SSH client connect tab
We can use powershell /gitbash /webconsole , to connect to ubuntu machine.
NOTE:- cd path of AWS folder // change path
To connect to above terminals we need to go into the path of the keypair.and 
paste  the
 ssh -i command from the aws console

IV .Clone the application from github, Write the Dockerfile
 once connected to instance

Step 1:-  install the docker
	install docker ---apt-get update
-                            		     apt-get install docker.io
                            apt-get install nano


step 2:-  git clone <paste the github link of maven-web-java project>
step 3:- navigate to the maven-web-java project

VI. Create the image

Step 4:-  nano Dockerfile 




V.  Run the image and access it with public ip of virtual machine

Step 1:- build your image 
	docker build –t <imagename> .(dot)
Step 2:- check for images
Step 3:- run image
	docker  run –d  --name app-demo –p 6060:8080 <image name>
Step:-4 Accessing the app by public ip of virtual machine
Note:-if your are not able to connect change the inbound rules..
