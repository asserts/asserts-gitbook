# Add Account

For Asserts to start observing your AWS Infrastructure, you need to add the AWS Account as a datasource. Once the account is added, the exporter will retrieve the account details and start observing AWS resources in the account. Before you add the account, you need to create an IAM role in your account which grants permissions to Asserts Server. This role can be created using [this CloudFormation template](https://s3.us-west-2.amazonaws.com/downloads.asserts.ai/aws-integration/ecs/v3/iam-role-with-trust-relationship.yaml). While creating this CloudFormation stack, you need to specify the role which is assigned to the EC2 instance on which Asserts Server is deployed.

<figure><img src="../../../.gitbook/assets/image (1) (1) (1) (1).png" alt=""><figcaption><p>Add an AWS Account after installing the exporter</p></figcaption></figure>

Once the role has been created, specify its ARN as the `Assume Role ARN` and add the account.
