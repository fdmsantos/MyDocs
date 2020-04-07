# Azure Fundamentals

## Cloud Concepts

### Language of Cloud Computing

* **High availability** means VMs can spin up fast to help process requests

* **Fault Tolerance** describes how azure will ensure you have zero downtime for services provided be Azure

* **Disaster Recovery** uses time-to-recovery and recovery point metrics to recover from tornados, floods and more

* **Scalability** refers to scaling out or scaling up

* **Elasticity** is the ability to quickly increase or decrease computer processing and resources

* **Agility** means do the ability to rapidly develop, test and launch software applications

### Languague of Cloud Economics

* **Capital expenditure (CapEx)** is buying hardware outright, paid upfront as a one time purchase

* **Operational Expenditure (OpEx)** is ongoing costs needed to run your business

* **Consumption-based** pricing let's you pay only for what you use

### Cloud Service Models

* **IaaS** provides servers, storage and networking as a service

* **PaaS** is a superset of IaaS and also includes middleware, such as database management tools

* **SaaS** is when a service is built on top of PaaS, like Office 365

* **Serverless** means that you don't have any servers. Let's a single function be hosted, deployed, run and managed on its own

### Cloud Architect Models

* **Private Cloud** is azure on your own hardware in a location of your choice. All the benefits of public cloud, but you can lock it down. A Lot of staff required

* **Public Cloud** Is Azure, AWS, GCP. No upfront costs, but monthly usage. Little control over services and infrastructure

* **Hybrid Cloud** model is the best of private and public, but could be complex

## Compute

### Virtual Machines

* Pricing - Calculated Hourly
* A Virtual Machine is your machine exclusively
* You don't buy, own or control any hardware. Azure does this
* VMs are an IaaS offering, where you are responsible for the entire machine
* Azure virtual machines take advantage of azure tools
* Pricing goes up as resources go up, and you pay by the hour

### Scale Sets

* Scale sets are identical VMs. They can be activated or deactivated as needed
* A baseline VM for the scale set ensures application stability. A baseline VM is what yoy copy to make up the scale set VMS
* As resources usage increases, more VMs are activated to take the load
* You only pay for the VM, storage and networking resources you use. Nothing additional for scale sets

### App Services

* App services are a PaaS offering on Azure
* Web Apps are used to ost web sites and web applications
* Web Apps for containers can host your existing container images
* API Apps can host your data backend services

### Kubernetes

* Kubernetes = AKS
* EMR = ACR

### Functions Benefits

* Only Runs When needed
* Saves Money
* Resilience

## Networking

### VNet

* An address space is a range of IP addresses you can use for your resources
* A subnet is smaller network, which is part of your VNET. Use these for security and logical division of resources
* A VNet is in a single region and single subscription
* VNets in the cloud can scale, have high availability and isolation

### VPN Gateway

* A VPN Gateway is a specific VNet Gateway, It consists of two or more dedicated VMS
* VNET Gateway + "vpn" becomes a VPN Connection
* Send encrypted data between Azure and on premises network
* Azure Gateway Subnet, secure tunnel and on-premises gateway makes up a VPN Gateway Scenario

### Application Gateway

* It works on the HTTP request of the traffic, instead of the IP address and port
* Traffic from a specific web address can go to a specific machine
* Is a fit for most other Azure Services
* Supports auto-scaling, end-to-end encryption, zone redundancy ans multi-site hosting

## Storage

### BLOB

* Types
    * Block
        * Store text and binary data up to 4.7TB. Made up of individually managed blocks of data
    * Append
        * Block blobs that are optimized for append operations. Works well for logging where data is constantly appended.
    * Page
        * Store files up to 8TB. Any part of the file could be accessed at any time, for example a virtual hard drive
  
* Pricing
    * Hot
        * Frequently accessed files. Lower access times and higher access costs
    * Cool
        * Lower storage costs and higher access times. Data remains here for at least 30 days
    * Archive
        * Lowest costs and highest access times
        
### Disk

* Types
    * HDD
        * Spinning hard drive. Low cost and suitable for backups
    * Standard SSD
        * Standard for production. Higher reliability, scalability and lower latency over HDD
    * Premium SSD
        * Super fast and high performance. Very low latency. Use for critical workloads
    * Ultra Disk
        * For the most demanding, data-intensive workloads. Disks up to 64 TB

## Databases

* Cosmos DB
    * Globally distributed database. It is super fast and easy to manage
    * Scale to infinite performance and size
    * Can be Costly
* Azure SQL
    * Fully managed and using stable Microsoft SQL Technology
* Azure Database for MySQL
* Azure Database for PostGreSQL
    * Horizontal scaling
* Database Migration Services

## Authentication and Authorization

### Azure Active Directory

* Active Directory (AD) is not the same as Azure Active Directory
* Different skillset from AD to Azure AD
* Every Azure account will have and azure AD service
* Organization
    * A tenant represents the organization
