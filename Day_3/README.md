# Day 3 : Create and Configure a Virtual Private Cloud (VPC) with Subnets.

## VPC networks
A Virtual Private Cloud (VPC) network is a virtual version of a physical network that is implemented inside of Google's production network by using Andromeda.

A VPC network does the following:

1. Provides connectivity for your Compute Engine virtual machine (VM) instances.
2. Offers native internal passthrough Network Load Balancers and proxy systems for internal Application Load Balancers.
3. Connects to on-premises networks by using Cloud VPN tunnels and VLAN attachments for Cloud Interconnect.
4. Distributes traffic from Google Cloud external load balancers to backends.

!["VPC network image"](https://cloud.google.com/static/vpc/images/vpc-overview-example.svg)
## Subnets
Virtual Private Cloud (VPC) networks are global resources. Each VPC network consists of one or more IP address ranges called subnets. Subnets are regional resources, and have IP address ranges associated with them.

In Google Cloud, the terms subnet and subnetwork are synonymous. They are used interchangeably in the Google Cloud console, Google Cloud CLI commands, and API documentation.


### Types of subnets
VPC networks support subnets with the following stack types. A single VPC network can contain any combination of these subnets.


 
| Stack type	 | Stack type | Compatible VM network interfaces |
|---|---|---|
| IPv4-only (single-stack) | 	Only IPv4 subnet ranges | 	IPv4-only interfaces |
| IPv4 and IPv6 (dual-stack) | Both IPv4 and IPv6 subnet ranges  | IPv4-only, dual-stack, and IPv6-only interfaces |
| IPv6-only (single-stack) | Only IPv6 subnet ranges | IPv6-only interfaces |



## Task 1. Create a new instance from the Cloud console
   
### Create a custom mode VPC network with a dual-stack subnet

You can create subnets when you create the network, or you can add them later.

A dual-stack subnet can have one of the following configurations for its primary IP address ranges:

* Internal IPv4 range and internal IPv6 range
* Internal IPv4 range and external IPv6 range

## Steps:

1. In the Google Cloud console, go to the VPC networks page.

> Go to VPC networks

2. Click Create VPC network.

3. Enter a Name for the network.

4. Maximum transmission unit (MTU): Choose whether the network has an MTU of 1460 (default), 1500, or 8896. Before setting the MTU to a value higher than 1460, review Maximum transmission unit.

5. For Subnet creation mode, choose Custom.

6. If you want to configure internal IPv6 address ranges on subnets in this network, complete these steps:

    * In the Private IPv6 address settings section, select Configure a ULA internal IPv6 range for this VPC Network.
    * For Allocate internal IPv6 range, select Automatically or Manually.

        If you select Manually, enter a /48 range from within the fd20::/20 range. If the range is in use, you are prompted to provide a different range.

7. In the New subnet section, specify the following configuration parameters for a subnet:

    * Provide a Name for the subnet.
    * Select a Region.
    * To create a dual-stack subnet, for IP stack type, select IPv4 and IPv6 (dual-stack).
    * Enter an IPv4 range. This is the primary IPv4 range for the subnet.

        If you select a range that is not an RFC 1918 address, confirm that the range doesn't conflict with an existing configuration. For more information, see IPv4 subnet ranges.

    * Select an IPv6 access type: External or Internal.

    * Click Done.

8. To add more subnets, click Add subnet and repeat the previous steps.

    Each subnet in a VPC network can have different stack type and IPv6 access type configurations. You do not need to configure all subnets in a given network as dual-stack subnets.

    You can also add more subnets of any stack type to the network after you create the network.

9. In the Firewall rules section, select zero or more predefined firewall rules in the IPv4 firewall rules and IPv6 firewall rules tabs. The rules address common use cases for connectivity to instances.

    If you don't select any predefined rules, you can create your own firewall rules after you create the network.

    Each predefined rule name starts with the name of the VPC network that you are creating, NETWORK. In both the IPv4 firewall rules tab and the IPv6 firewall rules tab, there is an editable predefined ingress firewall rule; the IPv4 rule is named NETWORK-allow-custom and the IPv6 rule is named NETWORK-allow-ipv6-custom. On the right side of the row that contains the rule, click Edit to select subnets, add additional IP ranges, and specify protocols and ports.

    The NETWORK-allow-custom and NETWORK-allow-ipv6-custom firewall rule are not automatically updated if you later add additional subnets. If you need firewall rules for the new subnets, you must update the firewall configuration to add the rules.

10. Choose the Dynamic routing mode for the VPC network.

    For more information, see dynamic routing mode. You can change the dynamic routing mode later.

11. Click Create.

## Reference :
Google Cloud  :
[Virtual Network](https://cloud.google.com/vpc/docs/vpc)

Google Skill : [VPC Networking Fundamentals](https://www.skills.google/focuses/1229?catalog_rank=%7B%22rank%22%3A2%2C%22num_filters%22%3A0%2C%22has_search%22%3Atrue%7D&parent=catalog&search_id=56720316)