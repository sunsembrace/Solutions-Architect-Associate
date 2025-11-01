1. Labs Created multiple budgets with IAM permissions on AWS console. - Hands on Lab.


2. launching website on EC2 - Hands on Lab.
Hands-On:
Launching an EC2 Instance running Linux
We’ll be launching our first virtual server using AWS Console.
We’ll see that our web server is launched using EC2 User data.
We’ll learn how to start/stop/terminate our instance.

Choose AMI, Architecture, Instance type, Create Key Pair (RSA Encrypted) w/ key format .pem  , network settings → Security group with rules (e.g allow SSH traffic from whatever IP), allow HTTP Traffic from internet (because we are launching a website on our instance), then decide configure storage (EBS volumes), then user data (A script that only runs/executes on the first launch of the instance), number of instance.

Userdata script.

#!/bin/bash
#Used for Userdata 
#install httpd (Linux 2 version)

Yum update -y
Yum install -y httpd
Systemctl start httpd
Systemctl enable httpd
Echo “<h1>Hello World from $(hostname -f) </h1>” > /var/www/html/index.html

3. Security Groups Troubleshoot a website not loading. - Hands on Lab.
Solved a timeout error.
Tip - if timeout ever occurs this is 100% because of an EC2 security group issue. E.g Try to HTTP query or SSH into it and it doesn’t load.
Solution: Add HTTP rule with source anywhere. → Inbound rule fix.
Can do it with any type and any source/ip → E.g our own IP.

4. Accessing EC2 with IAM credentials. - Hands on Lab.
Practice IAM roles with SSH.
Dont run aws configure and enter your personal details because anyone who connects to that ec2 can find that info. Rule of thumb - Never enter your personal access or secret access key. What we do instead is use IAM roles.
So create an IAM role if one doesn't exist already, attach it to the EC2 by going to action → security → modify IAM role → pick the role to attract.
Now if you do 
Aws iam list-users.
You can then have access to the ec2 instance.
If you try to run the same aws iam list-users command after detaching the role, you’ll get an Access Denied.

5. EC2 Instance Launch type - Spot fleets / Instance - Hands on Lab.
Launched Spot Fleet.
Learnt how to launch a spot instances through ec2.

6. Elastic IP Hands on Lab
Private v Public IP (IPv4)  - Hands on.
By default, your EC2 machine comes with:
A private IP for the internal AWS Network.
A public IP, for the WWW.

How does public IPv4 address pricing work?
Hourly charge  for in-use Public IPv4 Address: $0.005
Hourly charge for idle Public IPv4 Address: $.005

Go down to Elastic IPs → Allocate Elastic IP address → from amazon’s pool of ipv4s address → creates an ipv4 address to be allocated to an ec2 instance → Click Action again, associate Elastic IP address → Then decide Instance or Network interface → Choose private IP address to associate it with.

7. Create a placement group  - Hands on labs.

EC2 Placement groups → Create placement group → Give name, and decided placement strategy (cluster,spread,partition)  

Then launch instance → advanced detail → Pick your placement group based on your strategy!

