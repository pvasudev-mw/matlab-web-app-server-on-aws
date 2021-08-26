# MATLAB Web App Server on Amazon Web Services

# Requirements

Before starting, you need the following:

-   A MATLAB® Web App Server™ license. For more information, see [Configure MATLAB Web App Server Licensing on the Cloud](http://tinyurl.mathworks.com/8bfHUFTY). To configure a license for use on the cloud, you need the MAC address of the network license manager on the cloud. For more information, see [Get License Server MAC Address](#get-license-server-mac-address).

    <mark> For UX testing, use the following license file:(include link here) </mark>
-   An Amazon Web Services™ (AWS) account with an IAM user identity.
-   A Key Pair for your AWS account in the US East (N. Virginia), EU (Ireland) or Asia Pacific (Tokyo) region. For more information, see [Amazon EC2 Key Pairs](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/ec2-key-pairs.html).

# Costs
You are responsible for the cost of the AWS services used when you create cloud resources using this guide. Resource settings, such as instance type, will affect the cost of deployment. For cost estimates, see the pricing pages for each AWS service you will be using. Prices are subject to change.


# Introduction
The following guide will help you automate the process of running MATLAB
Web App Server on the Amazon Web Services (AWS) Cloud. The automation is
accomplished using an AWS CloudFormation template. The template is a JSON
file that defines the resources needed to deploy and manage MATLAB Web App
Server on AWS.
For information about the architecture of this solution, see [Architecture and Resources](#architecture-and-resources). For information about AWS templates, see [AWS CloudFormation Templates](https://docs.aws.amazon.com/AWSCloudFormation/latest/UserGuide/template-guide.html). <br>

The default MATLAB Web App Server deployment template deploys a network license manager to manage MATLAB Web App Server licenses. 

The template for using an existing VPC for deployment provides an option to either deploy a network license manager or use a network license manager that has already been deployed. For details, see [How Do I Use An Existing VPC to Deploy MATLAB Web App Server?](#how-do-i-use-an-existing-vpc-to-deploy-matlab-web-app-server).

# Prepare Your AWS Account
1. If you do not have an AWS account, create one at https://aws.amazon.com by following the on-screen instructions.
2. Use the regions selector in the navigation bar to choose **US-EAST (N. Virginia)**, **EU (Ireland)** or **Asia Pacific (Tokyo)**, as the region where you want to deploy MATLAB Web App Server.
3. Create a [key pair](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/ec2-key-pairs.html) in that region.  The key pair is necessary as it is the only way to connect to the instance as an administrator.
4. If necessary, [request a service limit increase](https://console.aws.amazon.com/support/home#/case/create?issueType=service-limit-increase&limitType=service-code-) for the Amazon EC2 instance type or VPCs.  You might need to do this if you already have existing deployments that use that instance type or you think you might exceed the [default limit](http://docs.aws.amazon.com/AWSEC2/latest/UserGuide/ec2-resource-limits.html) with this deployment.

# Deployment Steps

## Step 1. Launch the Template
Click the **Launch Stack** button to deploy resources on AWS. This will open the AWS Management Console in your web browser.

| Release | Windows Server 2019 or Ubuntu 18.04  |
|---------------|------------------------|
| MATLAB R2021a | <a href="https://us-east-1.console.aws.amazon.com/cloudformation/home?region=us-east-1#/stacks/quickcreate?templateURL=https://joeywebapplambdaarchive.s3.amazonaws.com/WebAppServer_new.yml" target="_blank">     <img src="https://s3.amazonaws.com/cloudformation-examples/cloudformation-launch-stack.png"/> </a> |

<!--For other releases, see [How do I launch a template that uses a previous MATLAB release?](#how-do-i-launch-a-template-that-uses-a-previous-matlab-release)-->
<p><strong>Note:</strong> Creating a stack on AWS can take at least 20 minutes.</p>

## Step 2. Configure the Stack
1. Provide values for parameters in the **Create Stack** page:

    | Parameter Name                         | Value                                                                                                                                                                                                                                                                                                                                                 |
    |----------------------------------------|-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
    | **Stack name**                         | Choose a name for the stack. This will be shown in the AWS console. <p><em>*Example*</em>: `Boston`</p>                                                                                                                                                                                                                                                                       |
    | |**Settings for Remote Access to EC2 Instance Hosting MATLAB Web App Server**|
    | **Name of Existing Amazon EC2 Key Pair**          | Choose an existing Amazon EC2 key pair to connect to the EC2 instance hosting MATLAB Web App Server. For information about creating an Amazon EC2 key pair, see [Amazon EC2 Key Pairs](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/ec2-key-pairs.html#having-ec2-create-your-key-pair). <p><em>*Example*</em>: `boston-keypair`</p>                                                                                   |
    | **IP Addresses Allowed to Remotely Connect to EC2 Instance and Administer MATLAB Web App Server** | Specify a single IP address or an IP address range that can remotely connect to the EC2 instance that hosts the MATLAB Web App Server and administer it. You still need login credentials to modify any server configurations. The format for this field is IP Address/Mask. <p><em>Example</em>: </p><p><em>Single IP Address [ 10.0.0.1 ]:</em> `10.0.0.1/32` </p><p><em>Range of IP Addresses [ 10.0.0.0 to 10.0.255.255 ]:</em> `10.0.0.1/16` <ul><li>This is the public IP address which can be found by searching for "what is my ip address" on the web. The mask determines the number of IP addresses to include.</li><li>A mask of 32 is a single IP address.</li><li>Use a [CIDR calculator](https://www.ipaddressguide.com/cidr) if you need a range of more than one IP addresses.</li><li>You may need to contact your IT administrator to determine which address is appropriate.</li></ul></p> |
    ||**Settings to Access to MATLAB Web App Server Home Page**||
    | **Use the Same IP Addresses to Access MATLAB Web App Server Apps Home Page**| Select, **Yes** or **No**. <ul><li>If you select **Yes**, the same IP address range specified above is configured to access the MATLAB Web App Server apps homepage.</li><li>If you select **No**, you must specify a new IP address range in the next field.</li></ul>
    | **IP Addresses Allowed to Access MATLAB Web App Server Apps Home Page** | Complete this field only if you selected "No" in the previous field. Specify the IP address range that can connect to MATLAB Web App Server. Specify the range in CIDR notation in the format IP Address/Mask. <p><em>*Example*</em>: `10.0.0.1/24`</p> |
    | **ARN of SSL Certificate** | Specify the Amazon Resource Name (ARN) of the SSL certificate you uploaded to the AWS Certificate Manager. The ARN facilitates connecting to the apps home page using an HTTPS connection.<p><em>*Example*</em>: <code>arn:aws:acm:us-east-1:012345678910:certificate/666abcd6-ab6c-6ab6-a666-a666666bcd66</code> </p><p>To retrieve an ARN:</p><ul><li>Type "Certificate Manager" in the search box at the top of the web page and hit Enter. This automatically takes you to the AWS Certificate Manager.</li><li>Expand the entry for the certificate you uploaded.</li><li>Copy the ARN from the "Details" section.</li></ul><p>For more information, see [Create Self-signed Certificate](/README.md#create-self-signed-certificate) and [Upload Self-signed Certificate to AWS Certificate Manager](/README.md#upload-self-signed-certificate-to-aws-certificate-manager).
    ||**Settings for EC2 Instance Hosting MATLAB Web App Server**|
    | **EC2 Instance Type** | Choose the AWS EC2 instance type to use for the server. All AWS instance types are supported. For more information, see [Amazon EC2 Instance Types](https://aws.amazon.com/ec2/instance-types/). <p><em>*Example*</em>: `m5.xlarge`</p> |
    | **Operating System** | Choose between Windows (Windows Server) and Linux(Ubuntu).  |
    ||**Settings for Network License Manager**|
    | **Password for Network License Manager** | Specify a password for the network license manager. Use this password to log in to the EC2 instance hosting the network license manager after the stack has been successfully created.<p>Deploying MATLAB Web App Server automatically deploys a network license manager.</p>|
    | **Confirm Password** | Reenter the password to log in to the network license manager. |

    >**Note**: Make sure you select US East (N.Virginia), EU (Ireland) or Asia Pacific (Tokyo) as your region from the navigation panel on top. Currently, US East, EU (Ireland), and Asia Pacific (Tokyo) are the only supported regions.

2. Tick the box to accept that the template uses IAM roles. For more information about IAM, see [IAM FAQ](https://aws.amazon.com/iam/faqs). 
  
3. Click the **Create** button. The CloudFormation service starts creating resources for the stack.
>**Note**: Clicking **Create** takes you to the *Stack Detail* page for your stack. Wait for the Status to reach **CREATE\_COMPLETE**. This can take up to 20 minutes.

## Step 3. Upload License File
1. Click **Outputs** in the *Stack details* for your stack.
1. Look for the key named `MATLABWebAppServerLicenseManager` and click the corresponding URL listed under value. This will take you to Network License Manager for MATLAB Dashboard login page.
1. The username is **manager**. For the password, enter the password you entered in the **Password for Network License Manager** field while creating the stack in [Step 2](#step-2-configure-the-stack).
1. Follow the instructions on the home page of the network license manager to upload your MATLAB Web App Server license.

<mark> For UX testing: Click **Administration** > **Manage License** and upload the license file. </mark>

>Note: MATLAB Web App Server automatically starts after successfully uploading a valid license file.

## Step 4. Open the MATLAB Web App Server Apps Home Page
1. In the *Stack details* for your stack, click the **Outputs** tab.
1. Look for the key named `MATLABWebAppServerAppsHomePage` and click the corresponding URL listed under value. This opens the apps home page.

You are now ready to use MATLAB Web App Server on AWS. 

To run applications on MATLAB Web App Server, you need to create web apps using MATLAB Compiler. For more information, see [Web Apps](https://www.mathworks.com/help/compiler/webapps/create-and-deploy-a-web-app.html) in the MATLAB Compiler product documentation.

# Common Tasks
## Upload Web Apps to AWS S3 Bucket
1. In the AWS management console, select the stack that you deployed. 
1. In the *Stack details* for your stack, click the **Outputs** tab.
1. Look for the key named `MATLABWebAppServerAppsS3Bucket`, and click the corresponding URL listed under value.
1. In the S3 console, click **apps**.
1. Click **Upload** > **Add Files** to select and upload web apps (`.ctf` files).
>**NOTE:** If you enable OIDC authentication, you can upload web apps from the apps home page. Any apps you upload via the apps home page are not synchronized with S3 bucket. To enable OIDC authentication, see [Configure OIDC Authentication](#configure-oidc-authentication).

## Get Password to EC2 Instance Hosting MATLAB Web App Server
1. In the AWS management console, select the stack you deployed. 
1. In the Stack Detail for your stack, click the **Outputs** tab.
1. Look for the key named `MATLABWebAppServerEC2Instance` and click the corresponding URL listed under value. This will take you to the server instance (`matlab-webapp-server-vm`) page. 
1. Click the **Connect** button at the top.
1. In the *Connect to instance* dialog, choose **Get Password**.
1. Click **Choose File** to navigate and select the private key file (`.pem` file) for the key pair that you used while creating the stack in [Step 2](#step-2-configure-the-stack).
1. Click **Decrypt Password**. The console displays the password for the instance in the *Connect to instance* dialog.
1. Copy the password to the clipboard.

## Connect to EC2 Instance Hosting MATLAB Web App Server Using Remote Desktop
1. In the AWS management console, select the stack you deployed. 
1. In the *Stack details* for your stack, click the **Outputs** tab.
1. Look for the key named `MATLABWebAppServerEC2Instance` and click the corresponding URL listed under value. This will take you to the server instance (`matlab-webapp-server-vm`) page. 
1. Click the value under Instance ID to view the instance summary. 
1. Click the **Connect** button at the top.
1. In the *Connect to instance* dialog, click  the **RDP client** tab.
1. Click the **Download remote desktop file** button to download a .rdp file.
1. Use the .rdp file to remotely connect to EC2 instance using the following credentials:
* Username: Administrator
* Password: The decrypted password. For details, see [Get Password to EC2 Instance](#get-password-to-ec2-instance).

## Connect to EC2 Instance Hosting MATLAB Web App Server Using SSH
1. In the AWS management console, select the stack you deployed. 
1. In the *Stack details* for your stack, click the **Outputs** tab.
1. Look for the key named `MATLABWebAppServerEC2Instance` and click the corresponding URL listed under value. This will take you to the server instance (`matlab-webapp-server-vm`) page. 
1. Click the value under Instance ID to view the instance summary. 
1. Click the **Connect** button at the top.
1. In the *Connect to instance* dialog, click  the **SSH client** tab.
1. Follow the instructions on the page to SSH to the EC2 instance.

## Configure OIDC Authentication
1. Connect to the EC2 instance hosting MATLAB Web App Server. For details, see:
    * [Connect to EC2 Instance Hosting MATLAB Web App Server Using Remote Desktop](#connect-to-ec2-instance-hosting-matlab-web-app-server-using-remote-desktop)
    * [Connect to EC2 Instance Hosting MATLAB Web App Server Using SSH](#connect-to-ec2-instance-hosting-matlab-web-app-server-using-ssh).
1. Follow the instructions on the [Authentication](https://www.mathworks.com/help/webappserver/ug/authentication.html) page in the MathWorks documentation.
    >**NOTE:** SSL is enabled when you deploy the stack.   
1. For the `redirectUrl`, use the URL created as part of your stack.
    * In the AWS management console, select the stack you deployed. 
    * In the *Stack details* for your stack, click the **Outputs** tab.
    * Look for the key named `MATLABWebAppServerOIDCRedirectUrl` and copy the corresponding URL listed under value.
    * Use this URL in the `webapps_authn.json` file.

## Create Self-signed Certificate
For information on creating a self-signed certificate, see [Create and Sign an X509 Certificate](https://docs.aws.amazon.com/elasticbeanstalk/latest/dg/configuring-https-ssl.html).

## Upload Self-signed Certificate to AWS Certificate Manager

1. Open the AWS Certificate Manager.
2. Click the button at the top of the page to **Import a certificate**.
3. Copy the contents of the `.crt` file containing the certificate into the field labeled **Certificate body**.
4. Copy the contents of the `.pem` file containing the private key into the field labeled **Certificate private key**.
5. Leave the field labeled **Certificate chain** blank.
6. Click the button labeled **Review and import**.
7. Review the settings and click the **Import** button.
8. Copy the value of the Amazon Resource Name (ARN) field from the **Details** section of the certificate.

The ARN value that you copied should be pasted into the **ARN of SSL Certificate** parameter of the template in [Step 2](#step-2-configure-the-stack).

## View Logs
Logs are available in AWS CloudWatch. 
1. In the AWS management console, select the stack you deployed. 
1. In the *Stack details* for your stack, click the **Outputs** tab.
1. To view logs related to the cloud console and the MATLAB Web App Server workers, look for the key named `MATLABWebAppServerLogGroup`, and click the corresponding URL listed under value.

## Get Network License Manager MAC Address
1. In the AWS management console, select the stack that you deployed. 
1. In the *Stack details* for your stack, click the **Outputs** tab.
1. Look for the key named `MATLABWebAppServerLicenseManager`, and click the corresponding URL listed under value.
1. Log in to the Network License Manager for MATLAB dashboard using the following credentials:<br>
Username: **manager**<br>
Password: Enter the password you provided while creating the stack.
1. Click **Administration** > **Manage License**.
1. Copy the license server MAC address displayed at the top.

When you deploy the MATLAB Web App Server reference architecture a network license manager is automatically deployed. You can also use an existing license manager that is located in the same VPC and the security group of the MATLAB Web App Server EC2 instance. For detials, see [Network License Manager for MATLAB](https://github.com/mathworks-ref-arch/license-manager-for-matlab-on-aws).

>**NOTE**: The license manager MAC address is available only after the stack creation is complete.

## Delete Your Stack
To delete the stack:
1. Log in to the AWS Console.
3. Go to the CloudFormation page and select the stack you created.
3. Click **Delete**.

# FAQ

## How do I use an existing VPC to deploy MATLAB Web App Server?

Use the following templates to launch the reference architecture within an existing VPC and subnet. The templates provide an option to deploy the Network License Manager for MATLAB to manage MATLAB Web App Server licenses. The license manager must be in the same VPC and security group as MATLAB Web App Server.

| Release | Windows Server 2019 or Ubuntu 18.04 VM |
|---------|---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| R2021a | <a  href ="https://us-east-1.console.aws.amazon.com/cloudformation/home?region=us-east-1#/stacks/create/review?templateURL=https://matlab-Web App-server-templates.s3.amazonaws.com/r2020b_mps_refarch/MatlabWeb AppServer_Existing.yml"  target ="_blank" >      <img  src ="https://s3.amazonaws.com/cloudformation-examples/cloudformation-launch-stack.png" />  </a> |

In addition to the parameters specified in the section [Configure the Stack](#step-2-configure-the-stack), you will need to specify the following parameters in the template to use your existing VPC.

| Parameter  | Value |
|----------------------------------|--------------------------------------------------------------------------------|
| Existing VPC ID | ID of your existing VPC. |
| IP address range of existing VPC | IP address range from the existing VPC. To find the IP address range: <ol><li>Log in to the AWS Console.</li><li>Navigate to the VPC dashboard and select your VPC.</li><li>Click the **CIDR blocks** tab.</li><li>The **IPv4 CIDR Blocks** gives the IP address range.</li></ol> |
| Subnet 1 ID | ID of an existing subnet that will host the cloud console and other resources. |
| Subnet 2 ID | ID of an existing subnet that will host the application gateway. |

- If Subnet 1 and Subnet 2 are public, then you must connect the EC2 VPC endpoint and the AutoScaling VPC endpoint to the VPC.
- If Subnet 1 and Subnet 2 are private, then you must either deploy a NAT gateway in the VPC, or connect all of the following endpoints to the VPC:
    - EC2 VPC endpoint
    - AutoScaling VPC endpoint
    - S3 VPC endpoint
    - CloudFormation endpoint 

For more information about creating endpoints, see [AWS documentation](https://docs.aws.amazon.com/vpc/latest/userguide/vpce-interface.html#create-interface-endpoint).

You will also need to open the following ports in your VPC:

| Port | Description |
|------------------|----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| `443` | Required for communicating with the cloud console. |
| `8000`, `8002`, `9910` | Required for communication between the cloud console and workers within the VPC.  These ports do not need to be open to the internet. |
| `27000`, `50115` | Required for communication between the network license manager and the workers. |
| `3389` | Required for Remote Desktop functionality. This can be used for troubleshooting and debugging. |

### How to use an existing license server in an existing VPC?
If you want to use an exisiting license server, select `No` for the *Deploy License Server* step of the deployment.

To use an existing license server, you must add the security group of the server VMs to the security group of the license server.
1. In the AWS management console, select the stack that you deployed. 
1. In the *Stack details* for your stack, click **Resources**.
1. Look for the **Logical ID** named ```SecurityGroup``` and click the corresponding URL listed under **Physical ID**. This will take you to the security group details.
1. Click the **Inbound Rules** tab, then click **Edit Inbound Rules**.
1. Click **Add Rule**.
1. In the **Type** dropdown, select ```All TCP```.
1. In the **Source**, search and add the ```matlab-Web App-server-cloud-stack-elb-1-sg``` and ```matlab-Web App-server-cloud-stack-elb-2-sg``` security groups. 
1. Click **Save Rules**.

You must also add the private IP address of the license server to the `--license` property in the server configuration file. 
Find the IP address of the license server from the AWS management console.
1. In the AWS management console, navigate to the EC2 dashboard. 
1. Select the license server instance.
1. In the instance details, copy the value of **Private IPs**. For example, 172.30.1.126
1. Add the private IP to the `--license` property. For example, `--license 27000@172.30.1.126`. For more information about editing the server configuration, see [Edit the Server Configuration](/releases/R2020b/doc/cloudConsoleDoc.md#edit-the-server-configuration). 

## What versions of MATLAB Runtime are supported?

| Release | MATLAB Runtime | MATLAB Runtime | MATLAB Runtime | MATLAB Runtime |  
|---------------|----------------|----------------|----------------|----------------|
| MATLAB R2021a |  R2019b | R2020a | R2020b |  R2021a |  

# Enhancement Request
Provide suggestions for additional features or capabilities using the following link: https://www.mathworks.com/cloud/enhancement-request.html

# Technical Support
If you require assistance or have a request for additional features or capabilities, please contact [MathWorks Technical Support](https://www.mathworks.com/support/contact_us.html).
