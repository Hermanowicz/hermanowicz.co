---
title: 'VPC setup using cloudformation on AWS'
description: 'AWS VPC basics using cloudformation'
pubDate: 'Jun 15 2024'
heroImage: '/blog-placeholder-2.jpg'
tags: ['AWS', 'tcp/ip', 'network', 'iac']
---

## Intro

VPC contains two main subnets (Private & Public) in VPC with /16 CIDR block (172.16.0.0).
Public subnet 172.16.250.0/24 & Private 172.16.1.0/20, private subnets connect to internet using NAT Gateway. By default, Assignment off public IP in Public subnet is disabled in this example this will require Attachment of EIP, but provided full control.

## CF code

```yaml
Resources:
  MainVPC:
    Type: AWS::EC2::VPC
    Properties:
      CidrBlock: 172.32.0.0/16
      EnableDnsSupport: true
      EnableDnsHostnames: true
      Tags:
        - Key: Name
          Value: Custome_VPC_172.32.0.0

  NatGatewayEip:
    Type: AWS::EC2::EIP
    Properties:
      Domain: vpc
      Tags:
        - Key: Name
          Value: Custome_VPC_NAT_EIP

  PublicSubnet:
    Type: AWS::EC2::Subnet
    Properties:
      VpcId: !Ref MainVPC
      MapPublicIpOnLaunch: false
      CidrBlock: 172.32.250.0/24
      AvailabilityZone: "eu-central-1c"

  PrivateSubnet:
    Type: AWS::EC2::Subnet
    Properties:
      VpcId: !Ref MainVPC
      CidrBlock: 172.32.1.0/20
      AvailabilityZone: "eu-central-1c"

  PrivRouteTable:
    Type: AWS::EC2::RouteTable
    Properties:
      VpcId: !Ref MainVPC

  PublicRouteTable:
    Type: AWS::EC2::RouteTable
    Properties:
      VpcId: !Ref MainVPC

  PrivateRouteAssoc:
    Type: AWS::EC2::SubnetRouteTableAssociation
    Properties:
      SubnetId: !Ref PrivateSubnet
      RouteTableId: !Ref PrivRouteTable

  PublicRouteAssoc:
    Type: AWS::EC2::SubnetRouteTableAssociation
    Properties:
      SubnetId: !Ref PublicSubnet
      RouteTableId: !Ref PublicRouteTable

  InternetGateway:
    Type: AWS::EC2::InternetGateway

  AttachGateway:
    Type: AWS::EC2::VPCGatewayAttachment
    Properties:
      VpcId: !Ref MainVPC
      InternetGatewayId: !Ref InternetGateway

  RouteInternetGateway:
    DependsOn: InternetGateway
    Type: AWS::EC2::Route
    Properties:
      RouteTableId: !Ref PublicRouteTable
      DestinationCidrBlock: '0.0.0.0/0'
      GatewayId: !Ref InternetGateway

  NATGateway:
    Type: AWS::EC2::NatGateway
    Properties:
      AllocationId: !GetAtt NatGatewayEip.AllocationId
      SubnetId:  !Ref PublicSubnet

  RouteNATGateway:
    DependsOn: NATGateway
    Type: AWS::EC2::Route
    Properties:
      RouteTableId: !Ref PrivRouteTable
      DestinationCidrBlock: '0.0.0.0/0'
      NatGatewayId: !Ref NATGateway

Outputs:
  MyMainVpc:
    Description: Main VPC with NAT and Internet Gateway
    Value: !Ref MainVPC
    Export:
      Name: MyMainVpc

  PublicRouteTable:
    Description: Public route table by default with route to IGW
    Value: !Ref PublicRouteTable
    Export:
      Name: PublicRouteTable

  PrivateRouteTable:
    Description: Private Route Table with NAT support.
    Value: !Ref PrivRouteTable
    Export:
      Name: PrivateRouteTable
```
