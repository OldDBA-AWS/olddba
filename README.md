<!-- https://www.markdownguide.org/basic-syntax/ -->
# Guidance for Maximum Data Availability Architecture on AWS
## (aka "MD2A" and "Rananeeti")

## Table of Content
1. [Overview](#overview)
    - [Cost](#cost)
2. [Background](#background)
3. [Cafe Demo App deployment process](#cafe-demo-app-deployment-process)
    - [Prepare the account](#prepare-the-account)
    - [Deploy Web and App Infrastructure](#deploy-web-and-app-infrastructure)
4. [Deployment Validation](#deployment-validation)
5. [Running the Guidance](#running-the-guidance)
    - [Supported Media Files](#supported-media-files)
6. [Next Steps](#next-steps)
7. [Cleanup](#cleanup)
8. [Notices](#notices)
9. [Authors](#authors)

## Overview
MD2A uses Aurora Global and Dynamo databases, Intelligent-Tiering S3 storage, global traffic management, application firewall and balancing infrastructure. These services, via their own programming APIs and SDKs, get engaged as necessary by our new _“Rananeeti”_ Data Platform which can deliver the _"Full Stack Resiliency"_, working with Application _in its entirety_, covering User Interface, Services and Database layers, as presented in the [Reference Architecture](#ArchDiag) diagram.

### What are we deploying?
The main idea is to _make your application resilient to database failures_.
This is how it looks like for our Cafe Demo Application:
<!-- ![name](link to image on GH)(link to your URL) -->
<img src="assets/CafeAppFullResiliencyToDBFailuresDemo.jpeg" alt="Cafe App Full Resiliency Example" width=400px>
The video, detailed explanations and build guide follow.

### Architecture Diagram
<a name="ArchDiag"></a>
The fully deployed production grade system have structure, similar to this:
<img src="assets/reference_architecture.png" alt="reference_architecture" width=800px>
Aurora Global Database and Amazon DynamoDB are powerful tools that can significantly enhance operational excellence by 1/ _replicating data_ across multiple Availability Zones and _Regions_; 2/ maintaining  continuous operations and _minimizing downtime_; 3/ _automating_ many database management tasks, such as backups, patching, and failover; 4/ with its high-performance storage engine, Aurora Global Database delivers low-latency performance, improving application responsiveness and providing _cross-regional availability_ at the same time; 5/ DynamoDB is a _fully managed_ NoSQL database that eliminates the need for database administration; 6/ DynamoDB _Global Tables_ enable low-latency access to data from multiple regions, which is ideal for applications with _global users_ and improves user experience.

### Cost
You are responsible for the cost of the AWS services used while running this Guidance. As of December 2024 _my cost_ of running this Guidance in the _us-east-1_ region is approximately _$55_ per month. \
<img src="assets/cost_nonrepresentative_example.jpeg" alt="One of possible Cafe App price points" width=200px> \
_Your cost may and will vary_.

## Background
You can read about *Application-level Resiliency* on 
[my LinkedIn page](https://www.linkedin.com/in/denys-dobrelya/):
- [Working Demo Application walkthrough](https://www.linkedin.com/posts/activity-7222454561465073664-metD), full resolution video is available upon request
- [Reference Architecture](#ArchDiag) diagram, describing all moving parts
- Main ideas behind "*Maximum Data Availability Architecture*" (MD2A):
   - [Building Resilient Applications: Leveraging Modern Databases for High Availability](https://www.linkedin.com/pulse/building-resilient-applications-leveraging-modern-high-denys-dobrelya-pcpqf)
   - [Building for the Future: Why Your Applications Need a Data Platform Foundation](https://www.linkedin.com/pulse/building-future-why-your-applications-need-data-denys-dobrelya-uzhaf)
   - [Business Summary: Deploying Aurora as a Global Cross-Region Database](https://www.linkedin.com/pulse/business-summary-deploying-aurora-global-cross-region-denys-dobrelya-o4wpf)
   - [Multi-Region Resilient Application Recipe](https://www.linkedin.com/pulse/multi-region-resilient-application-recipe-denys-dobrelya-cwjrf)
   - [Building a Cloud Fortress](https://www.linkedin.com/pulse/building-cloud-fortress-denys-dobrelya-b30wf)
- [Fully functional Cafe Demo website](https://cafe.olddba.people.aws.dev) is still available,
but we were asked to restrict public access. If you want to try it - let me know! 
(_It looks and works exactly like in my video above._)

## Cafe Demo App deployment process
By leveraging cloud computing and AWS managed services, _“Rananeeti” can reduce its carbon footprint_ significantly. AWS data centers are highly energy-efficient, utilizing advanced cooling technologies and renewable energy sources. AWS is committed to sustainable practices, such as reducing waste and optimizing resource usage.

### Prepare the account
-  Get new AWS Account
   So you don't mess up your things at work!
-  Use N.Virginia "us-east-1" region
   You may use any region you wish, but then you need to change the default value of CloudFormation parameter "HostAMI" 
-  Create a KeyPair and name it "cafe-keypair" or provide your own name as CloudFormation parameter "CafeKey"
-  Run the CloudFormation (CF from now) and deploy the stack "Cafe"
   Use file "deployment/Cafe_template.yaml"
   Aurora DB creation takes the longest time - be patient for about 15 min.
   Once everything is ready read the "Output" section and add your own IP to the Security Group.
- Connect to EC2 via ssh using your KeyPair.
   This is your Application server and all Flask App code will be deployed here.
   It is already configured to connect to empty Aurora PG database, just run "psql" from command line.
   This Git Repo code had already been cloned into "/home/ec2-user/md2a/rananeeti/olddba".
   Go there.
- Now we need to deploy [this target stack](https://www.linkedin.com/pulse/building-resilient-applications-leveraging-modern-high-denys-dobrelya-pcpqf).
<img src="assets/CafeAppStackMapping.png" alt="Cafe App Full Resiliency Stack Mapping" width=800px>
This image "maps" old (but not useless!) "legacy" Enterprise world with newer lightweight approach.

### Deploy Web and App Infrastructure
As "ec2-user" install necessary Python modules:
- $ pip3 install boto3 oauthlib Flask Flask-SQLAlchemy Flask-OAuthlib Gunicorn psycopg2-binary
Configure AWS CLI credentials.
The recommended practice is to use [IAM Identity Center authentication](https://docs.aws.amazon.com/cli/latest/userguide/cli-configure-sso.html).
        - $ cd ~
        - $ aws configure
        AWS Access Key ID [None]: Key-from-your-IAM-User
        AWS Secret Access Key [None]: Secret-from-your-IAM-User
        Default region name [None]: us-east-1
        Default output format [None]: text
        $ aws s3 ls
        2024-11-26 05:14:32 cf-templates-1j84y89c4zr7w-ap-southeast-2
        2024-11-26 06:42:48 cf-templates-1j84y89c4zr7w-us-east-1




## Cleanup
### Delete Stack
To clean up environment, AWS resources can be deleted using the CDK or CloudFormation. With CDK, run the `cdk destroy` command to delete the resources. With CloudFormation, you can go to the CloudFormation stack and click `Delete`
### Manually delete retained resources
After deleting the stack, there will be some resources that will be retained. You will need to manually delete these resources.
- Amazon S3 buckets will be retained:
  - `InputBucket`
  - `OutputBucket`
  - `TranscriptionBucket`
  - `LoggingBucket`
- Amazon Elastic Container Registry (ECR) will be retained:
  - `json2word_repo`
  - `exif_tool_repo`
  - `encoder_repo`
- In the EC2 Image Builder service, 3 container recipes and 1 infrastructure configurations will be retained.
  - Container recipes:
    - `json2word_recipe`
    - `exif_tool_recipe`
    - `encoder_recipe`
  - Infrastructure configurations:
    - `InfrastructureConfigurationContainerStack`



## Notices

**External Library Notice:**
*An external FFmpeg binary will be pulled from [https://www.johnvansickle.com](https://www.johnvansickle.com) for the [Encoder Dockerfile](deployment/media-management-solution-cdk/media_management_solutions_library/container_assets/EncoderRecipe/) container. It is the responsibility of the customer to abide by the licensing of FFmpeg.*
*An external exif binary will be pulled from [exiftool.org](https://exiftool.org/)
[https://github.com/exiftool/exiftool](https://github.com/exiftool/exiftool) for the [ExifTool Dockerfile](deployment/media-management-solution-cdk/media_management_solutions_library/container_assets/ExifToolRecipe) container. It is the responsibility of the customer to abide by the licensing of [ExifTool](https://github.com/exiftool/exiftool).*

**Legal Disclaimer:**
*Customers are responsible for making their own independent assessment of the information in this Guidance. This Guidance: (a) is for informational purposes only, (b) represents AWS current product offerings and practices, which are subject to change without notice, and (c) does not create any commitments or assurances from AWS and its affiliates, suppliers or licensors. AWS products or services are provided “as is” without warranties, representations, or conditions of any kind, whether express or implied. AWS responsibilities and liabilities to its customers are controlled by AWS agreements, and this Guidance is not part of, nor does it modify, any agreement between AWS and its customers.*


## Authors and Contributors

- John Meyer - Salesforce Solutions Engineer (Retired)
- John Sautter - Salesforce Senior Director of Solutions Engineering
- Kyle Hart - AWS Principal Solutions Architect
- Christian Ramirez - AWS Partner Solutions Architect
- Jared Wiener - AWS Senior Solutions Architect
- Kishore Dhamodaran - AWS Senior Solutions Architect
- Robert Sosinski - AWS Principal Solutions Architect
- Deepika Suresh - AWS Solutions Architect Technology Solutions
- Jason Wreath - AWS Head of Solutions for AIML & Data
