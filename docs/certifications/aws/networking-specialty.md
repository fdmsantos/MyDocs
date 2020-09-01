# AWS Certified Networking Specialty

# Chapter 1 - General Networking Fundamentals

## OSI Model

* Layer 1 - Physical
    * Responsible for physical connectivity to the network, and transmission of data
    * Ethernet Cables, Hubs + Repeaters

* Layer 2 - Data Link
    * Moves information between computers and networks. It0s responsible for transmission, format, and error correction
    * Switches, Mac Address, Bridges

* Layer 3 - Network
    * Responsible for routing. It splits the data into packets and adds destination and routing information
    * Network Routers, IP, ICMP

* Layer 4 - Transport
    * Ensures data is sent to the right destination, across networks, and manages error handling
    * TCP + UDP

* Layer 5 - Sessions
    * Defines and manages the rules for communication between two computers
    * Appletalk, Winsock

* Layer 6 - Presentation
    * Defines the format that will be used for data transmission. The data may also be compressed or encrypted at this layer
    * JPG, GIF, MPEG

* Layer 7 - Application
    * Where the user interacts with software like web browsers, social media apps, email
    * Web browser, Email
    
    
### OSI Model Inside AWS

* Layer 2
    * We have switches and Mac Address
    * In AWS we have subnets, instances communicate in layer 2 inside subnets
* Layer 3
    * For instances communicate inside subnet, uses router (layer 3)
    * To communicate with internet uses internet gateway (layer 3)
* Layer 7
    * ELB - ALB
    
## Chapter 2 - General Networking Fundamentals

### IpV4

* Operates at Layer 3
* Ip address are 32 bit numbers separated into 4 bytes

