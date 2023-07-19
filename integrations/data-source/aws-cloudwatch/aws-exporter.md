# AWS Exporter

#### Installing the Asserts AWS Exporter

Asserts provides a CloudFormation template to install the exporter. The link, **install AWS Exporter** as shown in the image below will launch the CloudFormation template to install the AWS Exporter.

<figure><img src="../../../.gitbook/assets/image (1).png" alt=""><figcaption><p>Add AWS Account after installing the exporter</p></figcaption></figure>

The following inputs would need to be provided -

**VPC and Subnets**

1. **VPC Id** The VPC in which the Asserts AWS Exporter ECS Service needs to be installed.&#x20;
2. **VPC CIDR** The CIDR of the VPC
3. **Subnets** The subnets for the service instances. At least 2 subnets in different availability zones need to be provided

**ECS Cluster and Log Group Settings**

1. **ECS Cluster Name** The ECS Cluster name under which the asserts exporter services will be created. Defaults to **asserts-aws-integration**
2. **Log Group Name** The CloudWatch log group name under which the logs for the services will be stored. Defaults to **asserts-aws-integration**
3. **Log Group Retention Period** The log retention. Defaults to **7 days**
4. **Enable Container Insights** This is an ECS Cluster level setting that will determine if ECS resource utilization metrics need to be captured at the container level. This defaults to **enabled**. This has cost implications so if cost is a concern, it can be set to **disabled**

**Asserts TSDB and API Server Credentials**

1. **Asserts Tenant Name** If your asserts application URL is https://acme.app.asserts.ai, your tenant name is **acme**
2. **Asserts Remote Write URL** For e.g. https://acme.tsdb.asserts.ai
3. **Asserts Remote Write Password** The Asserts remote write password
4. **Asserts API Server URL** For e.g. https://acme.app.asserts.ai
5. **Asserts API Server API User Name** An API key can be created in Asserts. In the Asserts product, Go to your Profile -> Settings -> Credentials and create a new credential. Copy the **Id** and **Secret** for use later. Specify the **Id** as the User name
6. **Asserts API Server API Password** Specify the **Secret** as the password

The CloudFormation template creates an API key to allow the Asserts server to read CloudWatch metrics from this account. Use the API Key and Credentials and add the account
