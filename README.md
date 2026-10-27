# aws-vpc-peering-ewb-aicte-internship-project
Hands-on AWS VPC Peering project for the EWB–AICTE internship, demonstrating private connectivity between VPCs, route table configuration, EC2 networking, and security group management.

# ☁️ AWS VPC Peering — Secure Private Network Connectivity

<p align="center">

**Connecting isolated AWS Virtual Private Clouds through private network communication**

<img src="https://img.shields.io/badge/Cloud-AWS-orange?style=for-the-badge&logo=amazonaws&logoColor=white" alt="AWS" />
<img src="https://img.shields.io/badge/Networking-VPC%20Peering-blue?style=for-the-badge" alt="VPC Peering" />
<img src="https://img.shields.io/badge/Project-Hands--On%20Lab-success?style=for-the-badge" alt="Hands-on project" />

</p>

## 📌 Project Overview

This project demonstrates how to establish **private network communication between two Amazon Virtual Private Clouds (VPCs)** using AWS VPC Peering.

VPCs provide logically isolated networks in AWS. By creating a VPC peering connection, configuring route tables, and applying appropriate security group rules, resources in different VPCs can communicate using their private IP addresses.

The implementation follows a hands-on cloud networking approach, covering network design, routing, access control, connectivity validation, and technical documentation.

> **Core objective:** Enable controlled private communication between workloads in separate VPCs without exposing their application-to-application traffic to the public internet.

## 🎯 Project Objectives

- Design two VPCs with non-overlapping IPv4 CIDR ranges.
- Create public and private subnets according to the selected architecture.
- Establish and activate a VPC peering connection.
- Configure route tables for communication between the VPCs.
- Apply security group rules following the principle of least privilege.
- Validate connectivity using private IP addresses and network testing.
- Document the architecture, configuration, troubleshooting, and results.

## 🏗️ Architecture

### Network design

| Component | Configuration |
|---|---|
| VPC A | `10.10.0.0/16` |
| VPC B | `10.20.0.0/16` |
| Public subnet — VPC A | `10.10.1.0/24` |
| Private subnet — VPC A | `10.10.2.0/24` |
| Private subnet — VPC B | `10.20.1.0/24` |
| Connectivity | AWS VPC Peering |
| Compute | Amazon EC2 |
| Network security | Security Groups |
| Routing | VPC Route Tables |

*These are the reference CIDRs for the planned design. Update this table if your actual AWS deployment differs.*

### Architecture flow

```text
                       ADMINISTRATOR
                             |
                             | SSH
                             v
                 +------------------------+
                 | VPC A                  |
                 | 10.10.0.0/16           |
                 |                        |
                 |  Public Subnet         |
                 |  10.10.1.0/24          |
                 |  +------------------+  |
                 |  | Bastion Host     |  |
                 |  +------------------+  |
                 |           |            |
                 |  Private Subnet         |
                 |  10.10.2.0/24          |
                 |  +------------------+  |
                 |  | Private EC2 A    |  |
                 |  +------------------+  |
                 +-----------+------------+
                             |
                     VPC PEERING
                    PRIVATE ROUTING
                             |
                 +-----------+------------+
                 | VPC B                  |
                 | 10.20.0.0/16           |
                 |                        |
                 |  Private Subnet        |
                 |  10.20.1.0/24          |
                 |  +------------------+  |
                 |  | Private EC2 B    |  |
                 |  +------------------+  |
                 +------------------------+
```

**Architecture note:** The diagram illustrates the intended design. Replace it with a screenshot or diagram matching your final deployed architecture.

## ⚙️ AWS Services Used

| Service | Purpose |
|---|---|
| Amazon VPC | Creates isolated virtual networks |
| VPC Peering | Connects the two VPCs privately |
| Amazon EC2 | Hosts the test workloads and bastion host |
| Route Tables | Directs traffic to local networks or the peering connection |
| Security Groups | Controls permitted inbound and outbound traffic |
| Internet Gateway | Provides internet routing for the public subnet, where configured |
| Amazon Linux | Operating system for the EC2 instances |

## 🔧 Implementation Workflow

### 1. Create the VPCs

Create two VPCs with distinct, non-overlapping CIDR ranges:

- VPC A: `10.10.0.0/16`
- VPC B: `10.20.0.0/16`

### 2. Configure the subnets

Create the public subnet and private subnets required by the architecture. Enable public IPv4 addressing only where necessary.

### 3. Configure internet access

Attach an Internet Gateway to VPC A and configure the public subnet's route table if a public bastion host is used.

Private workloads do not require public IP addresses for communication across the VPC peering connection.

### 4. Create the VPC peering connection

Create a peering connection between VPC A and VPC B, then accept the request if required. Verify that its status is **Active**.

### 5. Update route tables

Configure routes for the remote VPC CIDR in the route tables used by communicating resources.

