# Autoscaling Groups with Load balancer
In this project you are configuring three autoscaling groups across multiple availability zones with an application load balancer. The load balancer ensures traffic is evenly distributed to healthy instances, while autoscaling dynamically adjusts capacity for high availability, fault tolerance, and scalability.

![Infra](https://github.com/iam-avinash-jagtap/Web-Application_Hosting_using-ASG-LB/blob/master/Images/Infrastructure.png)

## Learning Objective
_Upon the completion of the project you will be able to deploy the infrastructure of healthy instances and dynamically adjust high availability and scalability._
- Autoscaling groups
- Load balancer
- Listener rules
- Launch templates
- User-data script
- Target groups
## Prerequisites
- Strong knowledge of Elastic Compute Cloud (EC2) service.
- Understanding of autoscaling and load balancing concepts.
- Conceptual understanding of user-data script.
- Understanding of web application architecture.
- Knowledge of networking concepts (VPC, security groups).
# Step 1:- Logging in to the Amazon Web Services Console
Login to your AWS account using your AWS credentials
# Step 2:- Creating Security Groups
_You will configure security groups at the creation of launch templates but to skip this every time process you need to create your security group firstly and then you can select the existing security group_
- In the AWS management console search bar, search EC2 and click on EC2 service. 
- Click on the 'Security Groups' option -- which is on the right side of the console in 'Network & Security' section.
1. Click on 'create security group'
2. Enter Security group name -  Bikewale-SG
3. Enter description -- This Security group allows port - SSH-22, HTTP-80, HTTPS-443
4. In Inbound rules - click on 'Add rule'
    - Select type - SSH         Source - Custom     anywhere - 0.0.0.0/0
    - Select type - HTTP        Source - Custom     anywhere - 0.0.0.0/0
    - Select type - HTTPS       Source - Custom     anywhere - 0.0.0.0/0
5. In Outbound rules - by default all traffic is allowed, which means no need to change anything.
6. Click on - Create security group

![Security-Group](https://github.com/iam-avinash-jagtap/Web-Application_Hosting_using-ASG-LB/blob/master/Images/Security%20Group.png)

# Step 3:- Creating Launch Templates
_In this project you are going to host web application through configure autoscaling groups and load balancer, so for the autoscaling groups you need to create templates of your web pages of the application using 'Launch templates'_
- click on the 'Launch templates'
- _you needs to create 3 launch templates for your web application._ 
### This template is for home page of the web application "Bikewale"
1. Click on 'Create launch template'
2. Enter the name of Launch template - Bikewale-LT
3. Enter description of template for your understanding.
4. Select OS image from Quick start - Amazon Linux
5. Select Instance type - t2.micro
6. Select key pair
7. in Network settings 
    - Subnet - When you specify a subnet, a network interface is automatically added to your template.
    - Firewall - You needs to select existing Security group which is creates by you -- Bikewale-SG
8. in Advanced details 
    - User-data - You need to pass user data script of your web application's home page that is 'Bikewale'
    - Script:-
        #!/bin/bash
        yum update -y
        yum install -y httpd
        systemctl start httpd
        systemctl enable httpd
        cat <<EOL > 
        /var/www/html/index.html
        <!DOCTYPE html>
        <html lang="en">
        <head>
        <meta charset="UTF-8">
        <meta name="viewport" content="width=device-width, initial-scale=1.0">
        <title>Bikewale</title>
        </head>
        <body>
        <h1>Welcome to Bikewale</h1>
        <p>Bikewale provides comprehensive information about bikes in the Indian market. Here you can find details about various bike models, features, and specifications tailored to the Indian consumer market.</p>
        <button onclick="location.href='/bmw'">BMW</button>
        <button onclick="location.href='/ktm'">KTM</button>
        </body>
        </html>
        EOL

9. Click on - Create launch template
#### Your Bikewale (Home page) template is ready
### Follow same process for Next Two Templates 
_In your web application there are three pages which are deploy into 3 different autoscaling groups, in that first template is created, now follow the same process and create 2 templates_
### First - BMW-LT
#### Script - 
    #!/bin/bash
    yum update -y
    yum install -y httpd
    systemctl start httpd
    systemctl enable httpd
    mkdir -p /var/www/html/bmw
    cat EOL  /var/www/html/bmw/index.html
    !DOCTYPE html>
    <html lang="en">
    <head>
    <meta charset="UTF8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>BMW Bikes</title>
    </head>
    <body>
    <h1BMW Bikes in India</h1
    <p>The history of BMW bikes is rich and innovative, known for premium performance, advanced 
    technology, and outstanding design. Here is a list of BMW bikes available in India with their power 
    and torque specifications:</p>
    <ul>
    <li>BMW G310R  Power: 33.5 HP, Torque: 28 Nm</li>
    <li>BMW F900R  Power: 105 HP, Torque: 92 Nm</li>
    <li>BMW S1000RR  Power: 205 HP, Torque: 113 Nm</li>
    ! Add more models as needed ⟶
    </ul>
    <button onclick="location.href='/'"Back to Bikewale</button>
    </body>
    </html>
    EOL
### Second - KTM-LT
#### Script -
    #!/bin/bash
    yum update -y
    yum install -y httpd
    systemctl start httpd
    systemctl enable httpd
    mkdir -p /var/www/html/ktm
    cat EOL  /var/www/html/ktm/index.html
    !DOCTYPE html>
    <html lang="en">
    <head>
    <meta charset="UTF8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>KTM Bikes</title>
    </head>
    <body>
    <h1KTM Bikes in India</h1
    <p>KTM, known for high-performance and stylish motorcycles, has a legacy of innovation and 
    racing excellence. Here is a list of KTM bikes available in India with their power and torque 
    specifications:</p>
    <ul>
    <li>KTM Duke 200  Power: 25 HP, Torque: 19.3 Nm</li>
    <li>KTM RC 390  Power: 44 HP, Torque: 37 Nm</li>
    <li>KTM Adventure 390  Power: 43 HP, Torque: 37 Nm</li>
    ! Add more models as needed ⟶
    </ul>
    <button onclick="location.href='/'"Back to Bikewale</button>
    </body>
    </html>
    EOL

![Launch-Templates](https://github.com/iam-avinash-jagtap/Web-Application_Hosting_using-ASG-LB/blob/master/Images/Launch%20Templates.png)

#### All 3 Launch templates are created
# Step 4:- Create Auto Scaling Groups
_After the creation of launch templates you are ready to configure auto scaling groups using launch templates_
_you are going to create 3 different types of the auto scaling groups Static-Bikewale, Dynamic-BMW, Scheduled-KTM_
### Bikewale Auto Scaling group - Static
1. Go to EC2 dashboard
2. Click on - Auto Scaling Groups
3. Click on - Create Auto Scaling group
4. Enter Auto Scaling group name - Bikewale-ASG
5. Select Launch template - Bikewale-LT
 #### Next
6. Select Availability Zones and subnets 
7. Select Availability Zone distribution - Balanced best effort
 #### Next
8. Integrate with other services - Skip for now
9. Enter Health check grace period - 300 seconds (recommended)
#### Next
10. Group size- Enter desired capacity - 3
11. Scaling - Min desired capacity-2 & Max desired capacity-4
12. Automatic scaling policy - No scaling policies (This ASG is Static so you must select No scaling policies)

    _// after this you can skips next steps and directly click on skip to review._
13. Click on - Skip to review
14. Review your entered details and configurations
15. Click on - Create Auto Scaling group

    _//After few seconds you can check your desired capacity instances are in running state_
### BMW Auto Scaling Group - Dynamic
1. Refresh Auto Scaling Groups
2. Click on - Auto Scaling Groups
3. Click on - Create Auto Scaling group
4. Enter Auto Scaling group name - BMW-ASG
5. Select Launch template - BMW-LT
 #### Next
6. Select Availability Zones and subnets 
7. Select Availability Zone distribution - Balanced best effort
 #### Next
8. Integrate with other services - Skip for now
9. Enter Health check grace period - 300 seconds (recommended)
#### Next
10. Group size- Enter desired capacity - 3
11. Scaling - Min desired capacity-2 & Max desired capacity-4
12. Automatic scaling policy- Target tracking scaling policy (This ASG is Dynamic so you must select Target tracking scaling policy)
    - Enter Scaling policy name - Target tracking policy
    - Select Metric type - Average CPU utilization
    - Target value - 50
    - Instance warmup time - 300 seconds

    _// after this you can skips next steps and directly click on skip to review._
13. Click on - Skip to review
14. Review your entered details and configurations
15. Click on - Create Auto Scaling group

    _//After few seconds you can check your desired capacity instances are in running state_
### KTM Auto Scaling Groups - Scheduled
1. Refresh Auto Scaling Groups
2. Click on - Auto Scaling Groups
3. Click on - Create Auto Scaling group
4. Enter Auto Scaling group name - KTM-ASG
5. Select Launch template - KTM-LT
 #### Next
6. Select Availability Zones and subnets 
7. Select Availability Zone distribution - Balanced best effort
 #### Next
8. Integrate with other services - Skip for now
9. Enter Health check grace period - 300 seconds (recommended)
#### Next
10. Group size- Enter desired capacity - 3
11. Scaling - Min desired capacity-2 & Max desired capacity-4
12. Automatic scaling policy- Target tracking scaling policy (This ASG is Scheduled so you needs first select Target tracking scaling policy)
    - Enter Scaling policy name - Target tracking policy
    - Select Metric type - Average CPU utilization
    - Target value - 50
    - Instance warmup time - 300 seconds

    _// after this you can skips next steps and directly click on skip to review._
13. Click on - Skip to review
14. Review your entered details and configurations
15. Click on - Create Auto Scaling group

    _//After few seconds you can check your desired capacity instances are in running state_

![Instances](https://github.com/iam-avinash-jagtap/Web-Application_Hosting_using-ASG-LB/blob/master/Images/All%20Instances.png)

_//This ASG is Scheduled so you need to make Scheduled it_
    - Refresh Auto Scaling Groups
    - Open KTM-ASG 
    - Open section - Automatic Scaling
    - Select Scheduled actions
    - Click on Create Scheduled action
        - Enter event name - New-Year-Online-sale-KTM
        - Enter Desired capacity - 10
        - Min desired capacity- 8 & Max desired capacity-15
        - Select Recurrence - Once (Once because this new year sale is only one time)
        - Select Time zone - Asia/kolkata
        - Enter Specific start time - 2025/03/01 : 00:00 
        - Click on - Create

![Scheduled](https://github.com/iam-avinash-jagtap/Web-Application_Hosting_using-ASG-LB/blob/master/Images/Scheduled-%20ASG%20-%20KTM.png)

        _//KTM-ASG is successfully scheduled for the new year sale._

![Auto-Scaling-Groups](https://github.com/iam-avinash-jagtap/Web-Application_Hosting_using-ASG-LB/blob/master/Images/ASG.png)

_All three Auto Scaling groups are configuring successfully._
# Step 5:- Create Target Groups
_After the creation of Auto Scaling groups you create load balancer but before that you need to create target groups to define a set of targets where traffic is routed._
### Create 3 Target groups to attach to 3 Auto Scaling groups
### 1. Bikewale -Target Group
1. Go to EC2 dashboard
2. Click on - Target Groups
3. Click on - Create target group
4. In basic configuration
    - Choose a target type - Instances
    - Target group name - Bikewale-TG
    - Protocol: Port - 80
    - Health check protocol - HTTP (This is home page, no need to enter path)
5. In register targets - Do not select instances because target is auto-scaling groups.
6. Click on - Create target group
### 2. BMW -Target Group
1. Go to EC2 dashboard
2. Click on - Target Groups
3. Click on - Create target group
4. In basic configuration
    - Choose a target type - Instances
    - Target group name - BMW-TG
    - Protocol: Port - 80
    - Health check protocol - HTTP
    - Health check path - /bmw
5. In register targets - Do not select instances because target is auto-scaling groups.
6. Click on - Create target group
### 3. KTM -Target Group
1. Go to EC2 dashboard
2. Click on - Target Groups
3. Click on - Create target group
4. In basic configuration
    - Choose a target type - Instances
    - Target group name - BMW-TG
    - Protocol: Port - 80
    - Health check protocol - HTTP
    - Health check path - /ktm
5. In register targets - Do not select instances because target is auto-scaling groups.
6. Click on - Create target group

![Target-Groups](https://github.com/iam-avinash-jagtap/Web-Application_Hosting_using-ASG-LB/blob/master/Images/Target%20Groups.png)

### Target groups are successfully created
# Step 6:- Assign Target Groups With Auto Scaling Groups
### Bikewale-ASG
1. Go to the auto-scaling groups 
2. Select Bikewale-ASG , go to action and Select Edit action
3. Scroll down and go to the Load balancing section 
4. click on - Application, Network or Gateway Load Balancer target groups
5. Select- Bikewale-TG

![Home=ASG+TG](https://github.com/iam-avinash-jagtap/Web-Application_Hosting_using-ASG-LB/blob/master/Images/Bikewale-TG%2BASG.png)

6. Click on - Update
#### Bikewale Target group assigned with Bikewale auto-scaling group
##### Follow same process for BMW-ASG & KTM-ASG 
### BMW-ASG

![BMW=ASG+TG](https://github.com/iam-avinash-jagtap/Web-Application_Hosting_using-ASG-LB/blob/master/Images/BMW-TG%2BASG.png)

### KTM-ASG

![KTM=ASG+TG](https://github.com/iam-avinash-jagtap/Web-Application_Hosting_using-ASG-LB/blob/master/Images/KTM-TG%2BASG.png)

#### Now you are ready to configure Load Balancer
# Step 7:- Configure Application Load Balancer
_After create all those resources now you need to create an application load balancer and add listener rules in that_
1. Go to EC2 dashboard
2. Click on - Load balancers
3. Click on - Create load balancer
4. Select type of load balancer - Application load balancer
5. Click on Create
6. In basic configuration
    - Enter Load balancer name - Bikewale-LB
    - Select Scheme - Internet facing
    -  Select load balancer IP address type - IPv4
7. In Network mapping 
    - Select your VPC
    - Select Availability Zones mapping
8. Select Security Group - Bikewale-SG
9. In listeners and routing - you have one default lister - HTTP:80 -- Forwards to - Bikewale-TG(It will be your home page)
10. Click on - Create load balancer

![Load-Balancer](https://github.com/iam-avinash-jagtap/Web-Application_Hosting_using-ASG-LB/blob/master/Images/Load%20Balancer.png)

#### Application Load Balancer created successfully
# Step 8:- Configure Listener Rules
_Bikewale listener is configured at the creation of load balancer so now you need to create rule for BMW and KTM page_
### BMW
1. Open load balancer - Bikewale-LB
2. Go to Listener rules and the open default rule
3. Click on - Add rule
4. Enter Name and tags - BMW
#### Next
5. Add Condition - Rule condition types- Path
6. Mention path for route traffic :- Path - /bmw/* & Confirm
#### Next
7. Select action types routing actions - Forward to target groups
8. Select target group - BMW-TG
#### Next
9. Give listener rule priority - 1
10. Review and Create - check all details and click on - Create
### KTM
1. Open load balancer - Bikewale-LB
2. Go to Listener rules and open default rule
3. Click on - Add rule
4. Enter Name and tags - KTM
#### Next
5. Add Condition - Rule condition types- Path
6. Mention path for route traffic :- Path - /ktm/* & Confirm
#### Next
7. Select action types routing actions - Forward to target groups
8. Select target group - KTM-TG
#### Next
9. Give lisetner rule priority - 2
10. Review and Create - check all details and click on - Create

![Listener-rules](https://github.com/iam-avinash-jagtap/Web-Application_Hosting_using-ASG-LB/blob/master/Images/Listner%20Rules.png)

#### Listener rules configured successfully
# Step 9:- Test Your Infrastructure
_After Configure all those things now you are ready to test your web application using Bikewale-LB's DNS._
1. Open load balancer - Bikewale-LB
2. Copy DNS name of - Bikewale-LB (eg. bikewale-lb-xxxxxxxxxx.us.west-1b.elb.amazonaws.com)
3. Open new tab - Paste Copied DNS name

![Home-Page](https://github.com/iam-avinash-jagtap/Web-Application_Hosting_using-ASG-LB/blob/master/Images/Bikewale%20Output.png)

4. Enter path for BMW - (eg. bikewale-lb-xxxxxxxxxx.us.west-1b.elb.amazonaws.com/bmw/ )

![BMW-Page](https://github.com/iam-avinash-jagtap/Web-Application_Hosting_using-ASG-LB/blob/master/Images/BMW%20Output.png)

5. Enter path for KTM - (eg. bikewale-lb-xxxxxxxxxx.us.west-1b.elb.amazonaws.com/ktm/ )

![KTM-Page](https://github.com/iam-avinash-jagtap/Web-Application_Hosting_using-ASG-LB/blob/master/Images/KTM%20%20Output.png)

# Summary
In this project, you Configured 3 Auto scaling groups and Application load balancer in that  An auto-scaling group for an application load balancer is the configuration to make a web app highly available, fault-tolerant, and scalable. Three auto-scaling groups- static, dynamic, and scheduled-are created in three availability zones each associated with specific launch templates having user-data scripts to accommodate different application pages (Bikewale, BMW, and KTM).
an Application load balancer distributes incoming traffic across healthy instances in ASGs. Listener rules and target groups route requests based on URL paths, such as /bmw and /ktm. Security groups, health checks, and scaling policies are further created to provide secure access and reliability while allowing dynamic scaling during changes in the number of concurrent users accessing the application.