8. Elastic Network Interface (ENI) - Hands on Labs.
Launch an instance → launch 2 after doing typical settings.
Select instance created click networking and check network interface (contains public ipv4, private ipv4, private ipv4 DNS.
Go to network interface on left side then click create new network Interface → Pick subnet for which your ec2 is in. → Attach security group → Create Network interface. →  Now you have created a secondary IPv4 → You can then click actions → Attach → Choose an instance.
Now you’ll be able to view it in the network interface GUI → Now why do this? Because this ENI we have control over and can move it from one EC2 to another. You detach it and re-attach it to a different EC2. 
This really helps with fail over. 

If we terminate the EC2’s. The ENI we created is going to remain but the two ENIs w/ the other EC2s will be automatically deleted. More control over IPv4 and networking.

ENI doesn’t cost any money if we leave it on.

9. EC2 Hibernate - Hands on Labs.
Launch an EC2 → Do normal settings → Advanced Settings → Enable “stop-Hibernate behaviour” → In advanced, go to storage volumes → Encrypt storage and choose sufficient size e.g 8GB would be enough for a t2.micro as it only has 1GiB memory. → then Launch.
 
How to prove it's enabled?
EC2 instant connect to connect to ec2.
Use the uptime command and show us how long it's been up. 
Now disconnect from your instance instant connect → go to instance state and click hibernate instance → Then it should be stopped, then start it again → then use ec2 instant connect and do uptime and it should start from a different number rather than 0!


10. EBS Volume attach to EC2 - Hands on labs.
EBS Hands on.
Go to instance, then storage hub, and view block device (will present a volume ID).
Can click volume ID to see it exists and can also view it easier through volume tabs.
Can create your own volume and you have to use the same AZ as your ec2 Instance.
To find this, click your instance, then click the networking tab, and scroll down till you find the specific AZ. e.g eu-west-1b so your EBS must be in the same.

Now you can click on the created EBS → Click actions → attach it to an instance. 
And now the instance has an EBS volume attached to it and can do it with multiple EBS volumes.


11. EBS Snapshot - Hands on Labs.
Go to EC2.
On the left panel go to Snapshots. → gives us info.
We can right click and copy it into any other region! (helps w/ disaster recovery).
We can also click actions then create volume 

Can click recycle bin as well to “create a retention rule”.
This protects EBS snapshots and AMIs from accidental deletion.
Create retention role → Resource type pick “EBS Snapshots” → Assign Retention period (days).
Rule lock settings → Leave unlocked (retention can be modified/deleted anytime by user w perms)

12. AMI hands on - Hands on Lab.
Launch an instance. (do all usual settings including picking amazon image at start).

In userdata paste:

#!/bin/bash
#Use this for userdata script
#Install httpd (Linux 2 version)
Yum update -y
Yum instal -y httpd
Systemctl start httpd
Systemctl enable httpd

So script used to install httpd which is apache web server.
Website should load  then right click ec2 → Create image
Then do settings and click “create image” → AMI is created.
Click AMI on left panel to see the AMI (demo image) once its created in status.
You can launch instance from the AMI or you can create an instance and click “my AMIs” when you name your instance to find the AMI (Demo image) you created and pick it.

Then we just paste in user data:
#!/bin/bash
#Use this for userdata script
#Install httpd (Linux 2 version)
Echo <”h1>Hello World from $(hostname -f)</h1>” > /var/www/html/index.html

→ We just paste a file & Dont need to install httpd as the imagelready contains httpd
→ Speeds up boot up time (hence why we create AMI)
→ then launch it.
→ take public IP, paste in browser, and it loads really fast!
→ So main take away is install things which take awhile initially but then you package it as an AMI and start from the AMI to do some end customisation there for a faster bootup 


13. Encryption - Encrypt an unencrypted EBS Volume - Hands on Labs.
Encryption: encrypt an unencrypted EBS volume.
Create an EBS snapshot of the volume.
Encrypt the EBS snapshot (using copy)
Create a new EBS volume from the snapshot (the volume will also be encrypted).
Now you can attach the encrypted volume to the original instance.

EBS Encryption Hands on Lab.
Go to EC2 → Create volume  (dont encrypt)→  When we create a snapshot, it’ll show the snapshot wont be encrypted just like the ec2.
Go to Actions → Copy snapshot → Enable “Encrypt this snapshot” into same destination region with KMS type. → From this click “Actions” → “Create volume from snapshot” → you can now create a volume from snapshot because the underlying snapshot is encrypted!

So first time just.
 So go to EC2 → Create volume → Enable encryption → Create snapshot (will be encrypted this time).

Remember to delete snapshots and volumes after!


14. Elastic File system (EFS) - Hands on Labs.


Create EFS - Decide file system type (regional  or One zone). → enables automatic backups → Can decide Transition within lifecycle manage data wise and into which storage & enable encryption. → Performance settings → Enhanced or bursting.  (elastic recommended or provisioned) → additional settings (performance mode) → General purpose (Recommended) or Max I/O → Then pick network VPC and Mount Targets → In this choose AZ, Subnet ID, Ip address is automatic and add or create  security groups → Create EFS.

Now mount it onto EC2 instance → launch instance. → Choose subnet → configure storage → edit file systems (EFS or FSX) → Add shared file system. → Can now keep boxes enabled rather than adding it manually like before. 
Can create another ec2 and attach the same efs we used before simultaneously.
Then connect to   instances using ec2 direct connect.
Verify if it exists.

Ls mnt/efs/fsl/
Sudo su 
Echo “hello world” > /mnt/efs/fsl/hello.txt
Cat /mnt/efs/fsl/hello.txt

Do this on each ec2 to check and it should work.
 

Enhanced → Provides more flexibility and higher throughput levels for workloads with a range of performance requirements.

Bursting → provides throughput that scales with basic  performance requirements

In EFS you only pay for the storage used. 

15. EBS v EFS - Section clean up.

Go to EFS → actions → delete file system. → Go to instances and terminate instances → Go to volumes and delete volumes → go to snapshots and delete snapshot → go to security groups and delete security groups except default. 


16. Sending traffic to a load balancer. - Hands on Labs 

Launch 2 EC2’s → Add EC2 user data 
#!/bin/bash
#use this for your user data (script from top to bottom.)
Yum update -y 
Yum install -y httpd
Systemctl start httpd
Systemctl enable httpd
Echo “<h1> Hello World from $(hostname -f)</h1 >”>/var/www/html/index.html

Copy ipv4 URL to ensure each instance is working on webpage
Then click load balancer on left tab then choose which LB you want.
Scheme → Interfacing
IP address → IPV4
Decide how many AZ’s it is deployed in.
Assign a security group to the Load Balancer.  (e.g could allow type as HTTP from anywhere.
Decide  Listeners/routing to a target group.

Creating a target target group. (can do within LB Creation.)
Group = group of x in this case it’ll be ec’2s. Set to http like the LB.
After creation the load balancer gets a DNS name which you can paste to check web works.
If you keep refreshing page you’ll see ip change as the application load balancer is redirecting between both ec2 instances.

17. Advanced LB - hands on labs 2.
17.1) Load balancer - Advanced things - hands on lab 2.1 
Typically we access the load balancer through our security group and the ec2’s through their own security group. Can also access ec2 through dns public ip. 

But what if we wanted to access the ec2 only through the load balancer.
Go to your ec2 security group  and edit the inbound rules and only allow it to type HTTP and change source from the security group of load balancer only.
This has allowed us to tighten Network security.

17.2)Application balancer rules.
Go onto ALB → Click listeners → Add more listener rules → Define rule conditions and can add multiple → Define Rule Actions (Action types, response code, content type, response body) → set rule priority → Create rule.

18. Create a Network Load Balancer - Hands on Labs.
 EC2 → Load Balancers → Network Load Balancer (Internet facing, IPv4), 3 AZ’s with IPV4 assigned by AWS. → Inbound rules type HTTP, port 80, Source - anywhere. → Attach SG or create one in a diff tab via EC2 → Security groups → Then add SG within NLB config/creation.

Then create a target group.
EC2 → Target groups → Create target group → Select instances and name target group
→ Protocol is TCP because it’s a network load balancer on port 80, for health check do HTTP as that is the application running on the EC2.

For advanced health check settings → set healthy & unhealthy threshold from 2-10, set timeout and set interval via seconds. → Then register targets →  Select as pending below → Create target group.

Now TG is created go back to network load balancer, refresh page and on listeners & routing select the TG and create network load balancer.

Now enter the URL and see if the website works.
Say it doesn’t load, lets see some of the issues on why:
Instances are unhealthy → health checks failed → edit inbound rules and we see we allow traffic from our early application load balancer but we need to make a new rule to allow traffic from our network load balancer. → This should resolve it.