| Route table | Destination | Target |
|---|---|---|
| VPC A public subnet | `10.20.0.0/16` | VPC peering connection |
| VPC A private subnet | `10.20.0.0/16` | VPC peering connection |
| VPC B private subnet | `10.10.0.0/16` | VPC peering connection |

Keep each subnet's local VPC route. Add other routes only where required by your actual design.

### 6. Configure security groups

Apply restrictive inbound rules to the EC2 instances:

- Allow SSH to the bastion host only from your authorized public IP.
- Allow SSH to private instances only from the approved administration path.
- Permit the required test traffic between the private instances.
- Avoid unrestricted inbound SSH access from `0.0.0.0/0`.

Security groups and network routes must both permit the intended traffic.

### 7. Launch and configure EC2 instances

Launch the instances in their intended subnets and verify their private IPv4 addresses. Use a secure administration method to access private instances.

### 8. Validate connectivity

Test communication using the private IP addresses of the remote instances. Record the commands, outputs, and any troubleshooting performed.

## 🧪 Validation and Testing

Complete this table using your actual test results.

| Test case | Expected outcome | Actual result |
|---|---|---|
| VPC peering status | Active | `UPDATE AFTER TESTING` |
| VPC CIDR ranges | Non-overlapping | `UPDATE AFTER TESTING` |
| Route table configuration | Correct peering routes | `UPDATE AFTER TESTING` |
| Private EC2 A → Private EC2 B | Connectivity if permitted by security rules | `UPDATE AFTER TESTING` |
| Private EC2 B → Private EC2 A | Connectivity if permitted by security rules | `UPDATE AFTER TESTING` |
| Unauthorized inbound access | Blocked by the configured rules | `UPDATE AFTER TESTING` |

**Example connectivity test**

From an authorized instance, test the other instance's private IP:

```bash
ping <REMOTE_PRIVATE_IP>
```

If ICMP is permitted and the destination responds, the command should return replies. A failed ping does not automatically mean VPC peering is broken: check route tables, security groups, network ACLs, host firewalls, and ICMP configuration.

> Replace all placeholder results with genuine evidence before presenting the project as complete.

## 🔐 Security Considerations

- Use non-overlapping CIDR ranges.
- Restrict administrative access to trusted sources.
- Keep private workloads private unless public exposure is explicitly required.
- Allow only the protocols and ports needed for testing.
- Never commit private SSH keys, AWS access keys, passwords, or secrets.
- Review and remove temporary resources after completing the lab.

## 📁 Repository Structure

```text
aws-vpc-peering-project/
│
├── README.md
│
├── architecture/
│   └── vpc-peering-diagram.png
│
├── documentation/
│   ├── project-overview.md
│   ├── network-configuration.md
│   └── testing-results.md
│
└── screenshots/
    ├── vpc-creation.png
    ├── subnet-configuration.png
    ├── peering-connection.png
    ├── route-table-configuration.png
    ├── security-group-rules.png
    └── connectivity-test.png
```

*Keep only files that you have actually created. Use your own AWS console screenshots and test evidence.*

## 🧠 Key Learning Outcomes

Through this project, I aim to strengthen my practical understanding of:

- AWS VPC architecture and IPv4 CIDR planning.
- Public and private subnet design.
- VPC peering and private network communication.
- Route table configuration and traffic flow.
- Security groups and least-privilege access.
- EC2 administration and connectivity troubleshooting.
- Cloud documentation and evidence-based validation.

## 🚀 Future Improvements

Possible extensions include:

- Automating the infrastructure with Terraform.
- Implementing more restrictive security group rules.
- Adding CloudWatch monitoring and logging.
- Exploring AWS Transit Gateway for larger multi-VPC environments.
- Documenting deployment and cleanup procedures as repeatable workflows.

## 💰 Cost and Cleanup

AWS resources may incur charges depending on the account, region, instance type, public IPv4 usage, and other services used.

After capturing the required evidence, delete the resources you no longer need, including EC2 instances, the peering connection, unused VPC components, and any associated chargeable resources. Verify that no unwanted resources remain.

## 📚 References

- [Amazon VPC Documentation](https://docs.aws.amazon.com/vpc/latest/userguide/what-is-amazon-vpc.html)
- [VPC Peering Guide](https://docs.aws.amazon.com/vpc/latest/peering/what-is-vpc-peering.html)
- [VPC Route Tables](https://docs.aws.amazon.com/vpc/latest/userguide/VPC_Route_Tables.html)
- [Amazon EC2 Documentation](https://docs.aws.amazon.com/ec2/)

## 👨‍💻 Author

**Thatha Rohith**

B.Tech — Artificial Intelligence and Data Science

**Focus areas:** AWS Cloud | Networking | Data Engineering | Cloud Infrastructure

- GitHub: [thatha-rohith](https://github.com/thatha-rohith)
- Project repository: `ADD YOUR REPOSITORY URL`

---

*Built as a hands-on AWS networking learning project. Architecture details, screenshots, and validation results should reflect the actual implementation.*

