# Creating-a-Highly-Available-3-Tier-Architecture-for-Web-Applications-in-AWS
Creating a Highly Available 3-Tier Architecture for Web Applications in AWS

A Five-Phase approach to launching a highly available and scalable cloud-based infrastructure for web application deployments

![3tier_AWS](https://user-images.githubusercontent.com/67044030/234254855-4120b123-403a-414f-bd93-632346d30b50.jpeg)

Businesses demand high availability, scalability, and security of the web applications. In this step-by-step tutorial, I am going to walk you through the structure and build of a standard 3-tiered cloud infrastructure


Prerequisites

A computer with internet access

AWS account with admin IAM access permissions

Extensive AWS Cloud Service Knowledge with IAM Permissions, VPC Structure, EC2 Instances and Services and Amazon DBS

Comfort with the AWS CLI



Phase 1: Foundations — Creating our VPC, Internet Gateway, NAT Gateway, Public Route Table, & Subnets

Creating our VPC — Navigate to the VPC dashboard in your AWS Console. Click “Create VPC”


We will give our VPC a name, leave the default IPv4 CIDR manual input, assign our own IPv4 CIDR, leave all other settings as default, then click “Create VPC”


If we were successful, then we will receive a green banner confirmation that informs us that our VPC was created successfully.

Internet Gateway — Next we will configure our internet gateway for our VPC by clicking the sub-menu link on the left side menu


Click “Create internet gateway”


We will give our internet gateway a name, then click “Create internet gateway”


If we were successful, then we will receive a green banner confirmation that informs us that our internet gateway was created successfully. Now we must attach our internet gateway to our VPC. We can do this by clicking the “Attach to VPC” link within our green banner confirmation.


We select the correct VPC and click “Attach internet gateway”.


If we were successful, then we will receive a green banner confirmation that informs us that our internet gateway was successfully attached to our VPC.


Public and Private Subnets — we need to create our six subnets-four of them will be public and two of them will be private. First, we navigate to the subnet dashboard by clicking the sub-menu link on the left side menu. Click “create subnet”.


From the “Create subnet” menu, we select our VPC and provide names, the availability zones and IPv4 CIDR blocks for all six of our subnets. Your values will be different. Finally, we click “create subnet” at the bottom.





If we were successful, then we will receive a green banner confirmation that informs us that our subnets we all created successfully. Next, we need to create a NAT gateway which will allow our private subnets to receive package updates via the public internet.

NAT Gateway — We navigate to the NAT gateway menu by clicking the sub-menu link on the left side menu. Click “NAT gateway”


From the NAT gateway menu, click “Create NAT gateway”.


From the NAT gateway menu, we give our NAT gateway a name, select our subnet to deploy it in, allocate an Elastic IP address, ensure the connectivity type is public. Finally, we click “create NAT gateway” at the bottom.


If we were successful, then we will receive a green banner confirmation that informs us that our NAT gateway was created successfully.


Public Route Table — We navigate to the route table menu by clicking the sub-menu link on the left side menu. Click “Route tables”.


From the route table menu, we give our route table a name, select our VPC, and then we click “create route table” at the bottom.


If we were successful, then we will receive a green banner confirmation that informs us that our route table was created successfully. We still need to edit our routes to ensure that public internet traffic which we want to reach our Public Subnets is routed correctly. We start by clicking “Edit routes”


Click “Add route” and we will add 0.0.0.0/0 as a destination and our target is our internet gateway. Finally, we click “save changes”.


If we were successful, then we will receive a green banner confirmation that informs us that our route table was updated successfully.


Lastly for our public route table, we need to explicitly associate our two public subnets with this route table. To do this, we select our route table from within our route table menu, click the “Subnet associations” table, then click “Edit subnet associations”.


We select our two public subnets that we want associated with out public route table, then click “save associations”.


If we were successful, then we will receive a green banner confirmation that informs us that we have successfully updated our subnet association with our route table.


We have successfully completed Phase one of our 3-Tier AWS architecture.



Phase 2: Creating our Web Tier

Web Tier Security Group -We start Phase 2 by creating the security group for our web server. We navigate via the security groups sub menu on the left side of our VPC dashboard and click, “Security Groups” to create a new security group.


We give our web-tier security group a name, a brief description, we select our VPC, and allow traffic in the following manner below. Finally, we click “Create security group”


If we were successful, we receive a green banner notification that tells us that our web tier security group was created successfully.

Web Tier Launch Template — To create this launch template, we navigate to the EC2 dashboard and within the left side sub menu, click “Launch templates”, then click “Create launch template”


We create a name for our template, a description, enable auto-scaling guidance, select our AMI, and architecture. Your values will be different.


We select our instance type, a key pair, if you wish, select our Web Server security group, and enable an auto-assign IPv4 address.


Next we are going to leave the storage volumes to default. We aren’t going to assign any resource tags. We are going to expand the “Advanced details” section and enter some bash code in our “User Data” field to ensure that our instances are provisioned with updated packages. Finally, we click “Create launch template”.


If our launch template was created successfully, we should receive a green banner notification informing us


Web Tier Auto Scaling Group, Load Balancer, and Target Group — While creating our ASG, we will be prompted to create a load balancer and target group, which we will also do in this step. We navigate to create an auto-scaling group via the EC2 dashboard. In the left side sub menu, scroll all the way down and click “Auto Scaling Groups”.



Let’s start working through the configuration. We give our ASG a name, select our launch template, then click “Next”.


In Step 2, we select our VPC and our two public subnets, then click “Next”.


In Step three, we are going to select “Attach a new load balancer”. Select Application load balancer and give it a name.

We will select “internet-facing for our scheme since this load balancer will be directing load traffic from the internet. We make sure our proper VPC and subnets are selected.

We ensure that our load balancer is listening on port: 80 and select our target group.


We will select elastic load balancing health checks for our EC2 instances and enable group metric collection for CloudWatch. Click “Next”.


In Step 4, we will set our desired, minimum, and maximum capacity of our EC2 instances. Your values maybe different. We aren’t going to select any scaling policies. We click “Next”.


We aren’t going to add any notifications. We click “Next”.


We aren’t going to add any tags. We click “Next”.


We can then review our Auto Scaling Group, if we wish, then click “Create Auto Scaling group”.


If the creation of our auto scaling group was created successfully, we should receive a green banner notification informing us.


Additionally, we can also confirm this by visiting our EC2 instance dashboard to see if our two EC2 instances were provisioned.


Now is a great time to ensure that we can access our Web Tier instances to ensure that we have configured out web tier correctly. before we build out our application and data tiers.


We can confidently move onto building our second tier.



Phase 3: Creating our Application Tier

In Phase 3, we need to allow accessibility of our web-tier with our application-tier, which means we will need to create a new security group for our application-tier to account for these nuances.

Application Tier Security Group — In the same way we created our tier-one security group, we navigate back to the VPC dashboard and select “Security Groups” and click “Create new security group”


We give our tier-2 security group a name, a brief description, we select our VPC, and this time we are going to enable inbound traffic from SSH, our tier-1 security group and ICMP so that we can ping out application server from the public internet so that we can test that traffic is routed correctly. Finally, we click, “Create security group”.


If the creation of our application tier security group was created successfully, we should receive a green banner notification informing us.


Next we can create the Launch Template for our Application Tier. Once again, we navigate back to the EC2 dashboard and select Launch Templates to start building.

There isn’t much we are changing in this template compared to the template that we built in our web tier.

We are going to select the security group for our application tier, which will allow our web servers to interact with our application servers.

Below is a screenshot where I highlight the differences in our application server templated when compared to our web tier server template. You values will be different.

When our settings are complete, we click “Create launch template”.


Application Tier Auto Scaling Group, Load balancer, and Target Group- Once again, we we click on the “Auto Scaling Groups” sub menu link within the EC2 Dashboard and fill in our parameters. Your values will be different.






If the creation of the auto scaling group of our application tier was successful, then we should receive a green banner notification in our ASG dashboard.

We can also confirm via our EC2 instances dashboard and see them being provisioned.


Creating Private Route Table -private route tables enable traffic between associated subnets, which is why we need to associate all six of our subnets with each other to link them together so that our different layers can route traffic between each other.

Let’s head back to the VPC dashboard. We give it a name, select our VPC, and click “Create route table”


In our Route Table dashboard, we select our private route table, click the subnet association tab, and click “Edit subnet associations”


We select all six of our subnets and click “Save associations”.


If our edits were successful, we will receive a green notifcation banner in our Route table dashboard.


Testing our Application Tier -Before moving onward to phase four and our database-tier, we want to ensure that our tier-2 infrastructure is working as intended. Since our 2nd tier is not internet facing, we can test this by trying to SSH into one of our tier-2 instances. We shouldn’t be able to and should receive a time out error.


Great! But can our tier-1 servers interact with our tier-2? To test this, we log into one of our tier-1 EC2 instances via SSH and run a ping command to a private IP address of our tier-2 servers.

Remember, you need to be logged into your web-tier instances to test this not via your local machine.



Phase 4: Creating our Data Tier

Finally, we need to construct our third and final tier, our data tier. This will serve as a RDB where the information needed for our application gets written, read, and stored. By this point, it’s “rinse, repeat”.

Data Tier Security Group — We start by configuring the security group for our data tier. Navigate to our VPC dashboard and in the left side sub menu, click “Security groups” and then click “create new security group”. We are going to give out data tier security group a name, a brief description, select our VPC, and edit the inbound rules.

For type, we are going to select “MySQL/Aurora”, protocol: TCP, Port 3306, source: custom, and select the security group from our application tier. We are going to leave our outbound rules as default and then click “Create security group”.


If our data tier security group was created successfully, then we will receive a green banner notification on our security group dashboard.


Creating our RDS Database — To create our database, we navigate to the RDS dashboard via the AWS console and click “Create database”


We are going to select “Standard create”, our engine type will be Amazon Aurora. We are going to select “Amazon Aurora MySQL-Compatible Edition” and “Production” for our template.


We are going to give our DB Cluster identifier a name, create a master username and password. We are going to select for memory optimized classes and our instance type. We are going to enable multi-AZ deployment per our architecture.


We don’t want to connect to an EC2 compute resource. We are going to select our VPC and not enable public access. We select our data tier security group and enable database port 3306.


We are going to require password authentication and run performance insights. Finally, we are going to click “Create database”.


It may take a few minutes for our DBS database to provision, but eventually, we should receive a green banner confirmation in our database dashboard showing a successful creation of our DBS database.


Next, we will click the “View connection details” link to retrieve and record the master username and password for our database table. Note: this is only displayed once so make sure you store your database credentials in a safe place.

We will also copy the endpoint for our database, which we will need in order to test the connection between our application-tier and data-tier in phase 5.



Now we need to connect our application instances with our database. From the RDS database dashboard, we select our database, click the actions sub menu and click “Set up EC2 connection”


We select the EC2 instance from our application tier and click “Continue”.


We can review and confirm by clicking, “Confirm and set up”



Phase 5: Final Testing of our 3-Tier Architecture

In our final phase, we will test the connection from our application tier instances to our database tier instances. First we must SSH into our application tier.

Once logged in, we will run the following CLI command:

mysql -h <Database End Point> -P 3306 -u <database username> -P <database password>
You will be prompted for your database password once more.

If successful, you will see a similar message confirming that we have connectivity between our application tier and database tier.


Success!