19. To enable sticky sessions - hands on labs.
Go to EC2 → target groups → actions → edit attributes → Scroll down to target selection configuration and enable Turn on stickiness. → Then choose LB generated cookie or application-based cookie. → Then duration and name it. → Save changes.

→ Go to website from previous labs and refresh and you’ll see the “Hello world from x-ip → Open debugger and click network and you can keep refreshing page and you’ll see its always the same IP instance as it doesn’t change. → Using debugger check get request and you’ll see a response cookie and it’ll show an expiration date.

20. CrossZone hands on lab. 
Create a NLB, GWLB, ALB → Click attributes to view if cross-zone load balancing is on/off.
Regional data transfer charges may apply when cross-zone load balancing is turned on (for GWLB and NLB).
Cross Load balancing is on by default for ALB.


21. SSL Certificate on both the ALB and the NLB hands on labs.
Go to EC2 → Load balancers → Select ALB and add a listener → change protocol from HTTP to HTTPS and port by default will be 443. → default actions to forward → Select target group → then Secure listener settings. →Select a security policy or leave as default → then default ssl/tls certificate to say where it’s from e.g from ACM or import ( paste certificate private key etc) and it’ll import it into the ACM.

Similar process for NLB  → Select NLB and add a listener → change protocol to TLS port 443 → default action to forward to a target group → Choose security policy → Then choose where SSL/TLS certificate is from → Then set an Application Load Balancer Negotiation (ALBN) which is pretty advanced for TLS.

22. Auto scaling Groups Hands on Labs.
EC2 → Autoscaling group → Create ASG → DemoASG → Create launch template → do all configs such as name, AMI, instance type, Key Pair, config storage, userdata  etc. 

#!/bin/bash
# Use this for your user data (script from top to bottom)
# Install httpd (Linux 2 version)
Yum update -y
Yum install -y httpd
Systemctl start httpd
Systemctl enable httpd
Echo “<h1> Hello World from $(hostname-f)</h1>”> /var/www/html/index.html

This creates a web server on every EC2 instance created as part of this ASG.
Now choose instance type requirements and yes this lets you override the launch template and very advanced specificities. 
For AZ choose “Balanced best effort.”
Can integrate with other services - optional → Attach to an existing load balancer. → Choose from your load balancer target groups → select target groups. → Turn on Elastic Load Balancing health checks. → Decide min x max capacity → Decide if you want a target tracking policy → Decide which policy → Create ASG.

23. Auto scaling Groups Hands on Labs.
EC2 → Autoscaling group → Create ASG → DemoASG → Create launch template → do all configs such as name, AMI, instance type, Key Pair, config storage, userdata  etc. 

#!/bin/bash
# Use this for your user data (script from top to bottom)
# Install httpd (Linux 2 version)
Yum update -y
Yum install -y httpd
Systemctl start httpd
Systemctl enable httpd
Echo “<h1> Hello World from $(hostname-f)</h1>”> /var/www/html/index.html

This creates a web server on every EC2 instance created as part of this ASG.
Now choose instance type requirements and yes this lets you override the launch template and very advanced specificities. 
For AZ choose “Balanced best effort.”
Can integrate with other services - optional → Attach to an existing load balancer. → Choose from your load balancer target groups → select target groups. → Turn on Elastic Load Balancing health checks. → Decide min x max capacity → Decide if you want a target tracking policy → Decide which policy → Create ASG.

This now creates 1 EC2 for me when I click ‘details’ i see → When ASG creates an EC2 i can click ‘Activity‘ to view the history, when I go instance management  it shows instance ID’s created → Then click Instance to see its running but because our ASG and instances are linked we can go to target groups on left hand side and select it and find that in targets tab that our EC2 is a registered target in our ASG. → Check this by going on website and refreshing the page. → if the instance becomes unhealthy it’ll be terminated and a new one will be made.

24. Scaling Policies Hands On Lab.
Create Scheduled action → Desired capacity, min, max. → Recurrence and timezone. → Specific timezone.

Create predictive scaling policy (which is machine driven) → scale based on forecast →  Decide metrics it scales on & target utilization. 

Create Dynamic Scaling policies → create dynamic scaling policy → Choose policy type (Target, Step, Simple scaling) → choose/create cloudwatch alarm) → Decide Take the action e.g Add, 10, Percent of group, add capacity units in increments of at least 2 capacity units, decide wait value.

25. Amazon RDS Hands on Labs
RDS → Create database → Standard Create → MySQL →  Template = Production, Availability & Durability = Single or Multi AZ → Settings = DB name identifier and master username/ password → Instance configuration = Standard/Memory/Burstable classes & instance type →  Storage & enable storage autoscaling & minimum storage threshold → Connectivity = Connect or dont to an EC2 compute resource, choose VPC, DB Subnet group, public access = yes/no, choose/create security group, AZ preference, DB port → database authentication = password or password and IAM or password and Kerberos → Monitoring = can enable Enhanced monitoring. → Additional configuration = Initial DB name, DB parameter group, enable automated backups and retention period (1-35 days), backup window, log exports ( audit, error, general, slow query), enable deletion protection.

When created you can view tabs and find security group and in inbound rules see the type as MYSQL/Aurora with port 3306 from my IP. If struggling to connect i’ll change it to ipv4 0.0.0.0 to allow from anywhere.

25.2 Connecting to RDS 
Downloaded SQLectron to connect to our DB 
Take the DB endpoint 
Go on SQLectron → Make db name and database type=MySQL, paste endpoint on Server Address and port 3306, enter user and pw, initial db → Then click Test and it should do a connection test successful. → If it doesn’t work check if your DB was set to be a public one or security group to allow your IP address.

Can now enter raw sql commands such as CREATE TABLE mytable (name VARCHAR(20), first_name VARCHAR(29));

Can then use INSERT statement to input data etc.

25.3 Create Read Replica
RDS → DB → DB-1 → add reader → DB instance type, Multi-AZ deployment, storage type etc.

 → Can view monitoring through rds cloudwatch and can use actions to take snapshots too.

