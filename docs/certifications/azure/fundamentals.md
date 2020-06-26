# Azure Fundamentals

## Cloud Concepts

### Language of Cloud Computing

* **High availability** Maintaining Acceptable continuous performance despite temporary load fluctuations or failures in services, hardware, or data centers

    * Data Center Redundancies
        * Power
        * Cooling
        * Networking
        * ETC
    * Availability Zone Redundancies
        * 1 or more data centers
    * Region Redundancies
        * Multiple Availability Zones

* **Fault Tolerance** A system's ability to continue operation properly when one or more of its component fails
    * Proactive
        * Regularly back up data/apps/resources
        * Deploy to multiple azs or regions
        * Load Balance across multiple availability zones or regions
        * Monitor health or data/apps/resources
    * Reactive
        * Restore data/apps/resources to different availability zones or regions
        * Deploy to different availability zones or regions
        
* **Disaster Recovery** A system's ability to back up and restore data/apps/resources when needed
    * Can use Azure to Restore
        * On-premises to on-premises
        * On-premises to Azure
        * Other cloud to Azure
        * Azure to Azure

* **Scalability** The ability to increase the instance count or size of existing resources
    * Scaling Out
        * Increase instance count of existing resources
        * Non-disruptive
    * Scaling Up
        * Increase instance size of existing resources
        * Disruptive

* **Elasticity** is the ability to increase or decrease the instance count or size of existing resources based on fluctuations in traffic, load, or resource workload
    * Ability to scale in both directions ( in+out, up + down )
    * Can be manual or automatic
    * Based on changes in load or workload
    * Pay only for what you use

* **Agility** means do the ability to rapidly develop, test and launch software applications
    * Focus on Time to Value
    * Innovation
    * Low Latency
    * Economic Effectiveness
    * Rapid Adaptation
    * Flexibility

### Language of Cloud Economics

* Economies of scale
    * Supply-Side Savings
        * Lower costs of land, servers, power, labor, etc
    * Demand-Side Savings
        * Serving more customers means using more server resources ( 80% CPU)
        * Higher server utilization rates mean lower costs
    * Multi-Tenancy Savings
        * More tenants (customers or users) lowers the cost of servers and management per tenant

* **Capital expenditure (CapEx)** is buying hardware outright, paid upfront as a one time purchase
    * Up-front investment
    * Creates benefits over long period of time
    * Usually non-recurring
    * Usally related to purchase of permanent assets

* **Operational Expenditure (OpEx)** is ongoing costs needed to run your business
    * No upfront investment
    * Creates immediate benefits
    * Recurring cost of doing business
    * Usually not related to purchase of permanent assets

* **Consumption-based** pricing let's you pay only for what you use
    * Compute - Pay by CPU/hour
    * Storage - Pay by GB
    * Data Transfer - Pay by GB for data out, not in

### Cloud Service Models

* **IaaS** provides servers, storage and networking as a service
    * You Manage
        * O/S
        * Middleware
        * Runtime
        * Data
        * Applications
    * Microsoft Manages
        * Virtualization
        * Servers
        * Storage
        * Networking

* **PaaS** is a superset of IaaS and also includes middleware, such as database management tools
    * You Manage
        * Applications
        * Data

* **SaaS** is when a service is built on top of PaaS, like Office 365
    * You Manage
        * Nothing

* **Serverless** means that you don't have any servers. Let's a single function be hosted, deployed, run and managed on its own

### Cloud Architect Models

* **Private Cloud** is azure on your own hardware in a location of your choice. All the benefits of public cloud, but you can lock it down. A Lot of staff required

* **Public Cloud** Is Azure, AWS, GCP. No upfront costs, but monthly usage. Little control over services and infrastructure

* **Hybrid Cloud** model is the best of private and public, but could be complex

## Core Azure Products & Services

### Azure Architecture

* Regions
    * Groups of multiple Azure data centers withing a latency-defined perimeter and connected through a dedicated regional low-latency network
    * Deploying your applications/systems to multiple regions allows for resiliency to region-wide outages
    * Multi-Region Failover (Active/Passive)
        * Failover across regions instantly
        * Resiliency to region-wide outages
        * Best when all users are in the same region
    * Multi-Region Deployments (Active/Active)
        * Distribute traffic/load across regions
        * Resiliency to region-wide outages
        * Best when all users are in different regions
* Availability Zones
    * Unique physical locations within An azure region made up of one or more data centers
    * Allow for high availability by protecting your applications and data from data center failures
    * Some Azure services can be deployed to two or more AZs within an Azure region
