# AWS 101

The First thing you need to do anything in AWS is an AWS account so you have to [Register/sign up](https://portal.aws.amazon.com/billing/signup) for an account in AWS. To make an account in AWS you need an email valid address and credit card information with you follow the instruction to make an account.

![AWS SignUp Form](https://user-images.githubusercontent.com/85181215/191954517-e8c8802f-ea61-434b-a0b8-83af0dd47811.png)

When you make your AWS account with your email address and that email address is for your root account in AWS. A root Account is a Higher Privileged account that has extreme power to do any thing there so you have to secure that account.

When you set up your root account after that you have to set up an IAM (Identity And Access Management) Account. The IAM accounts are used when you have multiple people in your organization and you want each account to have different levels of permission so that's why the IAM account is useful to set up. IAM Account required an Account ID of the AWS account and you get that when you set up your root account. After that, you provide the IAM user name that is the user you want to add, and to secure that account you provide also a password for that account.

![IAM Account Setup](https://user-images.githubusercontent.com/85181215/191954609-39460bba-05bd-4dfb-98e8-90b7c9016265.png)

When you set up all of that, then you can log in to your AWS account using your root email address, or if you set up an IAM account you can log in with that also.

After Successful login, you will able to access your AWS Console that looks like this.

![AWS Console](https://user-images.githubusercontent.com/85181215/191954692-d0ee5d19-2c0b-4ddd-8bb5-86205c58d32b.png)

In your AWS Console/Account you can pick any AWS Services, choose your region and configure your service there. For the sake of simplicity I will only explain VPC Sections and In the VPC we can talk about other services that are useful to make a Virtual Cloud.

## AWS Account
As we set up our **AWS Account** so now we talk a little more about what is inside that account. So let's started. 
The first question is in your mind is that **why do we need more than one AWS account?** and **Why could one account is not enough?** so the first thing is AWS IAM is the place where I can make the different user account and give them permission to do things in the AWS account. There could be more than one person that uses our AWS account and we don't want them they have our Root Account which has Higher Privileges that we don't want to give to others so that's why we make more AWS accounts and give them special permission to do there work that relates to its role in our organization i.e For Example John is our DevOps Engineer so we make an account and give them permissions to run Virtual Machines(EC2 Instances in AWS) for there development purposes, on the other hand, Bob is our Network Engineer so we give them permission to run and manage VPC and other network related services.

Some Important things to remember about AWS accounts are the following.

- **AWS Bill**
	One thing you have to know that is every AWS Account has its own AWS Bill. That bill AWS will charge for every service that users use in that account.
- **VPC**
	Every VPC Bob will create will only exist in that account. They can enable VPC sharing and share its subnet with other accounts.
- **Service Limits**
	Every Account has some Service Limits. There are some kinds of quotas in that account, for example, the number of EC2 Instances that can run on the VPC and the number of VPCs can be deployed in one region, etc. Some of them are **Soft Limits** that can be increased if you asked AWS to increase that for you but others are **Hard Limits** that cannot be increased and you have to keep that in mind.

## Business Unit
Larger Enterprises have different businesses in multiple countries and regions. So Different businesses required different policies. To manage that we can consolidate it into **Business Unit**. We make specific Business Units for each country and add policies for that Unit specific. All the things we discussed in the AWS Account are added to Business Unit so each Business Units have its own AWS Accounts, IAM, roles, and users. Each Account has there own AWS Bill, VPC, and Service Limits.

So let's summarize Business Unit's benefices with an example let's say we have two Business Units (Business A and Business B). Each has there own IAM Accounts. Business Unit B has other sections like Production, Development, Testing, and QA. And if one user from the production Account breaks some things this will only affect that Accounts services so another account will not get any effect for that account.

![AWS Business Units](https://user-images.githubusercontent.com/85181215/191954755-472daa30-7731-46f7-9ef1-7b4f93d38780.png)


## AWS Organizations

In our enterprise, we have so many Business Units and each has there own AWS Accounts with its own bills How we can manage all of the costs and bills, How we can manage all the policies? We have to manage all the bills and Policies individually which is quite cumbersome. To Help That Problem we have AWS Organizations. To use AWS Organization we have to make an account that we called Management and Administration Account In that account we make our AWS Organization. The First thing we do in AWS Organization is to define Business Units for each business. So If someone makes an account in that business we have to invite that to our Organizational Business unit. After that, we get a Consolidated AWS Bill that combined all of the business costs in a single bill. For Policy Standpoint we also have Service Control Policies. Each Business Units have its own policies but with the help of the AWS Organizations Service Control Policy, we can define a high level of policy that overwrite all of the IAM policy in that business unit's accounts. For example, if we want to restrict making a VPC on a specific region we can make a policy that denies that so even Business Unit allows making VPC on any region they cannot able to deploy any VPC in that region.

![[AWS Organizations.png]]

**Resource Sharing**

After you successfully configure AWS Organizations now can configure Resource Sharing with other Business Units i.e VPC Subnets sharing with other accounts so they can deploy their EC2 Instance in our CIDR (Classless Inter-Domain Routing) range and become a part of that network but you still maintaining your VPC. To configure Resource Sharing you use AWS Resource Access Manager.

![[AWS Resource Sharing.png]]

## AWS Region

From a single AWS Account, you can deploy AWS Services in any AWS Region unless you are restricted by some policy. **AWS Region** is a place in the world where AWS made its Data Centers and Organize them into AWS Region. All these Regions are connected to AWS's global backbone and provide high availability to its customers. 

![[AWS Regions.png]]

Now, let's look deeper into the AWS Region. A region is made up of more than one **Availability Zone (AZ)** and each AZ has more than one AWS Data Center that is connected to each other with fast internet connections to provide high availability. All the Data centers in the Availability Zone are in the same failure domain so they may experience power failures. They are also in the same flood plans and tornado plans. So that's why AWS made multiple AZ in the region that are on different flood plans or failure plans so if for any circumstance one Availability Zone suffers from catastrophic conditions other AZ continue their work and give their services to the customers.

![[AWS Availability Zones.png]]

So when you create any VPC you select which region you want to deploy and specify which AZ to use for running AWS services by creating a subnet and assigning that subnet to that AZ and now you can run your Services like EC2 instances there. Customers use the best practices by deploying their application into multiple Availability Zones so if one zone is fail another zone still works and their application gets high availability.

## Amazon Virtual Private Cloud (VPC)

The most used or fundamental service in AWS is AWS VPC (Amazon Virtual Private Cloud). So You have an AWS Account and in that account, you can deploy your AWS VPC. That VPC is only available in that account. VPC only exits in a region so the first thing you will do is select an AWS Region where you want to deploy your VPC. As we also know a Region has Availability Zones and our VPC exits in that Availability Zones. When we deploy our VPC there we assign a network/CIDR range for our VPC. AWS also can automatically assign Network Range but that is not recommended. Now we create subnets VPC Subnets and those Subnets only exist in Availability Zones. So when creating subnets you select which Availability Zone you want to assign that. When subnets are configured, now to can start your EC2 Instance (Virtual Machines in AWS) in that VPC subnet. Thant EC2 instance gets an IP address from that subnet. One thing to remember is that a subnet can be Public or Private. **Public Subnets** is accessible to the internet and **Private Subnets** are not accessible in that way.

You can deploy many VPCs in one account and region but there is a default limit to deploy VPCs. You can only deploy 5 VPCs in one region but that's a **Soft Limit** and that can be raised when you asked AWS to raise them for you.

![[AWS VPC Subnets EC2.png]]

Now we have our VPC setup and our Instances are launched so now we are moving to other AWS Services that are used in the VPC and that have their specific purposes.

**1. Flow Logs**
Flow Logs are the AWS services that can be enabled in the VPC that can keep track of all the sessions and traffic going in and out from the VPC and their EC2 Instances. So we can see it or visualize it for other security and analytical purposes.

![[AWS Flow Logs.png]]

Each business has multiple AWS accounts and each account has multiple AWS VPCs and subnets. To get log data in all of them we have to enable Flow logs in each of them. Flow Logs are just a stream of text data so every VPC has its own flow log data. So if we want to Aggregate all of them for visualization and search for a specific event we use different AWS services for that.  

![[AWS Flow Logs Aggregation.png]]

In the above image, you can see all the AWS services that are used in data aggregation. The step for data aggregation and visualization by AWS looks like this.

![[AWS Flow Logs Aggregation Services steps.png]]

**2. Elastic Network Interface (ENI)**
Every Computer Required **Network Interface Card (NIC)** card to talk and connect with other computers in the network so EC2 instances are not different they also need NIC like thing but in AWS we called it **Elastic Network Interface (ENI)**. When you create an EC2 Instance ENI will automatically assign to that instance and get the IP address from the subnet range they were attached to. You can create your own ENI and assign them IP Addresses manually and assign that to your EC2 instance.  We called it Elastic because it can be detached and attached to other EC2 Instances. When you detach ENI and connect to other instances then all the configurations applied in that ENI will remain the same so the instance that attaches that ENI has the same network configuration they were detached.

Some Common ENI Properties you should know are the following.
- **Elastic IP Address (EIP):** You can also assign Public IP Address to your ENI with Elastic IP Address (EIP). So if you want EIP you have to request for EIP and then associate that to your ENI. When you do that your Instance becomes Public and accessible through the internet. 
- You can have multiple ENI attached to your EC2 Instance and the number of ENI you can have depends on the EC2 Instance type and size you are using.
- One thing to remember in ENI is that ENI and EC2 instances mush is in the same Availability Zone. If your EC2 instance lies in a different AZ and your ENI from a different AZ then you cannot connect that to each other, you will get an error if you want to do that.
- Each ENI has an identifier and you will use that Identifier in the VPC Route Table and for some other services we discussed just in a moment.
![[AWS Elastic Network Interface.png]]

**3. VPC Security Groups and NACLs**
- **Security Group**
	Security Group is a Simple host-based firewall instance that has simple firewall functionality that we have to configure in AWS **Security Groups**. They control Network Traffic Coming in and Going out from our EC2 Instances. They just allow rules that allow certain traffic according to our rules and by default they deny all traffic coming from outside. They are attached to your ENI and they are required when you launched your Instance and ENI. You can assign many security groups in one ENI and also you can assign EC2 instances if an ENI is a part of any same security groups.
	Some Common things about Security Groups are the following.
	- By default all outbound(Going out from the instance) traffic will allow.
	- Security Groups are like a stateful firewall which means they check and keep track of complete connections.
	- To make a Security Group rule you have to assign which Source IP address to allow and which Destination IP address to deny and vice versa or you can specify which security group to allow and which to deny and vice versa.
- **Network Access Control List (NACL)**
	NACL is similar to Security Group but they protect the VPC Subnet instead of the EC2 instance.  They are also created by default when you create a subnet and by default, they allow all inbound and outbound traffic. NACL has both allow and deny rules. NACLs are like stateless firewalls so they don't keep track of full connection. You can make NACL rules by defining which IP Address and Range to allow and which to deny.

![[AWS Security Group.png]]

**Example**
Let's take an example case 1 where Instance B wants to talk to instance C in the private subnet 1. So by default, all the traffic going out to the instance is allowed so instance B successfully communicate to instance C as they allow all the traffic from security group 1.

![[AWS Security Group Example case 1.png]]

Now case 2 where Instance C talks to instance D that is from a different Subnet. First Instance C Traffic comes to NACL that by default allows all inbound and outbound rules so they pass the traffic to NACL of subnet 2 and they also allow it because of the default rule and pass that traffic to security group 3 of subnet 2. Now Security Group 3 denies all inbound traffic by default so they don't allow it.

![[AWS Security Group Example case 2.png]]

**4. VPC Route Tables**
VPC Route Tables are like simple router route tables there they route traffic to one network and subnet to other networks/subnets. In AWS you must have a routing table attached to the subnet. Like we said before Route Table has a list of destination addresses and they route your traffic according to that list. By Default Route Table only have a local route `10.1.0.0/16 -> local`. That basically says if any traffic that has addresses in this range is local so they route that traffic locally to its subnet.
Common things to Remember about Route Tables are the following.
- VPC has many route tables but one subnet has only one route table.
- One Rote Table can be assigned to one or more subnets.
- Route Table can be used to treat subnets differently. For Example, if we want one subnet is publicly accessible and others are private we can do that by making a default route to **Internet Gateway** i.e `0.0.0.0/0`. So any instance in that subnet has a connection to the internet and if any instance has public IP address they are also accessible through the internet.
- Route Tables are not dynamic which means you have to add all the entries manually except you are not using Virtual Private Gateway (VGW) propagation that we will discuss in a moment. 
	- **Internet Gateway  (IGW)**
		Internet Gateway is an AWS service that provides internet connection in the VPC.

![[AWS Route Table.png]]
	- **NAT Gateways**
		If we want one subnet can use the internet but we still want to make that subnet private we use NAT Gateway. NAT (Network Address Translation) Gateway is the AWS service that provides Internet connection but in a private manner like our home computers. In thing to note is that NAT Gateway always connects to the Public Subnet we have to make a route table entry that has a default route to NAT Gateway.
![[AWS NAT Gateway.png]]
		**Security Considerations in NAT/IGW**
		NAT and IGW will allow any traffic without any questions. So that makes a security risk because you didn't want to blindly trust all the IP Addresses and domains so for better security we have to use other AWS services instead of NAT and IGW.

The AWS add some new capabilities in Route Tables and that is a most specific route which can add route entry that is pointing to the next hop, in that case, ENI of subnet 2 will ENI acts like an intermediary system and forward the traffic to another system in that subnet. So using that you can talk to other instances in different subnets.
![[AWS Most Specific Route Table .png]]

In AWS configured Route Tables look like the following.
![[AWS Configured Route Tables.png]]

**5. Virtual Private Gateway (VGW)**
The VGW is the AWS Service that is used to connect VPC to our On-premises data center using a private network connection. To connect our VPC to the On-premises data center we create and assign a VGW in our VPC. When we do that we can configure site-to-site IPSec VPN to connect that VPN to our on-prem data center router or customer gateway(cgw). When our VPN is set up we can run Border Gateway Protocol (BGP) to share/propagate routes to our VPC. By route propagation all the route VGW learn are directly added to our route tables.
Some important considerations for VGW are the following.
- Route Propagation has a hard limit of 100 routes. So you have to keep that in mind for doing route propagation. One thing you can do to add a route table entry that says every traffic from `10.9.0.0/16` is routed to our VGW. By doing that we can use our VPC and On-Prem  Data Center without route propagation but we have to add that manually.
- The IPSec VPN tunnel has only 1.25 Gbps throughput and that's also a hard limit. You can create more the one IPSec VPN tunnel and do some load balancing to increase throughput but that makes your network more complex and difficult to troubleshoot.

When you set up VGW correctly then you can access your VPC EC2 instance through your On-Prem Data centers.
![[AWS VGW.png]]

**6. AWS Direct Connect (DX)**
Above we use VGW to connect our On-Prem Data Center to our VPC but that's the one way to do that. The other way to connect our On-Prem Data Center to our VPC is to use AWS Direct Connect. AWS Direct Connect is a service where you can connect your On-Prem data center to your AWS VPC using a physical dedicated connection to the AWS Direct Connect facility. You have to buy the fiber connection that has a connection speed of 1, 10, or 100 Gbps depending on the Direct Connect Location you are using, and connect that with your on-prem router and AWS Direct Connect facility. So when you did that configure a Direct Connect Gateway (DXGW) from your AWS console and connect that with the AWS Direct Connect facility and then associate your DXGW with your VPC Virtual Gateway (VGW). When all that is set up then we run BGP to propagate routes that have the same 100 route hard limit as discussed before. So if we run more than 100 routes it will break everything. The only advantage of DX is they have more speed than IPSec VPN.
![[AWS Direct Connect.png]]
They also have one disadvantage, In IPSec VPN we have end-to-end encryption but In the DX connect we don't have that. We can enable encryption but that's limited because DX uses only MACSec Encryption. MACSec encryption only encrypts data when they travel through the cable and when they come in the Direct Connect facility they will decrypt and all the data are in plain text in that facility. When they travel through the facility then they are again encrypted with MACSec and decrypted in Direct Connect Gateway.
![[AWS Direct Connect MACSec.png]]
So from the security standpoint, we should use end-to-end encryption but the only downside to that is only 1.25 Gbps speed so we have to trade off with security and performance.

## VPC Peering
All this time we discussed when is VPC, EC2 instance, Route Tables, and how to connect our VPC to the On-Prem data center. But now we look at how we connect our VPC to other VPCs from the same or different regions. If the VPCs are in the same region then we just create a VPC Peering Connection from the AWS console and peer all of them now all the VPCs are directly connected to each other and they can communicate with each other using that peering connection. And Now If the VPCs are from a different region then again we use the same peering connection and connect that to each other and they can communicate with each other like one before. But there is one problem with using VPC peering connection they will only connect VPCs that are directly connected. So Other VPCs will not communicate with them even if they are connected to a VPC that can communicate with them.
![[AWS VPC Peering 1.png]]
So to connect all of them we have to make full mesh. The problem is that they are hard to make and manage when you have 100 of VPCs. Furthermore, there is a hard limit, One VPC can only have 125 peering connections and we have to keep that in mind. Every VPC also needs a routing table that has all the peering routes to transfer the traffic.
![[AWS VPC Peering full mesh.png]]

## AWS Transit Gateway (TGW)
VPC Peering gives us some problems when we are in large organizations. To overcome that problem we use AWS Transit Gateway (TGW). To connect our VPC to other VPCs without any complications we create the TGW in our region and connect all our VPCs in that region with them. We do the same process with other regions because TGW only enable in the same region so every region has its own TGW. When both regions have set up TGW then we connect both of them using TGW Peering after that they can communicate with each other.
> **Note:** You cannot connect the VPC that has the same/overlap IP range that is already connected.

TGW has static routing so we have to configure all the routes manually and correctly. In the same manner, you also configure the route tables of the VPCs. The security consideration of TGW is we have limited visibility when traffic goes through TGW so we cannot get any packet captures in TGW. TGW only allows 50 Gbps throughput from one VPC to another.
![[AWS TGW.png]]

**Inner Networking of TGW**
TGW have route tables and we have to configure them manually. We can create only one route table if we want or we can create up to 20 route tables and that's the hard limit. The question is why do we need more than one route table in TGW?, So the answer for that sometimes we need to segment/isolate our traffic for some specific VPC or we want to route our traffic through the firewall first to do that we need to create more route tables manually.

So let's take an example case where we have 5 VPCs and our On-Prem data center and we want to create connectivity for all of them through a firewall.
![[AWS TGW Inside Case.png]]

So the first thing I do is to create VPC attachments to all of them and each attachment has its own number. After that, the next thing I will do is associate all the route tables with that attachments. After that, we do route propagation so every route is propagated in that route tables but remember only the Directly connected VPCs attachments routes are propagated. we can also propagate On-Prem data center routes that we get through BGP in the TGW Route Tables. When all the routes are propagated then we create our static routes in our VPC route table and TGW route tables. So if we want all the traffic first to go to the firewall then go anywhere else then we add his static route in all of the route tables so now every traffic will go through the firewall first and then go anywhere they want. If we want to propagate all the routes to our On-Prem data center we can also do that but we can only propagate 20 routes and that's the hard limit we cannot raise so if you have more than 20 routes you have to configured address summarization in your DX. 
> **Note:** You can also add the default route also so your internet traffic also goes through the firewall that we didn't able to do with IGW.

![[AWS TGW Inside Case Configurations.png]]

**Inside of Appliance VPC**
Now we look inside Appliance VPC where we have our firewall. In appliance VPC we have other AWS appliances like AWS Load Balancer IPS/IDS etc that have their different purposes.

**AWS Gateway Load Balancer (GWLB)**
AWS Gateway Load Balancer (GWLB) is used to manage our load in the cloud. They will decide which traffic to send to which firewall to get high availability. I'm not going to deep dive into that but they will look like the following.
![[AWS GWLB.png]]

## AWS Local Zone
AWS Local Zone is the smaller version of the Availability Zone that helps those customers that are far from AWS Availability Zones. Let's say our application is in **Oregon** and our customers are in **Los Angeles** they use our application and face some latency compare to customers who live in **Oregon**. So we connect **Los Angeles** to AWS Local Zone and attach that Local Zone to the AWS region. When we create a Local Zone then they extend our region and provide lower latency to our customers.

![[AWS Local Zone.png]]

**Inside of Local Zone**
Local Zone Just takes your VPC and extends that VPC in your Local Zone. Basically extending the VPC closure to customers. The way to use Local Zone is to create a subnet in the local zone and that subnet is only available in that zone. The thing we cannot do is take a VPC subnet and extends that to the local zone there is no term that explains stretching subnet. we can do that with other VPCs also. Local Zone doesn't have all the AWS services there are only a few services you will get in the local zone. One thing I will like to mention that doesn't exist in the local zone is Transit Gateway (TGW) to use TGW we have to go back to the AWS region and route traffic through that into other local zones.
![[AWS Local Zone TGW.png]]
The thing we can do is make a new subnet in the same VPC and route their traffic to each other so they can communicate with each other or we can also use VPC Peering to connect to other VPCs.
![[AWS Local Zone Peering.png]]

## AWS Wavelength Zones
The Wavelength Zone is like the Local Zone that extends the AWS region to the user but this time they are for Mobile Users through wireless carriers. As mobile users traffic goes to different wireless carriers and then goes to the AWS Datacenter because of that face very high latency and low throughput.
![[AWS Wavelength Wireless Carrier.png]]
So wavelength zone makes the Availability Zone that builds inside the wireless carrier network. Because of that, you can launch your instance inside the carrier's network and now mobile users get low latency and high throughput.
![[AWS Wavelength Zone.png]]
Same thing we did in a local zone not all the AWS services available there so we can do the same things to create VPC subnets in the wavelength zone and connect all of them using VPC peering.

## AWS Outpost
We mentioned earlier that we can connect the On-Prem data center using DX but to get high throughput and low latency we can use AWS Outpost. AWS Outpost allows you to run AWS Services i.e EC2 instances in the AWS racks that can be installed in the customer's On-Prem Network to provide low latency. This is also like a Local Zone that can extend AWS Region. All the concepts we explain for creating new subnets and connecting them through peering can be applied here also. 
![[AWS Outposts.png]]

## AWS Transit Gateway - Operational and Visibility Challenges
![[AWS TGW Operational Chanllenges.png]]

## AWS Transit Gateway Connect
AWS Transit Gateway Connect (TGW Connect) is the service that lets you connect through other VPCs using Generic Routing Encapsulations (GRE) Tunnel. **GRE is a protocol for wrapping data packets inside secondary data packets in order to set up a direct point-to-point network connection.** The Question is why do we want to do that? So the answer is to consider we have a software-defined network in the VPC. A software Defined Network is a virtual Network that is running inside our EC2 instance. So how we can connect through that network and exchange routes so to do that we use Transit Gateway Connect. First, we do is to connect all our VPCs and the VPCs that have **Network Virtual Appliance (NVA)** to TGW like we did before nothing has been changed there. After that, we defined the TGW Connect Attachment when we did that we do TGW Connect peering that made the GRE tunnel between NVA and TGW. In that tunnel, we run BGP to routes. One thing to note in GRE tunnel TGW can propagate more routes than we see before. In Simple TGW to only propagate 20 routes but in GRE tunnel you can propagate up to 5000 routes and NVA propagates up to 1000 routes.
![[AWS TGW Connect.png]]

TGW Connect support Direct Connect (DX) to connect our VPC to our On-Prem Data centers. So In Simple DX connection, we have only 100 route propagation to our VPC and only 20 routes propagation from our On-Prem data center but with the help of TGW Connect, we can get more route propagation. We just follow the same steps to create GRE Tunnel as with did above and in the GRE we run BGP to share routes with more route propagation. They add a little more complexity but we get more routes.
![[AWS TGW Connect On-Prem.png]]

## AWS Cloud WAN
AWS Cloud WAN is the service that helps you to create Cloud WAN without all the complexity you get when configuring TGW or VPC isolation etc. AWS Cloud WAN does some of the configurations you did before for you automatically and helps you to use AWS as a global WAN for all of your remote sites and VPCs. What Cloud WAN do is orchestrate the logical segmentation and isolation of your VPC environment and also orchestrate your network peering between different regions. What you have to do is to make a JavaScript Object Notation(JSON) policy where you defined your zones, policies, etc and Cloud WAN does that for you. The downside of that is you again have less visibility. One last thing I have to mention AWS Cloud WAN is not free.
![[AWS Cloud WAN.png]]

## Other AWS Network Services
- **AWS Elastic Load Balancing:** Provide Load Balancing Service that manages traffic flow to get high availability.
	- Application Load Balancer
	- Network Load Balancer
- **AWS Interface Endpoint:** Provide Private link for other AWS service i.e S3 Bucket in the cloud.
	- **S3 Bucket:** This allows users to access storage using a Public IP Address.
- AWS Private Link
- **Amazon Route 53:** It’s the DNS System that allows users to build applications and different services using a domain name resolution.
- **AWS Global Accelerator:** Allows users to connect their remote branches to the closest point in the AWS System.
- **Amazon CloudFront:** This caches frequently used data. This is the CDN service.
---