* Dedicated AAD
    * A tenant is a dedicated of AAD that an organization receives when signing up for Azure
* Separate 
    * Each tenant is distinct and completely separate from other AAD tenants
* One User - One Tenant
    * Each user in Azure only belong to a single tenant
    * Users can be guests of other tenants
* Azure AD can help manage users in a hybrid cloud setup
 

### Subscription

* Billing Entity
    * All Resources within a subscription are billed together
* Cost Separation
    * You can have multiple subscriptions within a tenant to separate costs
* Payment
    * If a subscription ins't paid, all the resources and services associated with the subscription stop
    

## Azure Solutions
    
### IoT

* IoT Hub
    * PaaS Solution that provides more control over the IoT data collection and processing
    * Managed and Secure
    * Ease of Deployment
    * Platform-as-a-service
    * Scaling and Authentication
* IoT Central
    * SaaS solution that provides pre-made IoT connections and dashboards to get set up quickly
    * Software-as-a-service
        * Simplify and speed up the implementation of your IoT solution
    * No Coding Needed
        * You don't have to know how to write code to deploy your IoT project
        * Receive feeds from devices and focus on metrics and business value
    * Pre-made Connectors
        * Use any of the hundreds of connectors that are ready to use in IoT Central
        
### Big Data

* Data Lake Analytics
    * Large Amounts of Data
        * A data lake is a very large body of data
    * Parallel Processing
        * Two or more processes or computers processing the same data at the same time
    * Ready to Go
        * Servers, processes and any other needed services are ready to go from the start Jump straight into the data analytics
* HD Insights
    * Similar to Azure Data Lake Analytics
    * Open Source, which is free and community supported
    * Includes Apache Hadoop, spark and kafka
* Azure Databricks
    * Based on Apache Spark, a distributed cluster-computing framework
    * Run and process a dataset on many computers simultaneously
    * Databricks provides all the computing power
    * Integrates with other Azure Storage Services
    
### Artificial Intelligence

* Machine Learning/AI
    * Models
        * The definition of what your machine learning application is learning
        * A model is a set of rules of how to use the data provided
        * The model finds patterns based on the rules
    * Knowledge Mining
        * Use Azure search to finding existing insights in your data
        * File relationships, geography connections and more
    * Built-in Apps
        * Azure has a number of built-in apps that you can use for machine learning and AI straight away.
        * These include cognitive services and bot services
* Azure Cognitive Services
    * Vision
        * You can use the vision service to recognize, identify and caption your videos and images. Automatically
    * Decision
        * Apps can make decisions based on content. Detect potential offensive language, detect IoT anomalies and leverage data analytics
    * Speech
        * Automatic speech-to-text transcription. Speaker identification and verification
* Azure Machine Learning Studio
    * Supports all Azure Machine Learning tools
    * Pre-made modules for your project
    * Use for real world scenarios such as twitter sentiment analysis, photo grouping and movie recommendations
* Machine Learning Service
    * End-to-End Service
        * The service to use AI and machine learning almost anywhere on Azure
    * Tooling
        * The Machine Learning service is a collection of tools to help you build AI applications
    * Automation
        * Axure automatically recognizes trends in your applications and creates models for you

### Serverless

* Azure Functions
* Logic Apps
    * Are a quick and simple way to integrate systems and applications inside and outside of azure
    * Connect Systems
        * Connect systems bot inside and outside of the Azure platform
        * Integrate not only apps, but also data flows, services and entire systems
    * Automation
        * A lot of ways to schedule, automate and orchestrate tasks and processes
    * Quick Start
        * No coding required to get started straight away. You just need access to your apps to integrate
* Event Grid
 
### DevOps

* Azure Boards
    * Keep track of work tasks, timelines, issues, planning and much more
* Azure pipelines
    * Produce and test your software automatically and continuously
* Azure Repos
* Azure Test Plans
    * Design tests of applications to implement automatically
* Azure Artifacts
    * Share applications and code libraries with other teams inside and outside your organization
* Azure DevTest Labs
    * Environment Management
        * Allow developers and engineers to create environment for test and development
    * Cost Management
        * You won't incur unexpected costs and will even minimize wast of resources on your account
    * Templates
        * Tailor your development and test environments and reuse them with template deployments
        
## Security

* Azure Security Center
    * Threat Alerts
    * Ready for Hybrid architectures
    * Each VM has an agent installed that sends data
    * Azure analyzes the data and alerts if necessary
    * Policy and compliance metrics
    * A "secure score" to entice great security hygiene
    * Integrate with other cloud providers
    * Alerts for resources that aren't secure
    * Define policies
    * Protect Resources
    * Response
    * Azure Advisor for security Assistance
* Azure Key Vault
* Azure Information Protection
* Advanced Threat Protection
    * Monitor users
    * Baseline Behavior
    * Suggest Changes
    
