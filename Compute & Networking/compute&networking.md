# Compute and Networking

## Amazon EC2

### Instances Types

Amazon Ec2 Instances family types are following

1. **General Purpose** - A balanced mix of CPU, RAM, and other resources
2. **Compute Optimized**- A high amount of CPU, such as high-performance web services
3. **Memory Optimized**- A large amount of RAM, such as in the memory database
4. **Storage Optimized**- A large amount of storage and Input/Output throughput.
5. **Accelerated Computing** - use for 3D rendering, and real-time video processing.

## EC2 Instance Launch Types

- **On-Demand:** short workload, predictable pricing
- **Reserved Instances:** long workloads (≥ 1 year)
- **Convertible Reserved Instances**: long workloads with flexible instances
- **Scheduled Reserved Instances:** launch within time window you reserve
- **Spot Instances: short workloads,** for cheap, can lose instances
- **Dedicated Instances**: no other customers will share your hardware
- **Dedicated Hosts:** book an entire physical server, control instance placement

### Storage

Your instance requires storage requires for both the root volume and any additional storage volume that you want to configure. You can create persistent storage volume with the Amazon Elastic Block Store (EBS) service to provide block storage also instance store which is temporary for instance.

### Network Interface

A virtual network interface called elastic network interface provides networking for your Amazon Ec2 Instances. Elastic network interfaces are associated with a software-defined network provided by Amazon VPC. Each Amazon Ec2 instance is assigned a primary network interface that is associate with a subnet within a AWS VPC , by default if you omit the network configurations Amazon EC2 assign the instance to one of the subnet within default VPC

### Discovering Instance MetaData

With the instance metadata service , code running on an Amazon Ec2 instance can discover properties about the instance. you can curl to get the metadata attributes

```bash
curl http://169.254.169.254/latest/meta-data/
```

## AWS Virtual Provide Cloud

Amazon Virtual Private Cloud provides logically isolated networking within your AWS account. These networks are software-defined and can span all of the availability zones within a specific region. For each VPC, you have full control over whether the Amazon VPC is connected to the internet, to a premises network, or another Amazon VPC.

Connections types are following

1. **Internet Gateway** - A highly available connection that allows outbound and inbound request to the internet from your VPC
2. **Egress Only Internet Gateway** - A special type that allows outbound traffic
3. **Gateway** - Allow you to establish a private connection to your other network.
4. **Amazon VPC Endpoint**- Allow traffic from your Amazon VPC to go to a specific AWS Service.
5. **Amazon VPC peering** - Privately route traffic from one VCPC to another VPC
6. **AWS Transit Gateway** - This allows you to centrally manage the connectivity between many VPCs and an on-premises environment using a single gateway

### IP Address

There are four types of IP Addresses.

### Public IP

Unlike the private IP address, the public IP address is IPv4 which can be reachable from the internet. You cannot manually associate or disassociate public IP addresses from an instance.

### Private IP

Private IP addresses are IPv4 addresses there not reachable from the internet. These addresses are unique within a VPC and use for traffic that is to be routed internally within the VPC for private communication with other networks.

### Elastic IP

An Elastic IP Address is similar to a public IP Address that can be reachable from the internet. You can disassociate or allocate between the instances.

You can also assign the elastic IP with infrastructure i.e NAT Gateway

### IPV6

When you enable IPv6 in your VPC the network operates in dual-stack mode meaning the IPv4 and IPv6 communicate independently of each other.

### Subnets

Within VPC, subnets are defined. A subnet is associated with a specific Availability Zone within the region containing the Amazon VPC. Each subnet has its block of private IP addresses defined using CIDR notation. for example, a subnet may have assigned the CIDR block range 10.0.0.0/24 which would include addresses in the range 10.0.0.0=10.0.0.255. Out of 256 possible address VPC reserve the first four IP address and the last IP address in the range. leaving 251 IP addresses in the subnet.

**Public Subnet**- Directly reach from the internet

**Private Subnet** - Cannot be directly accessible from the internet

### Route Tables

Networking traffic exiting a subnet is controlled with routes that are defined in the routing table. Route defines how the implicit router in the Amazon VPC routes IP traffic from a subnet to destinations outside that subnet. Each routing table includes a rule called a local route. This rule or route is what allows traffic from instances in one subnet within VPC to send traffic to instances in any other subnet within the same VPC.

### Security Groups

Security Groups act as a stateful firewall for your Ec2 instances. When you define a security group rules, you specify the source or destination of network traffic in addition to the protocol and protocol you allowed.

- should maintain one separate security group for SSH access
- time out error ⇒ security group issue
- connection refused error ⇒ it's an application error or it's not launched
- inbound traffic is blocked by default
- outbound traffic is allowed by default
- can allow inbound traffic from:
  - anywhere

  - IP
  - other security groups
![Untitled](https://user-images.githubusercontent.com/53600644/193905378-b644924e-5eee-44de-9c17-386f0686d599.png)


## SSH troubleshooting

### There's a connection timeout

This is a security group issue. Any timeout (not just for SSH) is related to security groups or a firewall. Ensure your security group looks like this and is correctly assigned to your EC2 instance.

![Untitled 1](https://user-images.githubusercontent.com/53600644/193905468-3f8d6aa6-2d6c-450d-9a55-b2480f27752c.png)


### NACL

NaCl allows an administrator to control traffic that enters and leaves a subnet. it consists of inbound and outbound rule that is associated with multiple subnets within a specific VPC. it can have a stateless firewall for traffic to or from a specific subnet.

### Network Address translation

NAT allows for instance a private subnet to make an outbound request to the internet without exposing those instances to inbound connections from internet users.

### DHCP Option Set

DHCP provides a standard for passing configuration information to a host on a TCP/IP network. The options field of a DHCP message contains the configuration parameter.

### Shared Responsibility Model

![Untitled 2](https://user-images.githubusercontent.com/53600644/193905488-822cc8ca-b480-47c9-9d07-ae7b5b37f206.png)


### Things you Should Know!

- You can use 10 tags per EC2 Instance.
- AMI is regional
- When you create an AMI, it’s marked private
- stateful meaning that any request allowed in is automatically allowed out.
- Allowed up to 5 VPC per region
- All subnets in default VPChase an internet gateway attached.
- SA subnetcannot mapped to multiple AZ
- You can have 1 IG per VPC
- NAT Gateway scale up to 10GBPS
- NAT Gateway automatically assigned to public IP
- Highes rule number in NACL is 32766
- NACL rules are stateless means it does not create outbound traffic automatically.
- Subnet per VPC - 200
- Internet per gateway - 5
- Customer Gateway per region 50
- VPN connection per region - 50
- Route table per VPC - 5
- Security group per VPC - 500
- Network interface per instance - 350
- Rules per NACL - 20
- NAT Gateway per AZ - 5