26. Amazon Aurora hands on labs
RDS → Engine type =  Aurora (MySQL Compatible) & Choose version → Template = Production  → settings = DB cluster identifier, Master username, Master Password → Cluster Storage Configuration = Aurora Standard → Instance Configuration = Burstable & serverless v2 toggle on w/ capacity range of ACUs  or Instance type = db.t3.medium → Availability& Durability = Create an Aurora Replica or Reader node in diff AZ → Connectivity = Dont connect to an EC2 compute resource, network type = IPv4, vpc = default vpc, db subnet group = default vpc, public access toggled on, vpc security group = choose/create, database port = 3306 (MySQL) → Database authentication → Monitoring → Additional Configs

Then click Databases to view it. → Shows in the connectivity & security tab that we have 2 endpoints (Reader & Writer). 
But if we click on the instances within the DB they will have their own endpoints.
Actions let us add replica, create cross-Region read replica, add replica auto scaling or even add replica auto scaling.

Replica Auto Scaling policy → policy name → Decide target metric → target value  → Choose cluster capacity details. 

Action also lets us add an AWS Region but only if it was made globally initially

To delete.
Delete reader instance then writer instance then you can delete the entire cluster.

27. ElastiCache hands on.
Amazon ElasatiCache → Get started → Redis/Memcached → Configuration: Serverless / Design your own cache and Creation method: New cache (Easy, cluster) or Restore from backup. → Configuration= production/Dev/Test/Demo → Cluster info → Location= AWS / On premises, multi-AZ Enable → Cluster settings= port/parameter groups/Node/Number of Replicas  → VPC ID & selected Subnets → Advanced settings (enable encryption at rest or in transit) → Access control and selected security groups. → Create.

Go to actions to delete!

28. Route 53 Hands on Labs.
Route 53 → Register domain → Choose unique domain name → decide duration / auto-renew, → Enter contact info → enable privacy protection → Submit to register and pay for domain.

Should have 2 records the SQA and the NS record → This one is for route 53.

Creating our first records.
Create record → record name and record type → Decide value, TTL, routing policy → Create.
Now command lines as pasting website url wont do anything because theres no servers hosting it i believe.

Go to AWS Cloudshell → nslookup , dig 
if those commands dont work or exist Do →  sudo yum install -y -bind-utils
Installs both these commands.
Then do nslookup <website url> and it should display info including the ip value you made earlier.

Dig <website url> and it should display similar info but more including such as the TTL and the record type e.g A.

29. Route 53, ec2 set up, alb - Hands on Labs
EC2 → launch instance and do config and in userdata specify a bootstrap data script→ 

