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
