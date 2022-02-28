 Regional Service - All AZs in the region

 Isolated Network

 Nothing IN or OUT without explicit configuration

 Flexible configuration - simple or multi-tier

 Hybrid Networking - other cloud & on-premises

 Default or (Dedicated Tenancy!)-> comes at a cost

 It uses IPv4 Private CIDR Blocks & Public IPs.

 Allocated one Primary Private IPv4 CIDR Block

 Min /28 (16 IP) Max /16 (65536 IP)

 Optional secondary IPv4 Blocks

 Optional single assigned IPv6 /56 CIDR Block

 DNS in a VPC privided by Route 53 -> VPC `Base IP +2` Address

 enableDnsHostnames - gives instances with public IP addresses DNS names.

 enableDnsSupport - enables DNS resolution in VPC.



-----------------------------------------------------------------------------------

All subnets are private by default

Private subnet indicated by blue color whereas a public subnet is indicated by green color.

AZ Resilient

1 per AZ

A subnetwork of a VPC

1 Subnet -> 1AZ, 1AZ -> 0 + Subnets

IPv4 CIDR is a subset of the VPC CIDR

Cannot overlap with other subnets

Optionally be allocated IPv5 CIDR (/64 subset of the /56 VPC - space for 256 /64 ranges)

Subnets can by default communicate with other subnets in the VPC.

5 reserved IP address in subnet - > 

eg 10.16.16.0/20  (10.16.0.0 to 10.16.31.255)

Cannot use the Network address -> 10.16.16.0

Cannot use Network + 1 (10.16.16.1) - VPC Router

Network + 2 (10.16.16.2) - Reserved by DNS

Network + 3 (10.16.16.3) - Reserved Future use.

Broadcast address (10.16.31.255) (last IP in subnets)

For every VPC there is a DHCP Options Set assigned to it 

Dynamic host config protocol - how computing devices receive IP address automatically.

Subnet can be set to assign Public auto IPv4 address

Helpful subnet calculator-> https://www.site24x7.com/tools/ipv4-subnetcalculator.html





--------------------------------------------------------------------------------------


VPC Router - every VPC -> Highly available

In every subnet 'network + 1' address.

Routes traffic between subnets.

Controlled by 'route tables' each subnet has one and only one.

VPC has a Main route table - subnet default.

Route tables can be attached to 0 or more subnets.

Route table controls what happens to the traffic that leaves the subnet(s) that it is associated with.

Local routes are always there, non editable, match Ipv4/Ipv6 CIDR range, and take priority exception being where the prefix value is higher.



-------------------------------------------------------------------------------------------

Internet Gateway 

Region resilient gateway attached to a VPC. Highly available.

1 VPC = 0 or 1 IGW, 1 IGW = 0 or 1 VPC. (1 to one relationship)

Runs from within the AWS Public Zone.

Gateways traffic between the VPC and the internet or AWS Public Zone (S3, SQS, SNS...etc)

Managed Gateway managed by AWS

Important: IGW translates EC2s private IPv4 address to public IP address in order for outgoing data packet to reach another instance on the internet.


-------------------------------------------------------------------------------------------

Bastion host = Jumpbox

An instance in a public subnet

Incoming management operations arrive there.

Then access internal VPC resources.

Often the only way IN to a VPC.


--------------------------------------------------------------------------------------------

Configure web subnets to be public subnets -> launch resources in the subnet, have them allocated with Public IPs

with public IPv4 address and connect to public IPv4 internet.

1. Create Attach IGW to a VPC.
2. Make web subnets public.

2.1. Create a route table for public subnet.
2.2. Associate with public subnets.
2.3. Add 2 routes to route table. Default routes(0.0.0.0/0 & ::/0) to Ipv4 and Ipv6 traffic.

3. Configure web subnet to have all resources created in the subnet to be assigned public IPv4 addresses.




----------------------------------------------------------------------------------------------


Stateful vs Stateless Firewalls:

Understand request/response cycle for the perspective of client and server.

In stateless, server has to open all ports to a response from an ephemeral port.

In stateful, server is aware of the port of the responding server and implicitly allows that port.


----------------------------------------------------------------------------------------------

NACL (Network Access Control List)

They are stateless, therefore both request and response parts of every communication need individual rules 1 X INBOUND & 1 X OUTBOUND

Tradational firewall avaialble within AWS VPCs.

Associated with Subnet.

Filter traffic crossing the subnet boundary INBOUND and OUTBOUND. Connections within a subnet aren't impacted by NACLs.

Rules match the destination IP range, Dest Port and Protocol and Allow or Deny based on that match.

Rules are processed in order, lowest rule number first. Once a match occurs, processing stops.

* is an implicit DENY if nothing else matches.

Great for blocking specific IPs that are associated with BAD actors. (Use together with security groups)

Each subnet can have on NACL (Default or custom)

A NACL can be associated with Many subnets.

------------------------------------------------------------------------------------------------

Security Groups.

Stateful - detect response traffic automatically.

Allowed (IN or OUT) request = allowed response.

No explicit DENY ... Only ALLOW or Implicit DENY. Can't block specific bad actors.

Support IP/CIDR and logical references, including other security groups AND itself, hence scalable.

Attached to ENI's not instances (Even if UI shows it this way)


-------------------------------------------------------------------------------------------------


Network Address Translation (NAT) - Not free tier eligible:

Not required for IPv6 and not work with it. Inside AWS, All IPv6 addresses in AWS are publically routable. IGW works directly with IPv6 addresses. 

(::/0 + IGW for bidirectional connectivity)

(Egress only Internet Gateway - Outbound only - works with IPv6.)

Set of diff processes remapping SRC or DST IPs

IGW performs static NAT. 

Fully mananged by AWS. Cannot connect to host OS. Don't support Security Groups but support NACLs (V.V. Imp.)

IP masquerading - hiding private CIDR Blocks behind one IP

Public IPv4 addresses running out.

Gives Private CIDR range outgoing internet* access and not the other way round.

Usecase: provide a subnet with resources having private IPs ability to get software updates.

Maintains translation table.

Runs from a public subnet.

Uses Elastic IPs (Static IPv4 Public)

AZ resilient Service (HA in that AZ)

For region resilience - NATGW in each AZ..

..RT in for private subnet in that AZ pointing to NGW in that availability zone.

Can get expensive if deployed in many AZs.

AWS Managed, scales to 45 Gbps, $ Duration & Data volume.

To use an EC2 instance as a NAT instance -> disable source and destination checks on it.

When not to Use Ec2 based instance when :

availability(automatically recover and scale), bandwidth, low maintenance and high performance, custom design

But Nat instance can be cheaper and can be multipurpose such as can act as bastion hosts, port forwarding.
filter traffic with NACL or Security Groups. 


SSH Agent Forwarding:

Check if ssh agent is running:

``` eval $(ssh-agent -s) ```

Add the private key to the ssh agent:

``` ssh-add ./A4L.pem```

Check if you have public internet connectivity:

$ ping 1.1.1.1
