#!/bin/bash
Yum update -y
Yum install -y httpd
Systemctl start httpd
Systemctl enable httpd
EC2_AVAIL_ZONE=$(curl -s http://169.254.169.254/latest/meta-data/placement/availability-zone)
Echo “<h1>Hello World from $(hostname -f) in AZ $EC2_AVAIL_ZONE </h1?” > /var/www/html/index.html

Then launch instance.
Then change region in top right account and launch the 2nd instance and same instruction.
Then repeat for 3rd instance also in a diff region.

Then create an ALB → Choose 3 subnets and choose a sg → create new target group based on the instances and register those instances you may → then create target group then create ALB.

Now go on each ipv4 address on each instance and paste on browser and it should work!

30. Route 53 TTL Demo.
Route 53 - TTL Hands on Lab.
Create record - paste value from one of your ec2 instances, set TTL.
Go on domain websites should work. Then instantly edit and change record value to the other instance (diff Ip)
If you then go into the cloud shell and do a dig command, then it’ll show how long left it’ll be cached.
After the TTL expires the browser will then ask route 53 the value of the domain and may be redirected to the new IP. 

31. Route 53 - Records accessing alb through hostname record - Hands on labs
Route 53  pick domain → Create record → in value paste hostname of ALB so we can access it through your Domain website rather than by the ALB link →  paste domain url in web browser and it should display what your ALB displays when its url is pasted in browser.
This works but isnt AWS native

So now we create an alias record → create record → Type A as our ALB only handles IPv4 traffic → route traffic to alias to application and classic load balancer → choose region and the LB → Create record.

How to do it for just the domain apex?
Create record → Record type is CNAME → points to value of domain name of our ALB → wont work as CNAME doesnt work for apex.

So we do it for an alias record and change record type to A for IPV4 like above which will make the domain apex accessible. 

32. Route 53- Simple routing hands on labs.

Route 53 → Create record → Type: A IPv4 → value an IP from an instance → TTL 20 seconds → Routing policy: Simple routing → Create → go test url and work. 

→ Open cloudshell and use dig <url> to see the TTLand can then go back to edit record and add another/multiple IP from another instance → so when TTL expires we can use dig again and see how the IP has changed and how its client side choice. 

33. Weighted routing - hands on labs.
Create record 1 → Record name → Record type A → Value: ip1 → TTL → Routing policy: Weighted → Weight e.g 10 → health check optional  → Record ID name

Create record 2 → Record name → Record type A → Value: ip2 → TTL → Routing policy: Weighted → Weight e.g 70 → health check optional  → Record ID name

Create record 3 → Record name → Record type A → Value: ip3 → TTL → Routing policy: Weighted → Weight e.g 20 → health check optional  → Record ID name

We can then go to our website url with prefix of weighted.url
Use dig to see ip, ttl, and weight to see how it redirects most of the queries into a specific IP.

34. Latency-based Hands on Labs.
Create record 1 → Record name → Record type A → Value: ip1 → TTL → Routing policy: Latency→ → health check optional  → Record ID name

Create record 2 → Record name → Record type A → Value: ip2 → TTL → Routing policy: Latency→ → health check optional  → Record ID name

Create record 3 → Record name → Record type A → Value: ip3 → TTL → Routing policy: Latency→ → health check optional  → Record ID name

35. Health Checks - Hands on Labs
Route 53 → Health Checks → Create health check → what to monitor → specify endpoint by IP address or domain name → protocol, IP/host name/port/path → Advanced config e.g failure threshold, string matching, etc → toggle on or off for cloudwatch alarm to notify if health check fails.  → Create Health check.
(do this for your 3 diff IPs)

Now go to ec2 → Tamper with SG of the ec2 ip for the health check such as failing port e.g deleting the inbound rule → go back to health checkers and it’ll show it was unhealthy.

Calculated health check
Can then create Calculated health check → create health check → what to monitor = status of other health checks → specify which health checks to monitor → Report healthy when ‘x’ number of 3 selected health checks are healthy or all or one or more → Create health check

Calculated health check shows unhealthy because one healthcheck is unhealthy!

Cloudwatch alarm healthcheck (assuming you made one)
Create health check → What to monitor = state of Cloudwatch alarm → Monitor cloudwatch configs in region etc → create 


36. Routing Policies - Failover (Active-Passive)
Route 53 mandatory health check for 1st ec2 if unhealthy it uses record of the 2nd.
Can only be one primary and one secondary 

Creating a fail over hands on labs 
Create a record → record type A ipv4 → Value of ec2 → TTL→ routing policy=Failover → Failover record type ==Primary → Record ID

Create record 2 → Record type A ipv4 → value of different ec2 → TTL → Routing policy=Failover → Failover record type=secondary → Create record

Now if you got to their IP both sites work so go to region of one of the ec2’s and make it unhealthy e.g remove security group inbound http rule and wait for it to be unhealthy then go on url and it should show its connected to a diff ec2 demonstrating the failover did work

37. 
Geolocation hands on labs.
Create a record → record type A ipv4 → Value of ec2 → TTL→ routing policy=geolocation → Location e.g = Asia

Add another record 2, Create a record → record type A ipv4 → Value of different ec2 → TTL→ routing policy=geolocation → Location = e.g = US 

Add another record 3 (anything that doesn't match our two locations above goes to default) → Create a record → record type A ipv4 → Value of different ec2 → TTL→ routing policy=geolocation → Location = default

To test  use vpn changing your IP to ensure URL loads you to the desired ec2. 

38. Multi-value health checks hand on labs.
Create record → Record Type A IPv4 → Value of ec2 → TTL 300s → Routing policy = simple routing= Multivalue answer →  health check (optional) = us-east-1

Create 2nd record → Record Type A IPv4 → Value of different ec2 → TTL 300s → Routing policy = simple routing= Multivalue answer →  health check (optional) = ap-southeast-1

Create 3rd record → Record Type A IPv4 → Value of different ec2 → TTL 300s → Routing policy = simple routing= Multivalue answer →  health check (optional) = eu-central-1

Go to cloudshell and use dig url and it’ll display all 3 ec2 instances because they are healthy.

Now make one unhealthy by selecting an ec2 and edit health check and ticking the invert health status box → Run dig again on cloudshell and it should display only 2 ec2’s now as one is unhealthy. → Untick box after your test in edit health checks to finish testing. 

39. Elastic Beanstalk Hands on Labs.

Elastic Beanstalk → Create application → Environment tier = e.gWeb server environment → application name →: environment info = env name, domain (this url is how we access the web servers) , env desc,  → Platform = Managed platform, Platform = e.g Node.js, Platform branch, platform version → Application code = sample application, → Presets = Single instance (free tier eligible), → Service access (IAM roles that allow beanstalk to do what it does), service role = create and use a new service role, service role name, choose EC2 keypair, EC2 instance profile. → Go to IAM Console and follow instructions below then refresh and return here →  Can now view and We Skip to review but could’ve configured networking,database or instance traffic and scaling etc. 

Go into IAM console → create a role for AWS service for EC2 → In permissions policy type in Beanstalk, add AWSElasticBeanstalkWebTier, AWSElasticBeanstalkWorkerTier, AWSElasticBeanstalkMulticontainerDocker → This is enough to get started. → Click Next → create role. 

Now events are happening and these come from CloudFormation → Click events to see things are being created, in progress, or create_complete. 
Resources → Shows what it's created and can click the template to go to the application composer which visually shows us the architecture and what it's created for us behind the scenes.

Events → Shows us sg created or an elastic ip created etc. We can then go to ec2 instances to view specifics of the things created such as the type and so on.

Now when everything is launched → We get a domain name → Paste onto a web browser and it’ll work and show its being run on Elastic Beanstalk.
Upload and deploy → Can upload a new application and automatically gets deployed to our ec2 instances.
Health → Shows us health checks and can click Logs, monitoring to be view centric on our applications. 
Application:Myapplication → Can create a new environment. 

40. Amazon S3 Hands on Lab
S3 → Create bucket → Choose region in account settings top right before hand as it’ll be autoselected in this → Bucket type = General purpose → Bucket name (must be unique globally), copy settings from existing bucket - optional. → Object ownership = ACLs disabled(reccomended) → Block public access settings e.g Block all public access → Bucket versioning = Disable → Tags → Default encryption = Server-side encryption with amazon s3 → Create bucket.

Can view after all general purpose buckets in all regions.
Select your bucket via name search → Upload → Select file → Destination is also given of the bucket. → Close and can see in our s3 bucket that the file is there, can click it for more details of said file properties with an object URL to view it.

Click Open and it’ll display the image.
But if I click the Object URL and paste it in the browser, I get denied access and can't view it using the public URL.
The difference is that the beginning is the same but then becomes complicated and long → Why? Because this is a s3 pre-signed URL as it contains a signature to ensure its me who’s making the request and contains my credentials hence why the first link displays it and the second public doesn't.

In buckets we can also create a folder → Create folder → folder name → Server-side encryption → Create folder.

Can now click folder and upload images within it. Via same upload method for files as before.
Can click delete and type permanently delete to delete it.

41. S3 Hands on Labs - Creating s3 bucket policy.
Create bucket --> Untick block all public access --> Only do this if we want it to be publicly accessed --> Confirm --> Permissions overview tab go to Bucket policy --> Now create one --> Look at bucket policy example --> But we use AWS policy generator.
Effect - Allow
Principal - * (for all)
AWS Service - S3
Actions = Get objects
ARN - bucket name with / and * e.g  (can copy the arn from the edit bucket policy page).

Generate policy --> Copy and paste it back on the edit bucket policy page.

42. S3 Website Static hosting hands on
Go to your bucket → Upload a file like an image.jpeg into it → Go to properties of bucket → Scroll down and edit static website hosting and click enable → Host a static website, index doc e.g index.html (will have to upload this file later), error doc, redirection rules → and save changes → Go back to the bucket and upload the missing index.html file → Upload and add the file.
Back to properties → Static website hosting will give us an endpoint URL and paste it into browser and it should work. → If we right click the image and open in new tab we get the public URL of our image.jpeg.

43. S3 Versioning hands on labs.
Go to bucket → Properties → Bucket versioning → edit → Enable → Save changes → Objects → get object url from before into browser → Say we wanna update website → Click index.html file and edit the line to idk “I Really love coffee” & Save it → now go click  Upload file → index.html (Has updated content now) → So it overwrites the previous one and if you refresh the page of the website on browser you’ll see the website update.

But to see what happened in the back click show versions toggle → It will show Version ID null for files uploading before we toggled on versioning → But files like index.html will show two versions. 1st  version ID for the file uploaded before for null and 2nd will have a version ID → This allows us to roll back changes.

So we click on the specific ID and we delete a specific version ID → a permanent delete → Delete objects and the website will revert to a previous version.

So what happens if we hide versioning 

44. S3 Versioning hands on labs.
Go to bucket → Properties → Bucket versioning → edit → Enable → Save changes → Objects → get object url from before into browser → Say we wanna update website → Click index.html file and edit the line to idk “I Really love coffee” & Save it → now go click  Upload file → index.html (Has updated content now) → So it overwrites the previous one and if you refresh the page of the website on browser you’ll see the website update.

But to see what happened in the back click show versions toggle → It will show Version ID null for files uploading before we toggled on versioning → But files like index.html will show two versions. 1st  version ID for the file uploaded before for null and 2nd will have a version ID → This allows us to roll back changes.

So we click on the specific ID and we delete a specific version ID → a permanent delete → Delete objects and the website will revert to a previous version.

So what happens if we disable show versions and delete a coffee.jpeg → In this case we are not deleting a version underlying ID but by adding a delete marker (Doesn’t actually delete the underlying object) → Now within our bucket visibly it looks like its been deleted but if we click show versions we’ll see the image with type Delete Marker and with a Version ID.

Now if we refresh the page the coffee image is gone → If we right click image to open in new tab we get an error 404 image not found → So to get it back we can click the delete marker and delete the delete marker which restores the previous object. (coffee jpeg).

45. S3 Replication hands on labs.
S3 replication Hands on labs. 
Create a bucket as your origin → Set in AWS region → Enable versioning (replication only works if versioning is on). 
Create a 2nd bucket as your target bucket → Can set in same or different region to → Enable bucket versioning. 

Add file in bucket origin. → Click management → Create replication rule → Status= Enabled, Rule scope = Apply to all objects in the bucket
Destination = Choose a bucket in this account.  (The 2nd target bucket we made).
 IAM Role = Create a new role
Save
New prompt shows up so it’ll only replicate from the moment we enable it, so we could use a one time batch operation to replicate existing objects that weren’t replicated (this is separate from the replication feature itself) but we click No.

Now if we refresh the replica bucket we'll see the object from the original bucket hasn't been replicated.
Now due to the replication rule added after the object, anything uploaded will be replicated from this point forward. 

Upload a new file in origin and then refresh target bucket after a few seconds we’ll see it was replicated and see version ID as the exact same as origin bucket 

Now go to management → Edit rule → Delete Marker Replications are not replicated but we can enable this feature so it replicates delete markers from one bucket to another.
E.g if i delete my coffee.jpeg file, it’ll create a delete marker as its versioned. Now in the replica bucket we’ll also see the delete marker replicated. 

If you decide to delete a specific version ID and a permanent delete then it will not be replicated in our replica bucket as only delete markers are replicated not deletes. 

46. S3 storage classes - hands on. 
Create a bucket e.g named S3-storage-classes-demos → Add files and add any file.
Look at properties of this object and under storage class → Pick a class. → Upload.
Now the object will show what its storage class is and you can always edit it.

We can automate moving objects between storage tiers/classes with lifecycle rules
Lifecycle rule configuration → Apply to all objects in this bucket → Move current version of object between storage classes → Decide which storage after how many days etc. 

47. S3 lifecycle rules - hands on labs.
Create lifecycle rule 
Life cycle rule actions (5 exist)
Move current versions of object between storage classes.
Move noncurrent versions of objects between storage classes.
Expire current versions of objects between storage classes.
Permanently delete noncurrent versions of objects.
Delete expired object delete markers or incomplete multipart uploads.

Can change storage class transitions, day after object creation and as many times as we want. 
Can also set up permanent expirations too. 
Create rule. 

48. S3 event notification hands on labs.

Two ways to do it in S3 service. 
Create event notification or involve Amazon EventBridge. (edit and set On).

 Create event name, Event Types. Object removals, etc. → Then choose destination e.g specify SQS name. → In new browser tab, Go to SQS service → create queue → Name it. And create → Go to Access Policy tab we wanna edit to allow our S3 to talk to our SQS queue.  —> Click policy generator and for action do send message → Then paste this policy in. → Now go back to s3 and add the SQS name to S3 destination and it should work. 

Now to test it works. Upload a file to S3 bucket works.
Now say we want the image to become a thumbnail so we go to SQS and click Send and receive messages → and click poll messages and it creates a message from the s3 showing connectivity with the Key showing which image created an event 

Create event notification or involve Amazon EventBridge. (edit and set On).

49. S3 encryption Hands on Lab.
Create bucket → Keep bucket versioning on → Default encryption shows 3 diff encryption types → choose SSE-S3 → Create bucket → upload object and click object uploaded, scroll down to Server-Side encryption settings → Will show it as done and you can edit it. → However, this will create a new version of the object with updated settings hence why we enabled versioning earlier.

Edit → Override bucket settings for default encryption → Dual-layer server-side encryption with Server-side encryption with AWS Key Management Service keys (SSE-KMS) → Choose or enter AWS key (default one which is free) but creating your own KMS key costs money. → Save changes.

Click versioning and you’ll see two versions. → Scroll down to Server-side encryption settings which will display encryption type and the encryption key ARN.

Do same process of creating a bucket but then edit it after to SSE-KMS then enable bucket key so it has less api calls and is therefore cheaper.

SSE-C is missing as it can only be done from CLI not console. 
Client side is done outside of AWS.
So only ones you can do in console are SSE-S3, SSE-KMS, DSSE-KMS.

50. S3 CORS Hands on Labs.
<html>
	<head>
		<title>My first Webpage</title?
	</head/>
	<body>
		<h1> I love coffee</h1>
		<p>Hello world!</p>
	</body>
	
	<img src=”coffee.jpg” width=500/>

# Cors demo #
<div id=”tofetch”/>
<script>
Var tofetch = document.getElementById(“tofetch”);

fetch(‘extra-page.html’)
.then((response) => {
		Return response.text();
})
	.then((html) => {
		tofetch.innerHTML = html
	})
</script>
</html>gh

In a bucket, upload coffee.jpeg and extra-page.html file.
Go to properties, click end-points, go to link and and it’ll show the fetch request worked with the new line added from extra-page.html from within the same origin  because both files are within the same bucket. 

Now to demonstrate CORS.
Create bucket → demo-cors-sunsembrace - any AZ → create bucket.
Under properties → scroll down and enable static website hosting → index document= index.html
Bucket policy (just copy one from prev bucket for simplicity) save.

Now upload extra.html in the new bucket and click endpoint link and it should work.
Now we can remove extra-page.html in origin bucket as we dont need it anymore as its in another bucket.
Now change the index.html file to point instead to the extra page in my other origin.
So first go back to original page and refresh and it’ll show 404 not found.
 Go to other bucket properties, find the public URL and open it on browser and use that entire URL as it works and change our index.html script by changing the fetch within CORS demo the full path of the new URL.  This will trigger a cross-origin resource share (CORS).
Then reupload the edited index.html file in the origin bucket to override previous one.
Check initial endpoint link and refresh page. 404 error is gone but its not displaying the extra page line but in our debugging console tool it shows cors was blocked.
This is because the other bucket was not set up for CORS hence why it got blocked.
SO on our 2nd bucket go back to properties go under permissions and edit it and add correct CORS settings in form of json.

[
	{“AllowedHeaders”: [
		“Authorization”
	],
	“AllowedMethods”: [
		“GET”
	],
	“AllowedOrigins”: [
		“<url of first bucket with http://… without slash at the end>”
	],
	“ExposeHeaders”: [],
	“MaxAgeSeconds”: 3000
	}
]

Paste this in.

Go to the first webpage, copy it and paste it into the JSON config above.
Save changes.
Now we’re telling the 2nd bucket that its okay to make requests from the origin.
Refresh page and the extra part should be working.
Then go into console debugging that we had before and in the networking tab it'll show the request made with the GET command and with the origin bucket link.

51. S3 MFA Delete hands on labs.
Create bucket → Enable bucket versioning → Go to properties → Click edit on bucket versioning → MFA shows disabled → To enable it we have to use CLI → Using root account click top right and click your security credentials → Create new access key → 

On cli
#generate root access keys 
Aws configure – profile root-mfa-delete-demo

Then 
(enter access key)
(enter secret key)
(enter region)
Aws s3 ls –profile root-mfa-delete-demo

#enable mfa delete
Aws s3api put-bucket-versioning –bucket mfa-demo-stephane –versioning-configuration Status=Enabled, MFADelete=Enabled –mfa “arn-of-mfa-device mfa-code –profille root-mfa-delete-demo

#disable mfa delete 
Aws s3api put-bucket-versioning –bucket mfa-demo-stephane –versioning-configuration Status=Enabled,MFADelete=Disabled –mfa “arn-of-mfa-device mfa-code” –profile root-mfa-delete-demo

#delete the root credentials in the IAM console.

Now go back to the bucket in the console, refresh and it should say mfa is enabled in the bucket versioning properties tab.

To test upload an image, then delete and it should add a delete marker because it has versioning and then attempt to permanent delete on console but it wont let you as mfa delete is enabled so you have to go through cli to do this.
(just change permissions from mfa delete command to disabled over the enabled.)

Go back to console, refresh, Now it should let you delete the delete marker as mfa is disabled. 

Then finally at the end of this lab, delete root access key. 

52. S3 Access logs hands on.
Create bucket → enable versioning → Create → Go to properties → Edit server access logging → Enable → Bucket policy gets updated as a result → Now decide target destination (which bucket you want e.g access log bucket) → Destination region, Destination bucket name, destination prefix →  Pick Log object key format → Save changes

Now go to your other bucket (not the target destination one) upload file etc and this generates activity which gets logged into the access log bucket. 
Check permissions of this bucket and check the bucket policy and you’ll see that it made a change to allow the service of logging.s3.amazonaws.com to allow all of this to occur. 

Logs take awhile to show but if you click the files it shows the api call made, who and when etc. 

53. S3 pre signed URL hands on labs
Use a private bucket, click its URL and you’ll get an error access denied message.
But if i click Open then the image will show e.g coffee.jpeg
Why? Because the beginning is the same but the end is different which is called a pre-signed URL.

So how do we generate a pre-signed URL for everyone?

Using CLI / object actions → Click share with a presigned URL → Then decide time interval until the presigned URL expires → Create presigned URL 

Now you can share the contents of a bucket with a url with anyone and have it expire for security purposes.

54. CloudFront hands on labs.
Create s3 bucket → name=DemoCloudFrontBucket → Upload files into it.
(Using object URL won't open it because its not public, but clicking open will as it gives you a presigned URL)

CloudFront → Create a CloudFront distribution → name= DemoCloudFrontDistribution → Distribution type = Single website app → Origin type=Amazon s3, then choose origin aka S3 bucket made earlier → Enable security (WAF)= Do not enable security protections → Create distribution.

Go view your bucket policy, refresh and you’ll see the JSON has changed permissions to allow interactions from the Service CloudFront.

Now once the cloud Distribution has deployed → Paste distribution domain name into URL and it still wont work unless you specify the path e.g one of the files you uploaded such as /coffee.jpeg → The elements in our s3 bucket are still private but see how loading the image was faster due to the cached version being in CloudFront.

55. CloudFront Georestriction hands on labs.
Go to Cloudfront → Security → CloudFront geographic restrictions → Countries-edit → Decide restriction type → Decide countries. → Save changes

56. Global Accelerator hands on labs.
AWS Global Accelerator → Create Accelerator —> Name=DemoAccel, AcceleratorType = Standard, Ip address type =IPv4 → Next → Add listeners, port =80,Protocol = TCP, Client affinity = source IP, → Add endpoint groups, region = us-east-1, traffic dial = 100, → Add Endpoint group 2 = eu-west-1, Traffic dial= 100, → Next (we’re missing endpoints in said region but just go next) → Create Accelerator 

Now lets create endpoints (e.g EC2’s or Load Balancers etc).
Click top right setting and set to same region as the endpoint you want to create → EC2 → Create EC2 → Create new security group → Allow HTTP traffic from the internet (allows it to be exposed) → Paste script into userdata → Do this again for other region → Then paste IPv4 into browser → Should work!

#!/bin/bash
#use this for User Data (script from top to bottom)
#Install httpd (linux 2 version).
Yum update -y
Yum install -y httpd
Systemctl start httpd
Systemctl enable httpd
Echo “<h1>Hello World from $(hostname -f) </h1>” > /var/www/html/index.html

#add in <region name> after hostname manually so we know what instance belongs to which region. 

Now in global accelerator lets configure both instances..
Select accelerator →  Click on the listener → Should display both endpoints now → Add endpoint groups → Decide region=eu-west-1, traffic dial = 100, → Next → Add endpoints → Endpoint type = Pick EC2, endpoint = Find the specific ec2 from earlier, weight = 128 → Save

Then click us-east-1 → Do instructions above the same process. → save

Now we are having issues with our health checks on the EC2’s we configured.
Click edit of the configuration → Healthcheck port = 80, Healthcheck protocol = HTTP, Health Check path = / , healthcheck interval = 10s, Threshold count = 2.

Do the same for us-east-1

Now wait and once provisioning status = deployed on your accelerator and it passes health checks → It’ll show 2 static IP addresses. → Paste in browser and it should work and redirect you to the closest one → Can use a VPN to change IP location to test other link but it should also work.

Then to finish labs delete the AWS Accelerator  → Delete →  Disable accelerator → Delete accelerator. 
Then delete instances → Terminate each EC2. 


57. AWS Snowball hands on labs. 
Snow family → Name, choose job type = import from amazon s3 → Next → Compute & Storage, Decide snow devices → Choose your pricing option= On demand, per day pricing, Select Storage type = S3 Data Transfer → Select your S3 buckets → Next → Security, how to encrypt data (KMS key), choose service access type, Shipping address, shipping speed, set notifications=create a new SNS topic → Next → Create.

58. Amazon FSx - Hands on labs.
Amazon FSx → Create file system → Pick Lustre → Enter file system name, deployment & storage type (SSD or HDD), throughput per unit of storage (pick a number) → Network and security=VPC, securitygroup for VPC, subnet → Encryption = Pick encryption key → Next → Create.
Note: Remember Lustre has persistent or scratch options.

Amazon FSx → Create file system → Pick Windows file Server → uses Server Message Block (SMB) Protocol. → Next → Name, deployment type = Single or Multi-az, Storage type = SSD/HDD, pick storage capacity →Network and security=VPC, securitygroup for VPC, subnet & standby subnet → Windows authentication = AWS Managed Microsoft Active Directory or Self-managed Microsoft Active Directory →  Encryption = Pick encryption key

Amazon FSx → Create file system → pick ONTAP→ Remember its compatible with linux, windows, MacOS → Next → Creation method= Quick create or Standard create → Quick configuration = file system name, deployment type = Single or Multi-AZ, SSD Storage capacity, VPC, Storage efficiency = enabled/disabled→ 

Amazon FSx → Create file system → pick OpenZFS → Also compatible with windows, linux, macOS → same simple config.

59. Storage Gateway Hands on Labs.
Storage gateway → Create gateway → Gateway timezone → Gateway options, gateway type = Amazon S3 File Gateway → Platform options Host platform → Platform options → Create
For storage gatewayOptions like VMware over Amazon EC2 allow us to set up the storage gateway directly on premise so the amazon s3 data is closer.
Volume Gateway is for cached or stored volume.

60. SQS Hands on Labs.
SQS → Create queue → Type=standard or FIFO, name → Configuration, visibility timeout,delivery delay, receive message wait time message retention period, maximum message size → Encryption = enabled/disabled, encryption key time = sse-kms, customer master key=default, data key reuse period =x minutes, → Access policy → Basic/Advanced, define who can send messages to the queue, define who can receive messages to the queue. → Create queue.


Click send and receive messages → message body “enter a msg” → Send message / pull for messages.
Can click receive messages ID for info on when sent, first received size, receive count etc, the body and the attributes. 

Can pull for message again which increases receive count,  can then click the message id and delete it which signals to SNS that we have processed the message. 

Can then send another message multiple times and pull and it’ll display the same number of messages available. 

Purge will delete all messages in the queue great in development but not production.
Monitoring gives info on some metrics to help you decide how to scale it e.g of oldest messages etc.

Can view access policy, encryption and dead letter queue if we set one on tabs.  
