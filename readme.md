1. Labs Created multiple budgets with IAM permissions on AWS console.


2. launching website on EC2 Labs - 
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

3. Security Groups Troubleshoot a website not loading.
Solved a timeout error.
Tip - if timeout ever occurs this is 100% because of an EC2 security group issue. E.g Try to HTTP query or SSH into it and it doesn’t load.
Solution: Add HTTP rule with source anywhere. → Inbound rule fix.
Can do it with any type and any source/ip → E.g our own IP.

4. Accessing EC2 with IAM credentials.
Practice IAM roles with SSH.
Dont run aws configure and enter your personal details because anyone who connects to that ec2 can find that info. Rule of thumb - Never enter your personal access or secret access key. What we do instead is use IAM roles.
So create an IAM role if one doesn't exist already, attach it to the EC2 by going to action → security → modify IAM role → pick the role to attract.
Now if you do 
Aws iam list-users.
You can then have access to the ec2 instance.
If you try to run the same aws iam list-users command after detaching the role, you’ll get an Access Denied.

5. EC2 Instance Launch type - Spot fleets / Instance Hands on.
Launched Spot Fleet.
Learnt how to launch a spot instances through ec2.

