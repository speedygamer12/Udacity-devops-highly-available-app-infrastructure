# TO DO

## 2 - Setup Servers
* Resources
    * Create LBSecGroup - Set SecurityGroupIngress: IpProtocol to tcp, FromPort 80 ToPort 80, CidrIp to everyone. Set SecurityGroupEgress: IpProtocol to tcp, FromPort80 ToPort 80, CidrIp to VpcCidr.
    * Create WebServerSecGroup - Set SecurityGroupIngress: IpProtocol to tcp, FromPort 22 ToPort 22, CidrIp to Vpc Cidr. and Set SecurityGroupIngress: IpProtocol to tcp, FromPort 80 ToPort 80, CidrIp to Vpc Cidr. Set SecurityGroupEgress: IpProtocol to -1, FromPort 0 ToPort 65535, CidrIp to Everyone.
    * Create PublicSecGroup - Set SecurityGroupIngress: IpProtocol to tcp, FromPort 80 ToPort 80, CidrIp to everyone. and to IpProtocol to tcp, FromPort 22 ToPort 22, CidrIp to myIp. Set SecurityGroupEgress: IpProtocol to tcp, FromPort80 ToPort 80, CidrIp to VpcCidr.
    * Create WebAppLaunchConfig - Set UserData to given. Set ImageId to ubuntu 18. set KeyName to testkey10. Set SecGroups to Refeerence PublicSecGroup. Set InstanceType to t3.small. Set BlockSevicemappings to: list DeviceName "/dev/sdk".and Ebs to VolumeSize 10.
    * Create WebAppGroup - Set VpcZoneIdentifier to PublicSubets. SetLaunchConfigName to WepAppLaunchConfig. Set MinSize to '4'. Set MaxSize to '8'. Set TargetGroupARNS to Reference WebAppTargetGroup.
    * Create WebAppLB - Set Type to application. Set Scheme to internet-facing. Set IpAddressType to ipv4. Set Subnets to PublicSubnets. Set SecurityGroups to Reference LBSecGroup.
    * Create Listner - Set DefaultActions to list: Type to foward, TargetGroupArn to Reference WebAppTargetGroup. Set LoadBalancerArn to Reference WebAppLB. Set Port to 80, Protocol to HTTP.
    * Create ALBListenerRule - Set Actions to list: type to foward, TargetGroupArn to Reference WebAppTargetGroup. Set Conditions to list: Field to path-patter, Values to Reference [/]. Set ListenerARN to Reference Listener. Set Priority to 1.
    * Create WebAppTargetGroup - Set HealthCheckIntervalSeconds to 10. Set HealthCheckPath to /. Set HealthCheckProtocol to HTTP. Set HealthCheckTimeout to 8. Set HealthyThresholdCount to 2. Set Port to 80. Set Protocol to HTTP. Set UnHealthyThresholdCOunt to 5. Set VpcID to VPC.










## 1- Setup Network
* Parameters
    * Initiate VpcCIDR -/16 Default, PublicSubnet1CIDR -/24 Default, PublicSubnet2CIDR -/24 Default, PrivateSubnet1CIDR -/24 Default, PrivateSubnet2CIDR -/24 Default

* Resources
    * Create VPC - Set to true: EnableDnshostname, InstanceTenancy, EnableDnsSupport, Set CidrBlock, Set Tag.
    * Create InternetGateway - Set Tag
    * Create InternetGatewayAttachment - Reference InternetGateWayId, VpcId.

    * Create PublicSubnet1 & 2 - Reference PublicSubnetsCidr. Set VpcId. Set to true: MapPublicIpLaunch. Set AvailabilityZone to index 0, then index 1 of AZ list in current region.
    * Create PrivateSubnet1 & 2 - Reference PrivateSubnetsCidr. Set VpcId. Set to false: MapPublicIpLaunch. Set AvailabilityZone to index 0, then index 1 of AZ list in current region.

    * Create NatGateway1&2EIP - Set Domain to vpc
    * Create NatGateWay1&2 - Set AllocationId to GetAtt NatGatewayId.AllocationId
    * Create PublicRouteTable - Reference VpcId. Set Tag.
    * Create DefaultPublicRoute - Set DependsOn to InternetGateway. Set RouteTableId to Reference PublicRouteTable. Set DestinationCidrBlock to everyone. Set GatewayId to reference InternetGatewayId.
    * Create PublicSubnet1&2RouteTableAssociation - Set RouteTableId to Reference PublicRouteTable. Set SubnetId to reference PublicSubnet1 then 2.
    * Create PrivateRouteTable1&2: Same instruction for PublicRouteTable. 
    * Create DefaultPrivateRoute1&2: Same instruction with DefaultPublicRoute. except set GatewayId to Reference NatGateway1&2.
    * Create PrivateSubnet1&2RouteTable Association: Same instruction with PublicSubnet1&2RouteTableAssociation.

    * Create PublicNacl - Set VpcId to Reference VPC
    * Create PublicNaclEntryInbound - Set NetworkAclId to Reference PublicNacl. Set CidrBlock to Everyone. Set Protocol to -1. Set RuleNumber to 100. Set RuleAction to allow. Set Egress to false.
    * Create PubliceNaclEntryOutbound - Same instruction with PublicNaclEntryInboud except: Set egress to true.
    * Create PublicSubnet1&2NaclAssociation - Set SubnetId to Reference PublicSubnet1, then 2. Set NetworkAclId to Reference PublicNacl.
