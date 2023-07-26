# Add Account

For Asserts to start observing your AWS Infrastructure, you need to add the AWS Account as a datasource. Once the account is added, the exporter will retrieve the account details and start observing AWS resources in the account. Before you add the account, you need to create an IAM role in your account which grants permissions to Asserts Server. This role can be created using [this CloudFormation template](https://s3.us-west-2.amazonaws.com/downloads.asserts.ai/aws-integration/ecs/v3/iam-role-with-trust-relationship.yaml). While creating this CloudFormation stack, you need to specify the role which is assigned to the EC2 instance on which Asserts Server is deployed.

<figure><img src="../../../.gitbook/assets/image (1).png" alt=""><figcaption><p>Add AWS Account after installing the exporter</p></figcaption></figure>

In the screenshot above, there is a link to a CloudFormation template to install the AWS Exporter as an ECS Service. While it is also possible to install the AWS Exporter as an ECS Service, this is not the recommended option. Please use the [CloudFormation template](https://s3.us-west-2.amazonaws.com/downloads.asserts.ai/aws-integration/ecs/v3/iam-role-with-trust-relationship.yaml) to create a role and specify the ARN of that role as the `Assume Role ARN`
