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
