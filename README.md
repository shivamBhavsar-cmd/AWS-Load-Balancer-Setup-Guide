![Screenshot 2024-07-08 204220](https://github.com/shivamBhavsar-cmd/AWS-Load-Balancer-Setup-Guide/assets/63534308/6f6f215c-5962-4bbd-9d80-22ae04559eba)![Screenshot 2024-07-08 204736](https://github.com/shivamBhavsar-cmd/AWS-Load-Balancer-Setup-Guide/assets/63534308/1fd667b9-2224-4952-8e08-6e345da860d9)# AWS-Load-Balancer-Setup-Guide
AWS Load Balancer Setup Guide
# AWS Load Balancer Setup Guide

This guide provides step-by-step instructions for setting up an AWS load balancer with an EC2 instance running a web server.

## Prerequisites

- AWS account
- Basic knowledge of AWS EC2, AMI, and Load Balancers

## Step 1: Create an EC2 Instance

1. **Launch Instance**
   - Name the instance: `web01_load_balancer` (or any name you prefer)
   - Select the AMI: `Amazon Linux (free tier)`
   - Instance type: `t2.micro (free tier)`
   - Create a new key pair
     ![Screenshot 2024-07-08 201622](https://github.com/shivamBhavsar-cmd/AWS-Load-Balancer-Setup-Guide/assets/63534308/7cf22659-2d79-4b90-85e1-98ad81176c0f)


2. **Configure Security Group**
   - Security group name: `wavecafe-web`
   - Edit Inbound traffic rules as needed
   ![Security Group Configuration](Images/Screenshot 2024-07-08 201622.png)

3. **Advanced Details**
   - Paste the following script in the User Data section:

     ```bash
     #!/bin/bash
     # Variable Declaration
     PACKAGE="httpd wget unzip"
     SVC="httpd"
     URL='https://www.tooplate.com/zip-templates/2098_health.zip'
     ART_NAME='2098_health'
     TEMPDIR="/tmp/webfiles"

     yum --help &> /dev/null
     if [ $? -eq 0 ]; then
         # CentOS Setup
         PACKAGE="httpd wget unzip"
         SVC="httpd"
         sudo yum install $PACKAGE -y > /dev/null
         sudo systemctl start $SVC
         sudo systemctl enable $SVC
         mkdir -p $TEMPDIR
         cd $TEMPDIR
         wget $URL > /dev/null
         unzip $ART_NAME.zip > /dev/null
         sudo cp -r $ART_NAME/* /var/www/html/
         systemctl restart $SVC
         rm -rf $TEMPDIR
         sudo systemctl status $SVC
     else
         # Ubuntu Setup
         PACKAGE="apache2 wget unzip"
         SVC="apache2"
         sudo apt update
         sudo apt install $PACKAGE -y > /dev/null
         sudo systemctl start $SVC
         sudo systemctl enable $SVC
         mkdir -p $TEMPDIR
         cd $TEMPDIR
         wget $URL > /dev/null
         unzip $ART_NAME.zip > /dev/null
         sudo cp -r $ART_NAME/* /var/www/html/
         systemctl restart $SVC
         rm -rf $TEMPDIR
         sudo systemctl status $SVC
     fi
     ```

4. **Launch the Instance**
   - After launching, wait for the instance to initialize.
   - Access the website using the instance's Public IPv4 address.
   ![Website on EC2](Images/Screenshot 2024-07-08 201622.png)

## Step 2: Create an AMI from the Instance

1. **Create AMI**
   - Select the instance
      ![Screenshot 2024-07-08 202601](https://github.com/shivamBhavsar-cmd/AWS-Load-Balancer-Setup-Guide/assets/63534308/c37d3a09-e3d9-451e-a1ee-38518fa919c2)

   - Go to Actions > Image and templates > Create image![Screenshot 2024-07-08 203849](https://github.com/shivamBhavsar-cmd/AWS-Load-Balancer-Setup-Guide/assets/63534308/8ca88625-615f-4580-a2a2-06864ebc97eb)

   - Provide an Image name and modify instance volume if needed
   - Click on `Create image`
   - Check the created AMI in the AMI section
   ![Create AMI](images/create-ami.png)

2. **Launch an Instance from the AMI**
   - Click on `Launch instance from AMI`
   - Follow the steps to launch a new instance
   ![Launch from AMI](images/launch-from-ami.png)

## Step 3: Create a Launch Template

1. **Launch Template**
   - Select the AMI
   - Choose the instance type and key pair
   - Create the template
![Screenshot 2024-07-08 204144](https://github.com/shivamBhavsar-cmd/AWS-Load-Balancer-Setup-Guide/assets/63534308/78fb002c-a7ea-4851-a8db-61e157c6b47f)

![Screenshot 2024-07-08 204746](https://github.com/shivamBhavsar-cmd/AWS-Load-Balancer-Setup-Guide/assets/63534308/6a16ab02-347c-4a91-a7a5-894ddb5fa083)
![Screenshot 2024-07-08 204736](https://github.com/shivamBhavsar-cmd/AWS-Load-Balancer-Setup-Guide/assets/63534308/d783a67e-e182-4ef6-b56c-e7b544c895b0)
![Screenshot 2024-07-08 204746](https://github.com/shivamBhavsar-cmd/AWS-Load-Balancer-Setup-Guide/assets/63534308/d6732c8c-e67f-4707-bfc9-cccae3d0dbd7)
![Screenshot 2024-07-08 204758](https://github.com/shivamBhavsar-cmd/AWS-Load-Balancer-Setup-Guide/assets/63534308/07645f65-568f-4d63-9dc4-81c9b4d2eee3)
![Screenshot 2024-07-08 204809](https://github.com/shivamBhavsar-cmd/AWS-Load-Balancer-Setup-Guide/assets/63534308/320a36cd-da1e-4a42-ad17-3a3796475349)
![Screenshot 2024-07-08 204822](https://github.com/shivamBhavsar-cmd/AWS-Load-Balancer-Setup-Guide/assets/63534308/8981ebb2-308f-4fcd-ade9-cae65dd56f46)
![Screenshot 2024-07-08 204917](https://github.com/shivamBhavsar-cmd/AWS-Load-Balancer-Setup-Guide/assets/63534308/843aeba3-5798-4c64-b74b-65c26e6526d9)

## Step 4: Create a Load Balancer

1. **Create Target Group**
   - Create a target group and select the instances
   ![Create Target Group](images/create-target-group.png)

2. **Create Load Balancer**
   - Select `Application Load Balancer` and create
   - Configure a new security group
   - Add the instances to the target group
![Screenshot 2024-07-08 205205](https://github.com/shivamBhavsar-cmd/AWS-Load-Balancer-Setup-Guide/assets/63534308/789682aa-0df9-47fb-a80a-299f05af205d)
![Screenshot 2024-07-08 205540](https://github.com/shivamBhavsar-cmd/AWS-Load-Balancer-Setup-Guide/assets/63534308/6ad32a0d-6fcb-4ec0-a572-48764d72d8bc)

3. **Configure Security Group**
   - Edit the inbound rules of the security group![Screenshot 2024-07-08 205700](https://github.com/shivamBhavsar-cmd/AWS-Load-Balancer-Setup-Guide/assets/63534308/5752b0d5-7e6b-4381-a82e-d0d01f3ddb76)

   ![Configure Security Group](images/configure-security-group.png)
![Screenshot 2024-07-08 205621](https://github.com/shivamBhavsar-cmd/AWS-Load-Balancer-Setup-Guide/assets/63534308/c27029e1-2a7d-458a-a450-cba59915a0d0)
![Screenshot 2024-07-08 205635](https://github.com/shivamBhavsar-cmd/AWS-Load-Balancer-Setup-Guide/assets/63534308/849e8066-b1a7-4a42-8492-a51bc6e24d1b)

## Step 5: Test the Load Balancer

1. **Check Targets**
   - Go to the target group section and ensure the targets are healthy![Screenshot 2024-07-08 205953](https://github.com/shivamBhavsar-cmd/AWS-Load-Balancer-Setup-Guide/assets/63534308/9d4a790e-7104-47f6-8d38-f4c4ad255ec8)

   ![Check Targets](images/check-targets.png)

2. **Access the Load Balancer**
   - Copy the DNS name of the load balancer and paste it into a browser![Screenshot 2024-07-08 210107](https://github.com/shivamBhavsar-cmd/AWS-Load-Balancer-Setup-Guide/assets/63534308/737f6bd8-97fa-4453-b59b-670e0e700590)
![Screenshot 2024-07-08 211221](https://github.com/shivamBhavsar-cmd/AWS-Load-Balancer-Setup-Guide/assets/63534308/4cb39317-fe08-46e1-8535-6c3542e1562e)

   - Verify that the website is working
   ![Load Balancer Website](images/load-balancer-website.png)![Screenshot 2024-07-08 211207](https://github.com/shivamBhavsar-cmd/AWS-Load-Balancer-Setup-Guide/assets/63534308/3e3e7925-301e-4db1-89a6-e2f66a7203c3)

![Screenshot 2024-07-08 211350](https://github.com/shivamBhavsar-cmd/AWS-Load-Balancer-Setup-Guide/assets/63534308/4feaa919-63f7-4043-84b7-2d77b0a85c48)

## Conclusion

By following these steps, you have successfully set up an EC2 instance, created an AMI, configured a launch template, and set up an application load balancer in AWS.

## Author

- [Shivam Bhavsar]

