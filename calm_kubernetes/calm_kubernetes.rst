.. _calm_kubernetes:

-------------------------------------
Calm: Nutanix Karbon Kubernetes
-------------------------------------

*The estimated time to complete this lab is 60 minutes.*

Pre-requisite:
++++++++++++++

#.  This course assumes the trainees have basic knowledge of Calm and understand the constructs in Calm.

#.  **Python** knowledge would be an added advantage but not essential.


Product Configuration
+++++++++++++++++++++

#.  pc.2020.9.0.1

#.  Calm 3.1.0 onward

#.  Calm Project & Environment Setup


Provision Karbon Kubernetes Cluster from Calm Marketplace
+++++++++++++++++++++++++++++++++++++++++++++++++++++++++

#.	Click on the Market Place.  Click on the Kubernetes icon  

   .. figure:: images/Marketplace-kubernetes.png

#. Click on **Launch**.

   .. figure:: images/Marketplace-launch.png

#. Select the project.  Click on **Launch**.

   .. figure:: images/Marketplace-select-project.png

#. Key in the name of the application.  Click on **Create**

   .. figure:: images/Calm-Launch.png

#. Wait for an estimated 15 mins.  The Kubernetes cluster was created successfully

   .. figure:: images/Application-successful.png


Add a Karbon Kubernetes Provider in Calm
++++++++++++++++++++++++++++++++++++++++

#. Click on Setting.  Click on Providers

  .. figure:: images/Calm_setting2.png

#. Click on +Add Provider

  .. figure:: images/Calm_provider.png

#. Key in the following: 1st Kubernetes Master Node IP address.

  .. figure:: images/K8S_provider.png

#. Scroll down and key in the following:

   - **Type** - Kubernetes
   - **Type** - Karbon
   - **Cluster** - Select the Karbon Kubernetes cluster created earlier

  .. figure:: images/K8S_Provider_Contents.png

#. Click on **Save** and Verify

  .. figure:: images/Provider-Verification.png

Add the Karbon Kubernetes Provider to the Calm Project
......................................................

#. Click on **Project**.  Drill into your respective project

  .. figure:: images/Project.png

#. Click on **Select Provider**.  Select **Kubernetes**

  .. figure:: images/Project-Provider.png

#. Scroll down and click on **Save**.

  .. figure:: images/K8S_Added_To_Project.png


Deploy WordPress Application into Karbon Kubernetes Cluster
+++++++++++++++++++++++++++++++++++++++++++++++++++++++++++

Download Karbon Kubeconfig & Create Secret
..........................................

#. This section is to create the secret for the wordpress container to connect to the mysql container securely.  The yaml file contains a reference instead of the password.

#. The Karbon Kubernetes Cluster application also provison a kubectl client.  Click on **Application**.  Click on the **Kubernetes Cluster** name.  

#. Click on **Manage**. Click on **Download Kube Config** to download the Kubeconfig.  Click on **Run**

  .. figure:: images/K8S-App-Download-Kubeconfig.png

#. Check on the Audit section to ensure the latest Karbon Kubeconfig file was downloaded successfully.

  .. figure:: images/download-kubeconfig-audit.png

#. Click on **Service**. Click on **Open Terminal** 

  .. figure:: images/K8S-Cluster-Service.png

#. Run this command to get the Karbon Kubernetes cluster kube config.  Please remember to change the <karbon-cluster-name> to the Karbon Kubernetes cluster created earlier.

  .. literalinclude:: kubeconfig.sh

#. Create a secret named mysql-pass.  Run this command

  .. literalinclude:: create-secret.sh
  
  .. figure:: images/CreateSecret.png

Create a blueprint to deploy the Wordpress application
......................................................

#. Go to the blueprint.  Choose **Multi VM/Pod** Blueprint.

  .. figure:: images/MultiVM.png

#. Name the blueprint as Wordpress_User_Initial.  Choose your respective project.  Click on **Proceed**

  .. figure:: images/CreateBP.png

Create Variables in the blueprint
*********************************

#.  Click on the Application Profile->Default

  .. figure:: images/AppProfile.png

#.  On the right side of the screen, click on + to add a variable.  Add a variable named: namespace

  .. figure:: images/var_ns.png

#.  Click on **Show Additional Option**.  Scroll down and check on **Mark this variable mandatory**

  .. figure:: images/var_mandatory.png 

#.  Click on + to add another variable named: **MYSQL_PASSWORD**.  Check on **secret**.  Put the value as **Nutanix/4u**

  .. figure:: images/var_mysql.png

#.  Click on **Show Additional Option**.  Scroll down and check on **Mark this variable mandatory**

  .. figure:: images/var_mandatory.png 

Create the MySQL Pod
********************

#. Click on + to add a Pod

  .. figure:: images/new_pod.png

#.  Fill in the following.  Named it as **MYSQLAPP**.  It was important for the Pod Name to use this name because the subsequent yaml file would reference it.  Check on **Spec Editor**

  .. figure:: images/MYSQLAPP.png

