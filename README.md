# Deploy Windows Server Using AWS CloudFormation

This AWS CloudFormation template demonstrates deployment of Windows Server EC2 instances.

The CloudFormation template demonstrates the following features:
* Optional joining to AWS Managed Active Directory.
* Optional use of a CIDR or prefix list to constrain the source of direct RDP access.
* Support for using either an EC2 SSH key pair or AWS Systems Manager Session Manager to remotely connect via RDP and via a PowerShell session without the need for bastion hosts and/or public IP addresses configured on the EC2 instance.
* Use of [`AWS::CloudFormation::Init`](https://docs.aws.amazon.com/AWSCloudFormation/latest/UserGuide/aws-resource-init.html) to carry out first boot configuration. 
* Use of AWS CloudWatch Agent to stream log data to CloudWatch Logs and to send Windows Server metrics to CloudWatch Metrics.

## Usage

Use AWS CloudFormation to create a stack using the template `samples-windows-server.yml`. See the next section for an explanation of the supported [parameters](#parameters).

Stack creation may take 5-10 minutes.  Since the template uses `cfn-signal` to inform CloudFormation that first boot configuration has completed, when the stack creation is complete, the Windows Server instance is ready to use.

### Accessing Windows Server via RDP and AWS Systems Manager Session Manager

See [Enable RDP Through Session Manager](https://reinvent2019.aws-management.tools/mgt406/en/optional/step7.html) for details on accessing your Windows Server instances without the use of bastion hosts and without needing to associate a public IP address with your EC2 instance.

If you're joining the instance to a domain, then you can skip the step of creating a local user. Instead, you can either use the domain admin or a user available in the domain who is part of the appropriate group.

## Parameters

|Parameter|Required|Description|Default|
|---------|--------|-----------|-------|
|**System Classification and Environment**| | | |
|`pOrg`|Optional|As an example of using resource naming standards, include the business organization in the names of resources including, for example, IAM roles.|`example`|
|`pSystem`|Optional|As an example of using resource naming standards, include a system identifier in the names of resources including, for example, IAM roles..|`samples`|
|`pApp`|Optional|As an example of using resource naming standards, include an application identifier in the names of resources including, for example, IAM roles.|`windows-server`|
|`pEnvPurpose`|Required|As an example of using resource naming standards, include a purpose for this particular instance of the stack in the names of resources including, for example, IAM roles.. For example, "dev1", "test", "1", etc.|None|
|**Windows Server Instance Configuration**| | | |
|`pAmiId`|Optional|EC2 AMI ID.|Resolved value of SSM parameter: `/aws/service/ami-windows-latest/Windows_Server-2019-English-Full-Base`|
|`pInstanceType`|Optional|EC2 instance type. See the template for supported types|`t3a.micro`|
|`pDomainJoined`|Optional|`true` or `false`. Automatically configure the Windows Server instances to join the specified Active Directory (AD) domain.|`false`|
|`pKeyPair`|Optional|Name of EC2 key pair if you plan on using RDP for remote desktop access. A key pair is not necessary if you plan to use AWS Systems Manager Session Manager to access the Windows Server instance.|None|
|**Network Configuration**| | | |
|`pVpcId`|Required|ID of the VPC to which to deploy the application.|None|
|`pSubnetId`|Required|ID of the subnet in which to deploy the Windows Server instance.||
|`pPublicIpAddress`|Optional|`true` or `false`. Automatically assign a public IP address to the instance. If you plan to use AWS Systems Manager Session Manager to access the Windows Server instance, you should specify `false`.|`false`|
|`pIngressCidr`|Optional. Only applies when `pKeyPair` is provided. Mutually exclusive with `pIngressPrefixListId`.|CIDR block to restrict ingress access.|None|
|`pIngressPrefixListId`|Optional. Only applies when `pKeyPair` is provided. Mutually exclusive with `pIngressCidr`|Prefix list ID to restrict ingress access.|None|
|**Active Directory Settings for Domain Joined Instances**| | | |
|`pActiveDirectoryId`|Required when `pDomainJoined` set to `true`|ID of the AWS Managed Active Directory.|None|
|`pActiveDirectoryDomainName`|Required when `pDomainJoined` set to `true`|Domain name associated with the AWS Managed Active Directory.|None|
|`pActiveDirectoryIpAddress1`|Required when `pDomainJoined` set to `true`|One of two IP addresses associated with AWS Managed Active Directory.|None|
|`pActiveDirectoryIpAddress2`|Required when `pDomainJoined` set to `true`|Second of two IP addresses associated with AWS Managed Active Directory.|None|
|**Other**| | | ||
`pPermissionsBoundaryArn`|Optional|ARN of an IAM Permissions boundary policy if you are required to use a permissions boundary whenever you create an IAM role.||