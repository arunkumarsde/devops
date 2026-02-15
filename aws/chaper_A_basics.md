# Introduction to AWS

Amazon Web Services (AWS) is the world’s most comprehensive and broadly adopted cloud platform. Started in 2006, it allows individuals, companies, and governments to access technology services such as computing power, storage, and databases on an on-demand basis.

Instead of buying, owning, and maintaining physical data centers and servers, you can access technology services from AWS and pay only for what you use. This is akin to flipping a light switch: you pay for the electricity (compute power) when you need it, and turn it off when you don't.

**Before And After AWS or Cloud**

![Before AWS](/images/aws/Before-AWS_.webp)

![After AWS](/images/aws/After-AWS_.webp)

## The AWS Global Infrastructure

The backbone of AWS is its massive global infrastructure. Understanding this is key to architecting resilient applications.

1. **Regions** -
   A Region is a physical location in the world where AWS clusters data centers. (e.g., us-east-1 in N. Virginia or ap-south-1 in Mumbai).

   Why it matters: You choose a region to optimize latency (closeness to users), minimize costs (prices vary by region), and meet data sovereignty/compliance laws.

2. **Availability Zones (AZs)**
   Each Region consists of multiple, isolated locations known as Availability Zones.

   An AZ is one or more discrete data centers with redundant power, networking, and connectivity.
   Why it matters: If you run your app in just one data center and it floods, your app dies. If you run it across multiple AZs, your app stays online even if one building fails.

3. Edge Locations
   These are smaller data centers located in major cities worldwide, used primarily for Amazon CloudFront (CDN) to cache content closer to end-users to reduce latency.

**The Shared Responsibility Model:**
Security is the top priority at AWS. To maintain it, AWS operates under a Shared Responsibility Model.

- Security OF the Cloud (AWS Responsibility): AWS protects the infrastructure that runs all the services offered in the AWS Cloud. This includes the hardware, software, networking, and facilities.
- Security IN the Cloud (Customer Responsibility): You are responsible for your data. This includes encryption, network configuration (firewalls), identity management (IAM), and OS patching.

## Core AWS Services

AWS offers over 200 services. Here are the fundamental ones you must know to get started.

1. **Compute Services**
   - **Amazon EC2 (Elastic Compute Cloud):** Resizable virtual servers. You choose the OS (Linux/Windows) and the hardware power (CPU/RAM). It is the workhorse of AWS.
   - **AWS Lambda:** Serverless compute. You upload your code, and Lambda runs it only when triggered (by an event like a file upload). You don't manage any servers.
   - **AWS Elastic Beanstalk:** Platform as a Service (PaaS) for deploying web apps. You upload code, and AWS handles the deployment (capacity provisioning, load balancing, auto-scaling).

2. **Storage Services**
   - **Amazon S3 (Simple Storage Service):** Object storage for files (images, videos, backups). It allows you to store and retrieve any amount of data from anywhere on the web.
   - **Amazon EBS (Elastic Block Store):** Block storage (virtual hard drives) that you attach to EC2 instances. It is persistent storage for your virtual servers.
   - **Amazon Glacier:** Extremely low-cost storage for data archiving and long-term backup.

3. **Database Services**
   - **Amazon RDS (Relational Database Service):** Managed SQL databases. It supports engines like MySQL, PostgreSQL, Oracle, SQL Server, and Amazon Aurora (AWS's high-performance proprietary engine).
   - **Amazon DynamoDB:** A managed NoSQL database service that provides fast and predictable performance with seamless scalability.

4. **Networking Services**
   - **Amazon VPC (Virtual Private Cloud):** Lets you provision a logically isolated section of the AWS Cloud where you can launch resources in a virtual network you define.
   - **Amazon CloudFront:** A Content Delivery Network (CDN) that speeds up distribution of your static and dynamic web content to users.
   - **Amazon Route 53:** A highly available and scalable cloud Domain Name System (DNS) web service.

From startups to large enterprises like _Netflix_, _Airbnb_, and _NASA_, _AWS_ is widely adopted for its flexibility, scalability, and security.

## Real-World Use Cases of AWS

AWS services are used by both startups and large enterprises based on their specific needs. Startups use AWS to overcome hardware infrastructure costs and deploy applications efficiently. Whereas large scale companies are using AWS cloud services for the management of their Infrastructure to completely focus on the development of products widely.

![Real World Use Cases](/images/aws/Real-World-Use-Cases-of-AWS__.webp)

- **Netflix:** The large streaming giant using AWS for the storage and scaling of the applications for ensuring seamless content delivery with low latency without interruptions to millions of users globally.
- **Airbnb:** By utilizing AWS, Airbnb manages the various workloads and provides scalable and reliable infrastructure for its virtual marketplace and lodging offerings.
- **NASA's Jet Propulsion Laboratory:** It takes the help of AWS services to handle and analyze large-scale volumes of data related to vital scientific research missions and space exploration.
- **Capital One:** A financial Company that is utilizing AWS for its security and compliance while delivering innovative banking services to its customers.

## AWS Pricing Overview

AWS (Amazon Web Services) follows a pay-as-you-go pricing model, offering flexibility and scalability for businesses of all sizes. Pricing varies depending on the services you use, and AWS provides multiple options to help optimize costs. Here’s an overview of key AWS pricing features and models:

![AWS Pricing](/images/aws/AWS-Pricing_.webp)

1. **Pay-as-You-Go Pricing** - AWS charges for usage-based billing, meaning you only pay for what you use. This pricing model is based on factors like:

- Compute (e.g., EC2 instances)
- Storage (e.g., S3)
- Data transfer
- Requests and service usage (e.g., Lambda invocations)
  This model is ideal for businesses with variable workloads.

2. **On-Demand Instances** - On-Demand Instances let you pay for compute capacity by the hour or second (depending on the instance type) with no long-term commitments or upfront payments. This option is ideal for:

- Applications with short-term, irregular, or unpredictable workloads
- First-time AWS users testing the platform
- Projects that cannot be interrupted
  These instances offer maximum convenience and are perfect for development, testing, and prototyping workloads.

3. **Reserved Pricing** - For predictable usage, you can commit to a long-term contract (1 or 3 years) with reserved instances for services like EC2, RDS, and Redshift. This offers:

- Up to 75% cost savings compared to on-demand pricing.
- Flexible payment options (All upfront, Partial upfront, or No upfront).

4. **Spot Instances** - Spot Instances allow you to bid on unused EC2 capacity. Prices fluctuate based on supply and demand, and you can save up to 90% compared to on-demand prices.

- Great for batch processing, data analysis, or flexible workloads.

5. **Free Tier** - AWS offers a Free Tier for new users, providing access to a limited set of services for free, such as:

- 750 hours/month of EC2 (t2.micro instance) for the first 12 months.
- 5GB of standard S3 storage.
- 1 million Lambda requests/month.

  This is an excellent way for businesses to explore AWS without incurring costs.

6. **AWS Pricing Calculator** - AWS provides a Pricing Calculator to estimate costs based on your specific usage. It helps you project the total cost of your cloud infrastructure by selecting services and configurations relevant to your business.

7. **Cost Management and Optimization** - AWS offers tools like AWS Cost Explorer and AWS Budgets to:

- Track usage and manage expenses.
- Set custom budgets and receive alerts when approaching limits.