#.  Click on **Edit**

  .. figure:: images/MYSQLAPP_Edit.png

#.  Click on “Pod Deployment Spec”.  Fill in the contents.

  .. figure:: images/MYSQLAPP-PodDeploy.png

  .. literalinclude:: MYSQLAPP-PodDeploy.sh
   

#. Click on the **container1**.  Fill in the contents.

  .. figure:: images/MYSQLAPP_Container.png

  .. literalinclude:: MYSQLAPP-Container.sh
    
#. Click on the Service Spec.  Fill in the contents.  Click on **Done**

  .. figure:: images/MYSQLAPP_Service.png

  .. literalinclude:: MYSQLAPP-Service.sh

      
 #.  Ensure the “Calm Published Service Name” is **MYSQLK8sPublishedService**.  It was important for the Service Name to use this name because the subsequent yaml file would reference it.

  .. figure:: images/MYSQLAPP-Service.png

#.  Click on **Save** to save the blueprint

Create the Wordpress pod
************************

#. Click on + to add a new pod

  .. figure:: images/new_pod.png

#. Named the pod: **WORDPRESSAPP**.  It was important for the Pod Name to use this name because the subsequent yaml file would reference it.  Check on **Spec Editor**.  Click on **Edit**. 

  .. figure:: images/WORDPRESSAPP.png 

#. Select the Pod Deployment Spec.  

  .. figure:: images/WORDPRESSAPP_Pod.png

  .. literalinclude:: WPAPP-Deploy.sh

#. Click on the **Container2**

  .. figure:: images/WORDPRESSAPP_Container.png

  .. literalinclude:: WPAPP-Container.sh
  
#. Click on the **Service** Spec

  .. figure:: images/WORDPRESSAPP_Service.png

  .. literalinclude:: WPAPP-Service.sh
 

#.  Click on **Save** to save the blueprint

Add Dependency to determine the sequence of execution
*****************************************************

#. The objective of the dependency was to define the sequence of execution.  Since the WordPress application was made up of both the application and mysql containers, the mysql container needs to be ready first.

#. Click on **Create Dependency** and link to MySQL as shown below

  .. figure:: images/Pod-Depend.png

#. Click on **Save** the blueprint

Launch the WordPress application into Kubernetes cluster
********************************************************

#.  Click on **Launch** to launch the blueprint

#.  Fill in the name and click on **Create**.

  .. figure:: images/LaunchWordPress.png

#. Wait for the application to be in **Running** State.

  .. figure:: images/WordPress_Running.png

Verify the Wordpress application
********************************

#.  Go to the Kubernetes application.  Expand the **Master**.  Choose the 1st Master Node.  Click on **Open Terminal**

  .. figure:: images/Wordpress_Service.png

#.  Run this command to ensure the **wordpress** and **wordpress-mysql** pods were started successfully

  .. figure:: images/WP_kubectl.png

#.  Run this command to identify the service for the wordpress application.

  .. figure:: images/WPSvc_kubectl.png

#.  Expand the Kubernetes worker node.  Note down the IP address

  .. figure:: images/Svc_Worker.png

#.  Open a new Chrome browser.  Key in the **Worker NodeIP address:NodePort** for wordpress.  In my example, it was http://10.38.207.64:32595.

  .. figure:: images/WP.png

Provision MetalLB into the Kubernetes Cluster
+++++++++++++++++++++++++++++++++++++++++++++

#.  The objective of this lab is to provision a MetalLB into the Kubernetes cluster.  The Wordpress application provisioned earlier did not have a public address.  It was using NodePort.  The subsequent Mountain Ranking Application was going to use LoadBalancer with public IP address.

#.  Click on the Calm MarketPlace Manager.  Drill into MetalLB.

  .. figure:: images/MarketPlaceMgr_MetalLB.png

#.  Add your project here.

  .. figure:: images/MetalLBJoinedProject.png

#.  Click on the Calm Market Place.  Click on MetalLB.

  .. figure:: images/MarketPlace-MetalLB.png

#.  Click on **Launch**

  .. figure:: images/MetalLB-launch.png

#.  Select your project.  Click on **Launch**

  .. figure:: images/MetalLB-Project.png

#.  Fill in the following:

  - **Name of the application** - 
  - **K8S Master Node IP ** - Find out the 1st Master Node IP address for your Kubernetes Cluster
  - **Start IP address** - Please check the IP address range was available before you defined the range.  Give a max of 3 IP address
  - **End IP address** - Please check the IP address range was available before you defined the range.

  .. figure:: images/MetalLB-launch2.png

#. The application was provisioned successfully.

  .. figure:: images/MetalLB-Succesful.png

#. Putty into the Kubernetes Master Node.  Run this command to ensure the MetalLB was installed successfully.

  .. figure:: images/kubectl-metallb.png