## Privacy, Compliance and Trust

### Azure Policy

* Azure Policy ensures that policies applied to resources are compliant
* A policy is a set of rules to ensure compliant resources

* RBAC
    * Security Principal
        * An object representing an entity such as user or group, which can access the resource
    * Role Definition
        * A collection of permissions such as read, write and delete
    * Scope
        * The resources the access applies to. Specify which role can access a resource or resource group
* Locks
    * Assigning
        * Assign a lock to a subscription, resource group or resource
    * Types
        * A lock can be of two types. 
        * Delete, where you can't delete the locked object. 
        * Read-only, where you can't make any changes to the object

### Azure Blueprints

* Templates for creating standard Azure environments
* Use Blueprints
    * Resource templaes
    * Role Based Access Control (RBAC)
    * Policies
    * Samples for common regulations
    
### Azure Monitor

* Constant Feed
    * Most Azure services feed telemetry data into Azure Monitor
    * Even on-premises services can send telemetry data to Azure Monitor
* Fully Managed
    * Azure Monitor is centralized and fully managed. You can analyze all the data from one place
* Query Language
    * Full access to an interactive query language to learn about the telemetry data
* Machine Learning
    * Predict and recognize problems faster with built-in machine learning
    
### Azure Service Health

* Dashboard
* Custom Alerts
* Real-Time tracking
* Free Service

### Compliance

* Industry Regulations
    * General Data Protection Regulation
    * ISO Standard
    * NIST
* Azure compliance manager
    * Azure knows about compliance and resources, and can give you recommendations through the compliance manager
    * Recommendations
    * Tasks
    * Compliance Score
    * Secure Storage
    * Reports
* Exam Tips
    * GDPR, ISO and NIST are regulations and standards to ensure compliance with applicable legislation
    * Azure compliance manager provides recommendations, tasks to assign team members, a compliance score, secure document storage and reports
    * Azure government cloud provids dedicated datacenters to US Government bodies. Compliant with US federal, state and local requirements
    * Azure china region contains all data and datacenters within china. Complies with all applicable chinese regulations

### Privacy

* Azure Information Protection
    * Classify, label and protect data based on data sensitivity
* Azure Policy
    * Define and enforce rules to ensure privacy and external regulations
* Guides
    * Use Guides on azure to respond and comply with GDPR privacy requests
* Compliance Manager
    * Make sure you are following privacy guidelines
    
### Trust

* TrustCenter
    * Learn about Microsoft's effort on security, privacy, GDPR, data location, compliance about trust in each product and service
* Service Trust Portal
    * Review all the independent reports and audits performed on Microsoft products and services
    * Azures complies with more standards than any cloud provider
    
## Pricing

### Subscriptions

* Multiple Subscriptions
    * Any Azure account can have multiple subscriptions
    * Useful for organizing who pays for what
* Billing Admin
    * One or more users can be a "billing admin", which manages anything to do with billing and invoicing on azure
    * Ensures separation of responsibility
* Billing Cycle
    * A billing Cycle on Azure is either 30 or 60 days
    
* Management Groups
    * Group subscriptions
        * Take action across subscriptions in bulk
        * Very useful in large organizations with many subscriptions
    * Organize
        * Manage access, policies and compliance in bulk
        * E.g. have a management group per country or department
    * Billing logic
        * You maintain the billing associated with the right budgets
        * Nest management groups to indicated hierarchy and relationship
 
### Cost Management

### Pricing Calculator

* Limit
    * Default Limit
        * Some Azure accounts with monthly credits to use will have default spending limits
        * When the credits are used, the limit kicks in
    * No increase
        * When the credits are gone, either remove the limit entirely or leave it in effect
    * No spending limit
        * Pay-as-you-go accounts have no spending limit functionality
* Quotas
    * Property limit
        * A quota is a limit on a certain property of an azure service
        * For example, a maximum of 100 namespaces for event hub
    * Ensure service level
        * The quotas are necessary to ensure azure can maintain high service level
    * Quota Change
        * If you need to increase the quota for a particular service, you can ask Microsoft to increase them
* Tags
    * Identity Roles
        * Protect sensitive data by defining which roles can access a resource
    * Filter
        * Filter resources per project, customer or for reporting purposes
    * Related Resources
        * To make bulk processing and updating easier, define which resources are related
    * Unambiguous
        * Create a list for tags used that includes: description, tag name, and potential values

## Support

### Plans

* Basic
    * 24/7 Access
    * Online Self-Help
    * Forums
    * Azure Advisor
    * Service Health
* Developer
    * Bus. Hours Email
* Standard
    * 24/7 Email/phone
* Professional Direct
    * Operations Support - Onboarding Reviews
    * Webinarrs
* Premier
    * Tech reviews, Reporting, Tech Account Man
    * Training On-demand
    
### Support Channels

* Azure Documentation
* Forums
* Social Media
