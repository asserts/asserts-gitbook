---
description: Quick start guide for Deploying Asserts on a EC2 Instance
---

# AWS CloudFormation

## **Before you begin**

### **Required P**rerequisites

* An **AWS Account** and **Access** to create **CloudFormation Stacks**
* An <mark style="color:orange;">**AWS EC2 key-pair**</mark>
* An <mark style="color:orange;">**AWS VPC**</mark>
* An <mark style="color:orange;">**AWS Public Subnet**</mark>

### **Optional P**rerequisites

All Asserts services publish their own metrics, which are in turn consumed by Asserts so it can monitor itself. This enables you to install and run Asserts without the following prerequisites to get a taste of the value that Asserts aims to provide. However, to realize the full potential that Asserts can provide please consider satisfying the following prerequisites.

* A Prometheus compatible endpoint to query (can be multiple)
* [node-exporter](https://github.com/prometheus/node\_exporter)
* [cadvisor](https://github.com/google/cadvisor)

\


## Stack Info

### Parameters

* <mark style="color:orange;">**KeyName**</mark>\
  ****Name of an existing EC2 KeyPair to enable SSH access to the instance\
  <mark style="color:red;">Required Input</mark>: Drop down selector auto populates with Keys available to user
* <mark style="color:orange;">**VpcID**</mark>\
  The VPC ID\
  <mark style="color:red;">Required Input</mark>: Drop down selector auto populates with VPCs available to user
* <mark style="color:orange;">**SubnetId**</mark>\
  ****The ID of the subnet to launch the EC2 instance into. Must be a public subnet!\
  <mark style="color:red;">Required Input</mark>: Drop down selector auto populates with subnets available to user
* <mark style="color:orange;">**Amazon Machine Image ID**</mark>\
  ****The ID of the AMI\
  <mark style="color:green;">Default</mark>: Dynamically set based on AWS region
* <mark style="color:orange;">**InstanceType**</mark>\
  ****AssertsServer EC2 instance type\
  <mark style="color:green;">Default</mark>: m5.xlarge
* <mark style="color:orange;">**DiskSize**</mark>\
  ****The size of the volume, in GiBs.\
  <mark style="color:green;">Default</mark>: 200
* <mark style="color:orange;">**SSHLocation**</mark>\
  ****The IP address range that can be used to SSH to the EC2 instances\
  <mark style="color:green;">Default</mark>: 0.0.0.0/0
* <mark style="color:orange;">**VPNLocation**</mark>\
  ****The IP address range that can be used to Access the EC2 instances\
  <mark style="color:green;">Default</mark>: 0.0.0.0/0

### Resources

* IAM::InstanceProfile
* IAM::Policy
* IAM::Role
* EC2::EIP
* EC2::Instance
* EC2::SecurityGroup

### _Quick-Create Links_

_Region specific Quick-Create Links for Asserts Stack_

{% tabs %}
{% tab title="US" %}
| ``[`us-east-1`](https://us-east-1.console.aws.amazon.com/cloudformation/home?region=us-east-1#/stacks/create/review?templateURL=https://s3.us-west-2.amazonaws.com/cfn.asserts.ai/tenants/asserts/ec2.yaml\&stackName=Asserts)`` | [US East (N. Virginia)](https://us-east-1.console.aws.amazon.com/cloudformation/home?region=us-east-1#/stacks/create/review?templateURL=https://s3.us-west-2.amazonaws.com/cfn.asserts.ai/tenants/asserts/ec2.yaml\&stackName=Asserts)   |
| -------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- | ---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| ``[`us-east-2`](https://us-east-2.console.aws.amazon.com/cloudformation/home?region=us-east-2#/stacks/create/review?templateURL=https://s3.us-west-2.amazonaws.com/cfn.asserts.ai/tenants/asserts/ec2.yaml\&stackName=Asserts)`` | [US East (Ohio)](https://us-east-2.console.aws.amazon.com/cloudformation/home?region=us-east-2#/stacks/create/review?templateURL=https://s3.us-west-2.amazonaws.com/cfn.asserts.ai/tenants/asserts/ec2.yaml\&stackName=Asserts)          |
| ``[`us-west-1`](https://us-west-1.console.aws.amazon.com/cloudformation/home?region=us-west-1#/stacks/create/review?templateURL=https://s3.us-west-2.amazonaws.com/cfn.asserts.ai/tenants/asserts/ec2.yaml\&stackName=Asserts)`` | [US West (N. California)](https://us-west-1.console.aws.amazon.com/cloudformation/home?region=us-west-1#/stacks/create/review?templateURL=https://s3.us-west-2.amazonaws.com/cfn.asserts.ai/tenants/asserts/ec2.yaml\&stackName=Asserts) |
| ``[`us-west-2`](https://us-west-2.console.aws.amazon.com/cloudformation/home?region=us-west-2#/stacks/create/review?templateURL=https://s3.us-west-2.amazonaws.com/cfn.asserts.ai/tenants/asserts/ec2.yaml\&stackName=Asserts)`` | [US West (Oregon)](https://us-west-2.console.aws.amazon.com/cloudformation/home?region=us-west-2#/stacks/create/review?templateURL=https://s3.us-west-2.amazonaws.com/cfn.asserts.ai/tenants/asserts/ec2.yaml\&stackName=Asserts)        |
{% endtab %}

{% tab title="Asia" %}
| ``[`ap-northeast-1`](https://ap-northeast-1.console.aws.amazon.com/cloudformation/home?region=ap-northeast-1#/stacks/create/review?templateURL=https://s3.us-west-2.amazonaws.com/cfn.asserts.ai/tenants/asserts/ec2.yaml\&stackName=Asserts)`` | [Asia Pacific (Tokyo)](https://ap-northeast-1.console.aws.amazon.com/cloudformation/home?region=ap-northeast-1#/stacks/create/review?templateURL=https://s3.us-west-2.amazonaws.com/cfn.asserts.ai/tenants/asserts/ec2.yaml\&stackName=Asserts)     |
| ----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- | --------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| ``[`ap-northeast-2`](https://ap-northeast-2.console.aws.amazon.com/cloudformation/home?region=ap-northeast-2#/stacks/create/review?templateURL=https://s3.us-west-2.amazonaws.com/cfn.asserts.ai/tenants/asserts/ec2.yaml\&stackName=Asserts)`` | [Asia Pacific (Seoul)](https://ap-northeast-2.console.aws.amazon.com/cloudformation/home?region=ap-northeast-2#/stacks/create/review?templateURL=https://s3.us-west-2.amazonaws.com/cfn.asserts.ai/tenants/asserts/ec2.yaml\&stackName=Asserts)     |
| ``[`ap-northeast-3`](https://ap-northeast-3.console.aws.amazon.com/cloudformation/home?region=ap-northeast-3#/stacks/create/review?templateURL=https://s3.us-west-2.amazonaws.com/cfn.asserts.ai/tenants/asserts/ec2.yaml\&stackName=Asserts)`` | [Asia Pacific (Osaka)](https://ap-northeast-3.console.aws.amazon.com/cloudformation/home?region=ap-northeast-3#/stacks/create/review?templateURL=https://s3.us-west-2.amazonaws.com/cfn.asserts.ai/tenants/asserts/ec2.yaml\&stackName=Asserts)     |
| ``[`ap-south-1`](https://ap-south-1.console.aws.amazon.com/cloudformation/home?region=ap-south-1#/stacks/create/review?templateURL=https://s3.us-west-2.amazonaws.com/cfn.asserts.ai/tenants/asserts/ec2.yaml\&stackName=Asserts)``             | [Asia Pacific (Mumbai)](https://ap-south-1.console.aws.amazon.com/cloudformation/home?region=ap-south-1#/stacks/create/review?templateURL=https://s3.us-west-2.amazonaws.com/cfn.asserts.ai/tenants/asserts/ec2.yaml\&stackName=Asserts)            |
| ``[`ap-southeast-1`](https://ap-southeast-1.console.aws.amazon.com/cloudformation/home?region=ap-southeast-1#/stacks/create/review?templateURL=https://s3.us-west-2.amazonaws.com/cfn.asserts.ai/tenants/asserts/ec2.yaml\&stackName=Asserts)`` | [Asia Pacific (Singapore)](https://ap-southeast-1.console.aws.amazon.com/cloudformation/home?region=ap-southeast-1#/stacks/create/review?templateURL=https://s3.us-west-2.amazonaws.com/cfn.asserts.ai/tenants/asserts/ec2.yaml\&stackName=Asserts) |
| ``[`ap-southeast-2`](https://ap-southeast-2.console.aws.amazon.com/cloudformation/home?region=ap-southeast-2#/stacks/create/review?templateURL=https://s3.us-west-2.amazonaws.com/cfn.asserts.ai/tenants/asserts/ec2.yaml\&stackName=Asserts)`` | [Asia Pacific (Sydney)](https://ap-southeast-2.console.aws.amazon.com/cloudformation/home?region=ap-southeast-2#/stacks/create/review?templateURL=https://s3.us-west-2.amazonaws.com/cfn.asserts.ai/tenants/asserts/ec2.yaml\&stackName=Asserts)    |
| ``[`ap-southeast-3`](https://ap-southeast-3.console.aws.amazon.com/cloudformation/home?region=ap-southeast-3#/stacks/create/review?templateURL=https://s3.us-west-2.amazonaws.com/cfn.asserts.ai/tenants/asserts/ec2.yaml\&stackName=Asserts)`` | [Asia Pacific (Jakarta)](https://ap-southeast-3.console.aws.amazon.com/cloudformation/home?region=ap-southeast-3#/stacks/create/review?templateURL=https://s3.us-west-2.amazonaws.com/cfn.asserts.ai/tenants/asserts/ec2.yaml\&stackName=Asserts)   |
{% endtab %}

{% tab title="Europe" %}
| ``[`eu-central-1`](https://eu-central-1.console.aws.amazon.com/cloudformation/home?region=eu-central-1#/stacks/create/review?templateURL=https://s3.us-west-2.amazonaws.com/cfn.asserts.ai/tenants/asserts/ec2.yaml\&stackName=Asserts)`` | [Europe (Frankfurt)](https://eu-central-1.console.aws.amazon.com/cloudformation/home?region=eu-central-1#/stacks/create/review?templateURL=https://s3.us-west-2.amazonaws.com/cfn.asserts.ai/tenants/asserts/ec2.yaml\&stackName=Asserts) |
| ----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- | ----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| ``[`eu-north-1`](https://eu-north-1.console.aws.amazon.com/cloudformation/home?region=eu-north-1#/stacks/create/review?templateURL=https://s3.us-west-2.amazonaws.com/cfn.asserts.ai/tenants/asserts/ec2.yaml\&stackName=Asserts)``       | [Europe (Stockholm)](https://eu-north-1.console.aws.amazon.com/cloudformation/home?region=eu-north-1#/stacks/create/review?templateURL=https://s3.us-west-2.amazonaws.com/cfn.asserts.ai/tenants/asserts/ec2.yaml\&stackName=Asserts)     |
| ``[`eu-south-1`](https://eu-south-1.console.aws.amazon.com/cloudformation/home?region=eu-south-1#/stacks/create/review?templateURL=https://s3.us-west-2.amazonaws.com/cfn.asserts.ai/tenants/asserts/ec2.yaml\&stackName=Asserts)``       | [Europe (Milan)](https://eu-south-1.console.aws.amazon.com/cloudformation/home?region=eu-south-1#/stacks/create/review?templateURL=https://s3.us-west-2.amazonaws.com/cfn.asserts.ai/tenants/asserts/ec2.yaml\&stackName=Asserts)         |
| ``[`eu-west-1`](https://eu-west-1.console.aws.amazon.com/cloudformation/home?region=eu-west-1#/stacks/create/review?templateURL=https://s3.us-west-2.amazonaws.com/cfn.asserts.ai/tenants/asserts/ec2.yaml\&stackName=Asserts)``          | [Europe (Ireland)](https://eu-west-1.console.aws.amazon.com/cloudformation/home?region=eu-west-1#/stacks/create/review?templateURL=https://s3.us-west-2.amazonaws.com/cfn.asserts.ai/tenants/asserts/ec2.yaml\&stackName=Asserts)         |
| ``[`eu-west-2`](https://eu-west-2.console.aws.amazon.com/cloudformation/home?region=eu-west-2#/stacks/create/review?templateURL=https://s3.us-west-2.amazonaws.com/cfn.asserts.ai/tenants/asserts/ec2.yaml\&stackName=Asserts)``          | [Europe (London)](https://eu-west-2.console.aws.amazon.com/cloudformation/home?region=eu-west-2#/stacks/create/review?templateURL=https://s3.us-west-2.amazonaws.com/cfn.asserts.ai/tenants/asserts/ec2.yaml\&stackName=Asserts)          |
| ``[`eu-west-3`](https://eu-west-3.console.aws.amazon.com/cloudformation/home?region=eu-west-3#/stacks/create/review?templateURL=https://s3.us-west-2.amazonaws.com/cfn.asserts.ai/tenants/asserts/ec2.yaml\&stackName=Asserts)``          | [Europe (Paris)](https://eu-west-3.console.aws.amazon.com/cloudformation/home?region=eu-west-3#/stacks/create/review?templateURL=https://s3.us-west-2.amazonaws.com/cfn.asserts.ai/tenants/asserts/ec2.yaml\&stackName=Asserts)           |
{% endtab %}

{% tab title="Other" %}
| ``[`af-south-1`](https://af-south-1.console.aws.amazon.com/cloudformation/home?region=af-south-1#/stacks/create/review?templateURL=https://s3.us-west-2.amazonaws.com/cfn.asserts.ai/tenants/asserts/ec2.yaml\&stackName=Asserts)``     | [Africa (Cape Town)](https://af-south-1.console.aws.amazon.com/cloudformation/home?region=af-south-1#/stacks/create/review?templateURL=https://s3.us-west-2.amazonaws.com/cfn.asserts.ai/tenants/asserts/ec2.yaml\&stackName=Asserts)      |
| --------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------ |
| [`ca-central-1`](https://ca-central-1.console.aws.amazon.com/cloudformation/home?region=ca-central-1#/stacks/create/review?templateURL=https://s3.us-west-2.amazonaws.com/cfn.asserts.ai/tenants/asserts/ec2.yaml\&stackName=Asserts)`` | [Canada (Central)](https://ca-central-1.console.aws.amazon.com/cloudformation/home?region=ca-central-1#/stacks/create/review?templateURL=https://s3.us-west-2.amazonaws.com/cfn.asserts.ai/tenants/asserts/ec2.yaml\&stackName=Asserts)    |
| [`me-south-1`](https://me-south-1.console.aws.amazon.com/cloudformation/home?region=me-south-1#/stacks/create/review?templateURL=https://s3.us-west-2.amazonaws.com/cfn.asserts.ai/tenants/asserts/ec2.yaml\&stackName=Asserts)``       | [Middle East (Bahrain)](https://me-south-1.console.aws.amazon.com/cloudformation/home?region=me-south-1#/stacks/create/review?templateURL=https://s3.us-west-2.amazonaws.com/cfn.asserts.ai/tenants/asserts/ec2.yaml\&stackName=Asserts)   |
| [sa-east-1](https://sa-east-1.console.aws.amazon.com/cloudformation/home?region=sa-east-1#/stacks/create/review?templateURL=https://s3.us-west-2.amazonaws.com/cfn.asserts.ai/tenants/asserts/ec2.yaml\&stackName=Asserts)              | [South America (São Paulo)](https://sa-east-1.console.aws.amazon.com/cloudformation/home?region=sa-east-1#/stacks/create/review?templateURL=https://s3.us-west-2.amazonaws.com/cfn.asserts.ai/tenants/asserts/ec2.yaml\&stackName=Asserts) |
{% endtab %}
{% endtabs %}

****

## **Install**

#### Launch Quick Create Stack

1. Determine the AWS region where you want the Cloudformation Stack to be created.
2. In the [Stack _Quick-Create Links_](aws-cloudformation.md#stack-quick-create-links) __ table above, Click the Asserts Quick-Create Stack link for your desired region.

#### **Enter** Stack Options

1. Select a <mark style="color:orange;">**KeyName**</mark>. \
   _Name of an existing EC2 KeyPair to enable SSH access to the instance_
2. Select the target <mark style="color:orange;">**VpcId**</mark>.
3. Select the target <mark style="color:orange;">**SubnetId**</mark>.\
   _The ID of the subnet to launch the EC2 instance into._ \
   __:warning: _Must be a **public** subnet!_

#### Confirm & Create Asserts Stack

1. Check the box at the bottom of the page
   * _I acknowledge that AWS CloudFormation might create IAM resources._
2. _Click the <mark style="color:orange;">**Create stack**</mark> <mark style="color:orange;"></mark><mark style="color:orange;"></mark> button_

#### **See the data!**

1. _Wait for the Asserts Stack to display <mark style="color:green;">**Create\_Complete**</mark>_
2. _Under the stack's Outputs tab, click the **WebsiteUrl**_ \
   _****Y_ou should now be able to view the Asserts UI



## **Upgrade**

#### Go To Stack

1. Navigate to the Cloudformation Stacks in the region where you created the Asserts Stack\
   _For example_ 👉 [us-west-2 > Cloudformation > Stacks](https://us-west-2.console.aws.amazon.com/cloudformation/home)
2. Under the list of **Stack names**, Select the **Asserts** Stack previously created

#### Select & Configure Update

1. Click the **Update** button in top right corner&#x20;
2. For the **Prepare template** option, select [👉](https://s3.us-west-2.amazonaws.com/cfn.asserts.ai/tenants/asserts/ec2.yaml) **Replace current template**
3. For the **Template** **source** option,  select [👉](https://s3.us-west-2.amazonaws.com/cfn.asserts.ai/tenants/asserts/ec2.yaml) **Amazon S3 URL**
4. For the **Amazon S3 URL** enter[ \
   👉 https://s3.us-west-2.amazonaws.com/cfn.asserts.ai/tenants/asserts/ec2.yaml](https://s3.us-west-2.amazonaws.com/cfn.asserts.ai/tenants/asserts/ec2.yaml)\
   Then click <mark style="color:orange;">Next</mark>
5. Update any stack details (Optional)\
   Then click <mark style="color:orange;">Next</mark>
6. Update any <mark style="color:orange;"></mark> stack options  (Optional)\
   Then click <mark style="color:orange;">Next</mark>

#### Confirm & Update Asserts Stack

1. Check the box at the bottom of the page\
   &#x20;\[ ]  _I acknowledge that AWS CloudFormation might create IAM resources._
2. _Click the <mark style="color:orange;">Update stack</mark> button_



## Uninstall

#### Go to Stack

1. Navigate to the Cloudformation Stacks in the region where you created the Asserts Stack\
   _For example_ 👉 [us-west-2 > Cloudformation > Stacks](https://us-west-2.console.aws.amazon.com/cloudformation/home)
2. Under the list of **Stack names**, Select the **Asserts** Stack previously created

#### Select Delete

1. Click the **Delete** button in top right corner

#### Confirm Delete

1. In the confirmation pop-up window, Click <mark style="color:orange;">**Delete Stack**</mark>

<mark style="color:orange;">****</mark>

## [Need Help](../../#before-you-begin)

