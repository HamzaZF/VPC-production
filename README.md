
# VPC Production Setup

This project sets up a production-ready Virtual Private Cloud (VPC) environment on AWS using CloudFormation templates. The stack is designed for high availability and security, supporting essential infrastructure components including public and private subnets, a bastion host, load balancer, and secure access policies.

## Table of Contents
- [Project Overview](#project-overview)
- [Architecture Diagram](#architecture-diagram)
- [CloudFormation Templates](#cloudformation-templates)
- [Features](#features)
- [Setup Instructions](#setup-instructions)
- [Parameter Details](#parameter-details)
- [Outputs](#outputs)
- [License](#license)

## Project Overview

This project consists of a master stack that orchestrates nested stacks for:
- Networking: VPC, subnets, gateways, and routing
- Security Groups: Configuring security rules for EC2 instances, load balancer, and bastion host
- Application: Deployment configurations for application resources

The structure follows AWS best practices for network security and scalability, ensuring efficient traffic management and controlled access to private resources.

## Architecture Diagram

The following diagram illustrates the overall VPC architecture:

![VPC Architecture](VPC-production.svg)

## CloudFormation Templates

### Main Stack (Master Stack)
The main stack (`MasterStack.yaml`) defines nested stack URLs for the following:
- **Network Stack**: Configures VPC, subnets, Internet Gateway, NAT Gateway, and route tables.
- **Security Group Stack**: Configures security groups for EC2 instances, load balancer, and bastion host.
- **Application Stack**: Configures application deployment settings.

### Nested Stacks
1. **NetworkStack.yaml**: Configures the VPC, public/private subnets, bastion subnet, Internet Gateway, and NAT Gateway.
2. **SecurityGroupStack.yaml**: Sets up security groups for instances, load balancer, and bastion host with ingress/egress rules.
3. **ApplicationStack.yaml**: Defines the EC2 instance configurations, including AMI, instance type, key pairs, and other application settings.

## Features

- **High Availability**: Subnets are distributed across multiple Availability Zones.
- **Secure Access**: Bastion host allows SSH access to private instances, while the load balancer provides public-facing HTTP access.
- **Modularity**: Uses nested stacks for organized, reusable infrastructure templates.
- **Parameterization**: Easily customize CIDR blocks, AMIs, instance types, and other configurations.

## Setup Instructions

### Prerequisites
- AWS account with permissions to create VPCs, EC2 instances, and CloudFormation stacks.
- AWS CLI installed and configured or access to AWS Management Console.
- Key pair for SSH access to EC2 instances (for bastion and application instances).

### Deployment Steps

1. **Clone the Repository**
   ```bash
   git clone https://github.com/yourusername/vpc-production-setup.git
   cd vpc-production-setup
   ```

2. **Upload Nested Templates to S3**

   Upload the `NetworkStack.yaml`, `SecurityGroupStack.yaml`, and `ApplicationStack.yaml` templates to an S3 bucket and note their URLs. 

3. **Update `MasterStack.yaml` with the correct S3 URLs**

   Change S3 URLs under `SecurityGroupTemplateURL`, `NetworkTemplateURL`, and `ApplicationTemplateURL` in the `MasterStack.yaml` template.

3. **Deploy the Master Stack**

   After the nested templates are uploaded to S3, use the AWS CLI or Management Console to deploy `MasterStack.yaml`
   ```bash
   aws cloudformation create-stack --stack-name VPC-production --template-body file://MasterStack.yaml --parameters ParameterKey=VpcCidr,ParameterValue=10.0.0.0/16 ...
   ```

4. **Monitor Stack Creation**

   Wait for stack creation to complete. You can view progress in the CloudFormation console or with:
   ```bash
   aws cloudformation describe-stacks --stack-name VPCProduction
   ```

5. **Access Resources**
   - **Bastion Host**: Use the bastion host's Elastic IP to SSH into private instances.
   - **Load Balancer**: Use the load balancer’s DNS to access the application.

## Parameter Details

| Parameter                  | Description                                             | Default                |
|----------------------------|---------------------------------------------------------|------------------------|
| `VpcCidr`                  | CIDR block for the VPC                                  | 10.0.0.0/16           |
| `PublicSubnet0Cidr`        | CIDR block for the first public subnet                  | 10.0.1.0/24           |
| `PublicSubnet1Cidr`        | CIDR block for the second public subnet                 | 10.0.2.0/24           |
| `PrivateSubnet0Cidr`       | CIDR block for the first private subnet                 | 10.0.3.0/24           |
| `PrivateSubnet1Cidr`       | CIDR block for the second private subnet                | 10.0.4.0/24           |
| `BastionSubnetCidr`        | CIDR block for the bastion subnet                       | 10.0.5.0/24           |
| `EC2InstanceKeyName`       | Key name for EC2 instance SSH access                    | `VPC-EC2`             |
| `VPCBastionHostKeyName`    | Key name for SSH access to the bastion host             | `VPC-BastionHost`     |
| `ImageId`                  | AMI ID for EC2 instances                                | `ami-06b21ccaeff8cd686` |
| `InstanceType`             | EC2 instance type                                      | `t2.micro`            |


## License

This project is licensed under the MIT License - see the [LICENSE](LICENSE) file for details.
