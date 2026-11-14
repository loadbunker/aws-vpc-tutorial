
# Designing and Troubleshooting a Custom AWS VPC Network

**Project title:** Designing and Troubleshooting a Custom AWS VPC Network  
**Scenario:** An organisation wants to isolate its cloud resources – public‑facing servers need Internet connectivity, internal resources must stay private.

## Table of Contents
1. Prerequisites
2. Step‑by‑step implementation
3. Troubleshooting scenarios
4. Architecture diagram
5. License

## Prerequisites
- AWS account with permission to create VPC, subnets, IGW, route tables, EC2, and security groups.  
- AWS Management Console (or AWS CLI) access.  
- PowerShell / Git installed on your workstation.  
- GitHub account (you already have one).

## Step‑by‑step implementation

### Step 1 – Create a custom VPC
| Setting | Value |
|---------|-------|
| Name    | `cloud-support-vpc` |
| IPv4 CIDR | `10.0.0.0/16` |
| Tenancy | `Default` |

*(Console → VPC → Your VPCs → Create VPC → fill table → Create VPC)*

### Step 2 – Create two subnets
| Subnet | CIDR | Purpose |
|--------|------|---------|
| Public subnet | `10.0.1.0/24` | Internet‑facing resources |
| Private subnet | `10.0.2.0/24` | Internal resources |

*(Console → VPC → Subnets → Create subnet → select VPC → add both CIDRs)*

### Step 3 – Create an Internet Gateway
1. VPC → Internet Gateways → **Create internet gateway**, name `cloud-support-igw`.  
2. **Attach** it to `cloud-support-vpc`.

### Step 4 – Configure route tables
| Route table | Routes |
|-------------|--------|
| **Public** (`cloud-support-public-rt`) | `10.0.0.0/16 → local`<br>`0.0.0.0/0 → cloud-support-igw` |
| **Private** (`cloud-support-private-rt`) | `10.0.0.0/16 → local` (only) |

*Associate the public route table with the public subnet and the private route table with the private subnet.*

### Step 5 – Deploy an EC2 instance (public subnet)
- AMI: Amazon Linux 2 (or Ubuntu 22.04)  
- Instance type: `t2.micro`  
- Subnet: **public subnet** (`10.0.1.0/24`)  
- Auto‑assign Public IPv4: **Enabled**  
- Security group: `cloud-support-sg` (SSH 22 from your IP, HTTP 80 open)  
- Key pair: `cloud-support-key` (the `.pem` you used)

After launch, SSH from your laptop:

```powershell
ssh -i "C:\Users\MASF\Desktop\ssh\cloud-support-key.pem" ec2-user@<public‑IP>