* Resource Groups - Containers of related Azure services grouped together
    * All the resources in the group should share the same lifecycle - They should be deployed, updated, and deleted together
    * A resource can only exist in one resource group
    * A resource group can contain resources that are located in different regions
    * A resource group can be used to scope access control for administrative
    * A resource can interact with resources in other resource groups (When two resources are related but don't share the same lifecycle; for example web apps connection to a database)
* Azure Resource Manager
    * The Azure deployment and management service
    * Provides a consistent management layer that enables you to create, update, and delete resources in your Azure subscription, including wihth templates
    * You can use its access control, auditing, and tagging features to secure and organize your resources after deployment

### Azure Products and Services

#### Compute

* Virtual Machines
    * Pricing - Calculated Hourly
    * A Virtual Machine is your machine exclusively
    * You don't buy, own or control any hardware. Azure does this
    * VMs are an IaaS offering, where you are responsible for the entire machine
    * Azure virtual machines take advantage of azure tools
    * Pricing goes up as resources go up, and you pay by the hour
* Scale Sets
    * Auto-scaling pool of virtual machines
    * Centrally manage, configure and update a large number of vms
    * Scale sets are identical VMs. They can be activated or deactivated as needed
    * A baseline VM for the scale set ensures application stability. A baseline VM is what you copy to make up the scale set VMS
    * As resources usage increases, more VMs are activated to take the load
    * You only pay for the VM, storage and networking resources you use. Nothing additional for scale sets
* App Services
    * On-Demand, auto-scaling web/mobile/api app hosting
    * Linux & Windows platforms
    * App services are a PaaS offering on Azure
    * Web Apps are used to ost web sites and web applications
    * Web Apps for containers can host your existing container images
    * API Apps can host your data backend services
* Serverless functions
    * Event-driven serverless compute resources
    * Auto-scale on demand
    * Pay only for what you consume
* Kubernetes
    * Kubernetes = AKS
    * EMR = ACR

#### Networking

* VNet
    * An address space is a range of IP addresses you can use for your resources
    * A subnet is smaller network, which is part of your VNET. Use these for security and logical division of resources
    * A VNet is in a single region and single subscription
    * VNets in the cloud can scale, have high availability and isolation
* VPN Gateway
    * A VPN Gateway is a specific VNet Gateway, It consists of two or more dedicated VMNets
    * VNET Gateway + "vpn" becomes a VPN Connection
    * Send encrypted data between Azure and on premises network
    * Azure Gateway Subnet, secure tunnel and on-premises gateway makes up a VPN Gateway Scenario
* Azure Load Balancer
    * AWS ELB Similiar
* Azure APP Gateway
    * AWS ALB Similiar
    * It works on the HTTP request of the traffic, instead of the IP address and port
    * Traffic from a specific web address can go to a specific machine
    * Is a fit for most other Azure Services
    * Supports auto-scaling, end-to-end encryption, zone redundancy ans multi-site hosting
* Azure content Delivery Network
    * Efficently dlivers web content from locations that are close to end users to minimize latency
    

#### Storage

* BLOB
    * Highly scalable, REST based object store
    * Unstructure data
    * Types
        * Block
            * Store text and binary data up to 4.7TB. Made up of individually managed blocks of data
        * Append
            * Block blobs that are optimized for append operations 
            * Works well for logging where data is constantly appended
        * Page
            * Store files up to 8TB 
            * Any part of the file could be accessed at any time, for example a virtual hard drive
    * Pricing
        * Hot
            * Frequently accessed files. Lower access times and higher access costs
        * Cool
            * Lower storage costs and higher access times. Data remains here for at least 30 days
        * Archive
            * Lowest costs and highest access times
* Tables
    * Massive auto-scaling NoSQl Store
    * Dynamic scaling based on load
    * Scale to PBs of table Data
    * Fast Key/value lookups
* Queues
    * Reliable queues at scale for cloud services
    * Decouple and scale components
            
* Disk - Types
    * HDD
        * Spinning hard drive. Low cost and suitable for backups
    * Standard SSD
        * Standard for production. Higher reliability, scalability and lower latency over HDD
    * Premium SSD
        * Super fast and high performance. Very low latency. Use for critical workloads
    * Ultra Disk
        * For the most demanding, data-intensive workloads. Disks up to 64 TB
* Files
    * AWS EFS
    * Fully managed File Shares in the cloud
    * SMB and Rest Access
    * Lift and shift legacy apps

#### Databases

* Cosmos DB
    * Globally distributed database. It is super fast and easy to manage
    * Scale to infinite performance and size
    * Can be Costly
    * Supports multiple APIs, including SQl, MongoDB, Cassandra, Tables & Gremlin
    * Supports analytics through Apache Spark
* Azure SQL
    * IaaS 
        * SQL Server on Azure Virtual Machines
        * Pay-as you-fo for SQL server license or use an existing license
    * PaaS
        * Fully managed SQL database engine
        * Available as single database, elastic pool, and managed instances
* Azure Database for MySQL
* Azure Database for PostGreSQL
    * Horizontal scaling
* Azure SQL Data Warehouse
    * Cloud based Enterprise Data Warehouse (EDW)
    * Leverages massively parallel processing (MPP) to quicly run complex queries across petabytes of data
    * Key component of a Big Data solution
* Database Migration Services
    * Enables seamless migrations from multiple database sources to azure data platforms with minimal downtime
    * Offline migration: Application downtime starts when the migration starts
    * Online migration: Downtime is limited to the time to cut over the end of the migration

#### Azure Marketplace

* An online applications and services marketplace
* Offers technical solutions and services from Microsoft and parteners that are designed to extend azure products and services

### Azure Solutions
   
#### IoT

An IoT solution is made up of one or more IoT devices and one or more back-end services running in the cloud that communicate with each other
Devices usually have sensors and connect to the internet

* IoT Hub
    * PaaS Solution that provides more control over the IoT data collection and processing
    * Enables reliable and secure bidirectional communications between milions of IoT devices and cloud solution
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
        

#### Big Data

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
* Azure SQL Data Warehouse
    * Cloud-based enterprise warehouse (EDW)
    * Leverages massively parallel processing (MPP) to quickly run complext queries across petabytes of data 

#### Artificial Intelligence

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
    * Use this if you want to experiment with machine learning models quickly and easily, and the built-in machine learning algorithms are sufficient for your solutions
    * Uses prebuilt and preconfigured machine learning algorithms and data handling modules
    * Collaborative, drag-and-drop visual workspace where you can build, test, and deploy machine learning solutions without the need to write code
* Machine Learning Service
    * End-to-End Service
        * The service to use AI and machine learning almost anywhere on Azure
    * Tooling
        * The Machine Learning service is a collection of tools to help you build AI applications
    * Automation
        * Azure automatically recognizes trends in your applications and creates models for you
    * Use this if you work in a python environment, you want more control over your machine learning algorithms, or you want use open-source machine learning libraries
    * Fully supports open-source technologies: tens of thousands of open-source machine learning components are available
    * Provides a cloud-based environment tou can use to prep data and train, test, deploy, manage, and track machine learning models

#### Serverless

* Azure Functions
    * Configure your code to load and run only when triggered
    * Triggers can be HTTP requests, data from other Azure Services, etc
    * Can Return data or call other azure services
* Logic Apps
    * Are a quick and simple way to integrate systems and applications inside and outside of azure
    * Visual designer for automating and orchestration tasks processes and workflows
    * Great for integrating apps/data/systems and for business-to-business(B2B) integrations
    * Connect Systems
        * Connect systems bot inside and outside of the Azure platform
        * Integrate not only apps, but also data flows, services and entire systems
    * Automation
        * A lot of ways to schedule, automate and orchestrate tasks and processes
    * Quick Start
        * No coding required to get started straight away. You just need access to your apps to integrate
* Event Grid
    * Fully managed event routing service
    * Greatly simplifies the development of event-based applications and serverless workflows
    * Handles all event routing from any source, to any destination for any application

### Azure Management Tools

#### Azure cli, PowerShell, Azure Portal

* Azure Cli
    * Command line experience for managing Azure resources
    * You can use it in your browser with Azure cloud shell (interactive browser-accessible shell that runs basb or powershell)
    * You can install it on macOs, Linux, and Windows and run it from the command line
* Power Shell
    * Set of PowerShell cmdlets for managing Azure resources
    * You can use it in your cloud shell
    * Uses .NET standard making it available for windows, macOS, and Linux
* Azure Portal
    * Build, manage and monitor every Azure resource in a single unified console
    * Browse active resources modify settings, provision new resources, and view basic monitoring data from all your azure products and services

#### Azure Advisor

* Personalized cloud consultant that helps you follow best practices to optimize your azure deployments
* Analyzes your resource configuration and usage telemetry
* Recommends solutions to help improve the cost effectiveness, performance, security, and availability your resources

## Security, Privacy, Compliance, and Trust

### Network Security

#### Azure Firewall

* Managed, cloud-based network security service that protects your azure virtual Network resources

* Centrally create, enforce, and log application and network connectivity policies across subscriptions and virtual networks

* Fully integrated with azure monitor for logging and analytics

#### Network Security Groups (NSGs)

* Enforce and control network traffic rules at the networking level

* Security rules allow or deny inbound network traffic to, or outbound network traffic from, several types of Azure resources

* For each rule, you can specify a source, destination, port, and protocol


#### Azure DDoS Protection

#### Choosing an Azure Security Solution

* Isolation
    * Use different virtual networks, or virtual network subnets, to segment your network or isolate resources when required

* Principle of least privilege
    * Limit each user's access rights to the bare minimum permissions they need to perform their work

* Use NSGs for Bare Minimum Network Connectivity
    * Use network security groups (NSGs) to enforce rules allowing only the minimum network access needed

* Use Azure Firewall and Azure DDoS Protection
    * Use Azure Firewall and Azure DDoS protection to protect your resources and prevent large unanticipated costs

### Azure Identity Services

* Authentication and Authorization
* Azure Multi-Factor Authentication (MFA)
    * Something you know ( Typicall password)
    * Something you have ( a trusted device that is not easily duplicated, like a phone)
    * Something you are ( biometrics )
* Azure Active Directory (AAD)
    * Comprehensive, highly available identity and access management (IAM) cloud solution
    * Fully managed identity service - requires no hardware, virtual machines, etc
    * Main objects are users (pay per user) and groups(free)
    * Users are assigned a license based on which edition of Azure AD they should have
    * Groups can be assigned multiple licenses, which are inherited by all users in the group
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

### Azure Security tools and features

* Security is built into every layer of azure
* Secure Foundation
    * State-of-the-art security in Azure data centers around the world, integrated security controls in Azure hardware and firmware components, and protection against threats such as DDoS
* Simplify security with Built-In Services
    * Easily manage identify and control access, secure your network, safeguard data, manage secrets and certificates, prevent attacks, and gain centralized visibility
* Detect threats early with unique intelligence
    * identify new threats and respond quickly with services that are informed by real-time global cybersecurity intelligence

#### Azure Security Center

* Protects Azure and non-Azure servers and virtual machines - including windows and linux servers - when the microsoft monitoring agent is installed
* Events collected from azure and the agents are sent to the security analytics engine, which provides threat detection alerts and tailored recommendations for securing workloads
* Azure PaaS services - including SQL databases and storage accounts - are automatically monitored and protected by Azure Security Center

#### Azure Key Vault

* Encrypt and safeguard authentication keys, storage account keys, data encryption keys, certificates, and passwords
* Data is protected by hardware security modules (HSMs)
* Control access to keys using Azure Active Directory
* Grant access to individual users or to applications and systems

#### Azure information Protection (AIP)

* Cloud-based solution that helps organizations classify and protect documents and emails
* Manually or automatically classify sensitive data using 80+ built-in data types (credit card numbers, ID/SSN numbers)
* Enforce policies on classified daa - require credit card numbers to be encrypted
* Can be retroactively applied to existing documents and emails


#### Azure Advanced Threat Protection (ATP)

* Cloud-based security solution that identifies, detects, and helps you investigate advanced threats, compromised identifies, and malicious insider actions
* Built-in advanced threat detection using data from Azure Active Directory (Azure AD), Azure Monitor logs, and Azure security Center

### Azure Governance

#### Azure Policies

* Service in Azure that you use to create, assign, and manage policies
* Policies enforce different rules for your resources
* Policies can be assigned to Management Groups, subscriptions, or Resource Groups and are inherited downward
* Evaluate your resources for non-compliance with assigned policies
* Policies can be audited or applied to resources

#### Azure Initiatives

* A collection of policy definitions that are tailored toward achieving a single overarching goal
* Simplify managing and assigning policy definitions
* Initiatives can be assigned to Management Groups, subscriptions, or Resource Groups and are inherited downward

#### Role-Based Access Control (RBAC)

* An authorization system built on Azure Resource Manager that provides fine-grained access management for azure resources
* Roles are defined and assigned to a security principal: User, group or service principal (i.e., application account)
* Role can be scoped to management groups, subscriptions, resources groups, or resources and are inherited downward

#### Resource Locking

* Prevent other users in your organization from accidentally deleting or modifying critical resources
* Locks can be set to prevent deletion only, or to prevent modifications and deletion
* Locks can be assigned to Management Groups, subscriptions, resource groups, or resources and are inherited downward


Hierarchy : Management groups -> subscriptions -> resource groups -> resources

#### Security Assistance n Azure Advisor

* Azure Advisor integrates with Azure security Center to bring you security recommendations in addition to the high availability, performance, and other recommendations already included in Azure advisor
* Periodically analyzes the security state of your azure resources and makes recommendations for any potential security vulnerabilities

### Azure Monitor










### Subscription

* Billing Entity
    * All Resources within a subscription are billed together
* Cost Separation
    * You can have multiple subscriptions within a tenant to separate costs
* Payment
    * If a subscription ins't paid, all the resources and services associated with the subscription stop
    
    


 
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
