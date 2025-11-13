## **Managing AWS Resources: Optimize Utilization – Café Web Application**


## **Overview**

This lab demonstrates how to optimize AWS resources by removing unnecessary components and right-sizing compute instances to reduce operational costs.
I acted as Sofía, the Cloud Engineer responsible for improving the Café web application efficiency after its database migration to Amazon RDS.

After migrating the database, I identified optimization opportunities:

Removing the local MariaDB instance (no longer needed).

Downsizing the EC2 instance type from t3.small to t3.micro.

These steps align with real-world cost optimization best practices in the AWS Well-Architected Framework (Cost Optimization Pillar).

## **Objectives & Learning Outcomes**

After completing this lab, I was able to:

Optimize an Amazon EC2 instance for cost efficiency.

Uninstall unnecessary software to reduce storage and CPU load.

Use the AWS CLI to resize and manage EC2 instances.

Estimate AWS service costs using the AWS Pricing Calculator.

Identify cost-saving opportunities through resource right-sizing.

## **Architecture Diagram**

<img width="1536" height="500" alt="9f574742-2d5a-4dc1-9939-ca18ad9756d2" src="https://github.com/user-attachments/assets/d9d2ddc1-5b66-44b0-b3be-abf3549a6993" />

## Before Optimization:

Café EC2 instance (t3.small) hosted both the web app and a local MariaDB.

Increased CPU usage and higher storage (40 GB).

## After Optimization:

Local database removed.

EC2 downsized to t3.micro (20 GB storage).

Amazon RDS (db.t3.micro) now hosts the external database.

Reduced monthly EC2 cost by ~50%.

## Cost Estimation Results (AWS Pricing Calculator)
Scenario	EC2 Type	EBS (GB)	RDS Type	Monthly Cost (USD)
Before Optimization	t3.small	40	db.t3.micro	$35.60
After Optimization	t3.micro	20	db.t3.micro	$25.18

Monthly Savings: ≈ $10.42 (29% reduction)


 ## **Command and Steps**
 ```bash

# 1️⃣ Connect to Café Instance (replace <public-ip>)
ssh -i labsuser.pem ec2-user@<public-ip>

# 2️⃣ Stop and remove local MariaDB
sudo systemctl stop mariadb
sudo yum -y remove mariadb-server

# 3️⃣ Switch to CLI Host and identify Café instance
aws ec2 describe-instances \
  --filters "Name=tag:Name,Values=CafeInstance" \
  --query "Reservations[*].Instances[*].InstanceId"

# Save instance ID
CafeInstanceID=i-0abcd123456789xyz

# 4️⃣ Stop the Café instance
aws ec2 stop-instances --instance-ids $CafeInstanceID

# 5️⃣ Modify the instance type (t3.small → t3.micro)
aws ec2 modify-instance-attribute \
  --instance-id $CafeInstanceID \
  --instance-type "{\"Value\": \"t3.micro\"}"

# 6️⃣ Restart the instance
aws ec2 start-instances --instance-ids $CafeInstanceID

# 7️⃣ Verify the instance is running and note new public DNS/IP
aws ec2 describe-instances \
  --instance-ids $CafeInstanceID \
  --query "Reservations[*].Instances[*].[InstanceType,PublicDnsName,PublicIpAddress,State.Name]"

# 8️⃣ Test web app
# Visit in browser:
# http://<Downsized CafeInstance Public DNS Name>/cafe

```



## **Screenshots**

Showing successful removal of MariaDB.
<img width="791" height="500" alt="Uninstall MariaDB" src="https://github.com/user-attachments/assets/bf200d44-e640-4234-b4d6-8ccaf8ea8389" />

CLI confirmation of instance type change.
<img width="932" height="93" alt="Cli modify instance" src="https://github.com/user-attachments/assets/6e5dad26-a701-471f-b9a8-5caada166c71" />
<img width="902" height="249" alt="instance type change to t3 micro" src="https://github.com/user-attachments/assets/d018ed74-805c-42fd-9801-7b9cacc70b31" />

Café app running successfully post-optimization.
<img width="1479" height="906" alt="cafe webapp after optimization" src="https://github.com/user-attachments/assets/2b126c5c-dbfa-4497-89d2-bf648ff4e962" />

PricingBeforeAfter.png	AWS Calculator comparison showing cost reduction.
<img width="1847" height="456" alt="pricing before optimization" src="https://github.com/user-attachments/assets/dd6f472d-07d0-4977-850a-0bae9fc2b46b" />
<img width="1240" height="432" alt="pricing after optimizating" src="https://github.com/user-attachments/assets/9a278f62-f3d5-416a-a9f1-c8945ab82dba" />

## **Key Takeaways**

Right-sizing reduces costs without affecting performance.

Removing decommissioned services (like local databases) lowers storage and CPU load.

AWS Pricing Calculator helps forecast cloud spending and justify optimization decisions.

Continuous resource monitoring helps prevent waste and improve ROI.

This lab reflects AWS best practices for ongoing cloud cost optimization.


## **What Actually Happened**

I,

Connected via SSH to the Café EC2 instance using key authentication.

Stopped and uninstalled the MariaDB local database to reduce resource usage.

Identified instance ID using the describe-instances command.

Stopped the EC2 instance, modified its type from t3.small to t3.micro.

Restarted the instance and verified the new DNS name and IP.

Tested the Café web application — confirmed functionality.

Used the AWS Pricing Calculator to compare pre- and post-optimization costs.

Calculated savings: $10.42/month, proving optimization effectiveness.



## Author: 

Amarachi Emeziem

### Tools Used: AWS CLI, EC2, RDS, EBS, Pricing Calculator, Linux SSH
