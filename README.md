# ☁️ AWS VPC Peering — Private Network Connectivity

<p align="center">
  <strong>AWS Networking | VPC Peering | EC2 | Route Tables | Security Groups</strong>
</p>

<p align="center">
  A hands-on AWS networking project demonstrating secure private communication between two Virtual Private Clouds using VPC Peering.
</p>

---

## Project Overview

This project demonstrates how to establish private network connectivity between two Amazon Virtual Private Clouds (VPCs) using **AWS VPC Peering**.

The infrastructure was configured in the **Asia Pacific (Sydney) Region — `ap-southeast-2`** as part of the EWB Courses AWS Architect Associate Student Project.

The implementation includes two VPCs with non-overlapping IPv4 CIDR ranges, a bastion host, private EC2 application instances, route tables, and security group rules. Connectivity was validated through SSH and bidirectional ICMP ping tests.

**Project status:** Completed and tested. AWS lab resources have subsequently been cleaned up. Test results documented here represent the completed lab execution, not a currently running AWS environment.

## Objectives

- Create two isolated VPCs with non-overlapping IPv4 address ranges.
- Configure public and private subnets.
- Deploy a bastion host and private EC2 instances.
- Establish an active VPC peering connection.
- Configure forward and return routes between the VPCs.
- Restrict SSH and ICMP traffic using security groups.
- Validate connectivity and investigate a controlled network failure.
- Document the implementation, test results, and resource cleanup.

## Architecture

The project uses two VPCs connected through a VPC peering connection.

### VPC A — `ewb-vpc-a`

| Component | Configuration |
|---|---|
| VPC CIDR | `10.10.0.0/16` |
| Public subnet | `10.10.1.0/24` |
| Private subnet | `10.10.2.0/24` |
| Bastion host private IP | `10.10.1.23` |
| App-A private IP | `10.10.2.10` |

### VPC B — `ewb-vpc-b`

| Component | Configuration |
|---|---|
| VPC CIDR | `10.20.0.0/16` |
| Private subnet | `10.20.1.0/24` |
| App-B private IP | `10.20.1.10` |
| Public subnet | Not configured |

### VPC Peering Connection

| Property | Value |
|---|---|
| Name | `ewb-a-b-peer` |
| Connection ID | `pcx-0de08c729abc71fa1` |
| Status during testing | Active |
| Connected networks | VPC A ↔ VPC B |

The two VPCs use different CIDR ranges, allowing them to communicate through private IP addresses without overlapping address spaces.

### Network Traffic Flow

1. Administrative SSH access enters through the bastion host in VPC A's public subnet.
2. The bastion host provides a controlled SSH path to the private application instances.
3. App-A sends traffic to App-B using its private IPv4 address.
4. VPC A's route table forwards traffic for `10.20.0.0/16` through the peering connection.
5. VPC B's route table provides the return route for `10.10.0.0/16`.
6. Security group rules determine whether the required inbound traffic is permitted.

**Important:** VPC peering provides private connectivity between the configured networks; it does not automatically provide internet access to VPC B.

## AWS Services and Concepts

| Service / Concept | Purpose |
|---|---|
| Amazon VPC | Creates isolated virtual networks |
| Subnets | Separates public and private resources |
| EC2 | Hosts the bastion and application instances |
| VPC Peering | Connects the two VPCs privately |
| Route Tables | Directs traffic to local and remote networks |
| Internet Gateway | Provides the configured internet path for VPC A's public subnet |
| Security Groups | Controls permitted inbound network traffic |
| SSH ProxyJump | Provides an administrative access path through the bastion host |
| ICMP Ping | Tests IP-level reachability |

## Testing and Results

The following results were observed during the lab execution.

| Test | Description | Result |
|---|---|---|
| SSH access | Connected to App-B through the configured bastion `ProxyJump` path | ✅ Successful |
| App-A → App-B | Ping from `10.10.2.10` to `10.20.1.10` | ✅ 4/4 packets received |
| App-B → App-A | Ping from `10.20.1.10` to `10.10.2.10` | ✅ 4/4 packets received |
| Controlled ICMP failure | Temporarily removed the relevant App-B inbound ICMP rule | ✅ Ping failed as expected |
| Connectivity recovery | Restored the ICMP rule and tested again | ✅ Connectivity restored |

### What the tests demonstrated

The successful tests showed that private communication depended on correct routing in both directions and the required security group permissions.

The controlled failure demonstrated how a security group rule can block traffic even when a peering connection and routes are configured. Restoring the rule re-established connectivity.

> These are historical results from the completed lab. The AWS resources have since been cleaned up, so the tests cannot be rerun against the original instances.

## Security Considerations

- Application instances used private IPv4 addresses without public IPv4 addresses.
- SSH access was controlled through security group rules and the configured bastion path.
- ICMP permissions were restricted to the intended peer instance addresses.
- Forward and return routes were configured for the required private networks.
- Credentials and private keys are not included in this repository.

## Project Report

The detailed project report covers the architecture, resource inventory, subnet configuration, route tables, security group rules, connectivity tests, traffic flow, evidence, and cleanup process.

**[View or Download the Project Report](assets/Rajalakshmi_S_EWB_VPC_Peering.pdf)**

To enable this link:

1. Create an `assets` folder in the repository.
2. Copy your final report PDF into that folder.
3. Rename the file to `Rajalakshmi_S_EWB_VPC_Peering.pdf`.
4. Commit and push the PDF to GitHub.

The link will work once the PDF is present at the specified path.

## Key Learning Outcomes

- Understanding VPC CIDR blocks and subnet addressing.
- Distinguishing public and private subnets.
- Establishing a VPC peering connection.
- Configuring route tables and return routes.
- Applying security group rules to control traffic.
- Accessing private instances using SSH ProxyJump.
- Troubleshooting network failures through controlled tests.
- Documenting cloud infrastructure and cleaning up lab resources.

## Resource Cleanup

After the configuration and connectivity tests were documented, the lab resources were cleaned up to help avoid unnecessary ongoing AWS charges.

The cleanup process covered the project EC2 instances, peering connection, project networking resources, security groups, subnets, route tables, and Internet Gateway, along with checks for remaining resources.

AWS billing and usage reporting can be delayed, so reviewing Billing and Cost Management separately is recommended.

## Project Information

- **Project:** AWS VPC Peering — Private Network Connectivity
- **Author:** Rajalakshmi S
- **College:** SDNB Vaishnav College for Women
- **Course:** EWB Courses — AWS Architect Associate Student Project
- **Region:** Asia Pacific (Sydney), `ap-southeast-2`
- **Project type:** Hands-on AWS networking lab

