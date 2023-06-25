# Network Design Document

## Overview
This document outlines the network design for the AWS VPC infrastructure with an EC2 instance running Windows Server 2019. The Windows Server will act as the primary DNS server for remote networks. Additionally, Active Directory will be installed on the Windows Server. The AWS VPC will connect to a private network using an IPsec VPN, and the connection will be established through a pfSense router. Departmental VLANs will be implemented to connect Windows 10 endpoints.

## AWS VPC Design

### VPC Configuration
- VPC Name: Globex VPC
- CIDR Block: 10.1.0.0/16

### Subnets
- Private Subnet
  - CIDR Block: 10.1.1.0/24
  - Availability Zone: us-east-1b
  - Route Table:

    | Component                    | IP Address       |
    |------------------------------|-----------------|
    | Internet Gateway             | 0.0.0.0/0      |
    | Virtual Private Gateway              | 192.168..1.0/24        |
    | VPN Customer Gateway      | 34.193.147.83   |


### Security Groups
- Windows Server Security Group:
  - Inbound Rule: Allow RDP (TCP Port 3389) from specific IP ranges
  - Inbound Rule: Allow SSH from specific IP ranges
  - Inbound Rule: Allow all UDP from specific IP ranges
  - Inbound Rule: Allow ICMPv4 from specific IP ranges
  - Outbound Rule: Allow all traffic to the Internet

## Windows Server Configuration

### Server Details
- Operating System: Windows Server 2019
- Instance Type: t2.micro
- Private IP: 10.1.1.50

### Services
- DNS Server Role: Primary DNS server for remote networks
- Active Directory: Installed and configured

## IPsec VPN Configuration

### pfSense Router
- Router Type: pfSense

    | Network          | IP Address    | Subnet Mask  |
    |------------------|---------------|--------------|
    | Public IP NAT    | 15.0.1.0/24   | N/A          |
    | Public IP        | 192.168.0.1  | 255.255.255.0 |
    | Internal IP      | 10.0.5.1      | 255.255.255.0 |
    | DHCP Lease Range  | 10.0.5.50 - 10.0.5.250 |
    | Reserved Address Range | 10.0.5.2 - 10.0.5.49 |


### VPN Configuration
- VPN Type: IPsec
- Authentication Method: Pre-shared Key
- Tunnel Encryption: AES-256
- Hash Algorithm: SHA-256
- Diffie-Hellman Group: Group 2
- Phase 1 Proposal: AES256-SHA256
- Phase 2 Proposal: AES256-SHA256

## Departmental VLANs

### VLAN Configuration
- VLAN 1: Engineering
- VLAN 2: Opeerations
- VLAN 3: Sales & Marketing
- VLAN 4: Customer support

### Windows 10 Endpoints
- Each department will have Windows 10 endpoints connected to the respective VLANs.