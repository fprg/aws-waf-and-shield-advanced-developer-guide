# Step 5: Detect and Filter Malicious Web Requests Using AWS WAF<a name="tutorials-ddos-cross-service-WAF"></a>

You can use a web application firewall \(WAF\) to protect your web applications against attacks that attempt to exploit a vulnerability in your website\. Common examples include SQL injection or cross\-site request forgery\. You can also use a firewall to detect and mitigate web application layer DDoS attacks\. 

AWS WAF is a web application firewall service that lets you monitor the HTTP and HTTPS requests that are forwarded to Amazon CloudFront or an Application Load Balancer\. AWS WAF also lets you control access to your content\. Based on conditions that you specify, such as the IP addresses that requests originate from or the values of query strings, CloudFront responds to the requests either with the requested content or with an HTTP 403 status code \(Forbidden\)\. 

Some attacks consist of web traffic that is disguised to look like regular user traffic\. To mitigate this type of attack, you can use AWS WAF rate\-based blacklisting\. With rate\-based blacklisting, you can set a threshold for how many requests your web application can serve\. If a bot or crawler exceeds this limit, you can use AWS WAF to automatically block any additional requests\.

AWS provides preconfigured templates that include a set of AWS WAF rules, which you can customize to best fit your needs\. These templates are designed to block common web\-based attacks such as bad bots, [SQL injection](https://en.wikipedia.org/wiki/SQL_injection), [cross\-site scripting \(XSS\)](https://en.wikipedia.org/wiki/Cross-site_scripting), [HTTP floods](https://en.wikipedia.org/wiki/HTTP_Flood), and known\-attacker attacks\. This tutorial uses these templates to provide firewall protection for your website\. The following procedures show you how to deploy the templates using AWS CloudFormation\. For more information, including an diagram of the template's solution, see [AWS WAF Security Automations](https://aws.amazon.com/answers/security/aws-waf-security-automations/)\. 

The template uses some AWS features, such as AWS Lambda and Amazon API Gateway, that are not covered in this tutorial\. The template performs all the necessary configuration, so you don't need to perform any additional actions for those services\. However, if you want to learn more about Lambda and Amazon API Gateway, see the [AWS Lambda Developer Guide](http://docs.aws.amazon.com/lambda/latest/dg/) and [Amazon API Gateway Developer Guide](http://docs.aws.amazon.com/apigateway/latest/developerguide/)\.

**Important**  
You are responsible for the cost of all the AWS services that are deployed as part of this template, including Amazon S3, AWS Lambda, Amazon API Gateway, AWS WAF, and others\. For full details, see the pricing page for each AWS service\.


+ [Launch the Stack \(Template\)](#tutorials-ddos-cross-service-waf-launch)
+ [Associate the Web ACL with Your Web Application](#tutorials-ddos-cross-service-waf-associate)
+ [Configure Web Access Logging](#tutorials-ddos-cross-service-waf-store)

## Launch the Stack \(Template\)<a name="tutorials-ddos-cross-service-waf-launch"></a>

This automated AWS CloudFormation template deploys the AWS WAF Security Automations solution on the AWS Cloud\. 

**To launch the AWS CloudFormation stack \(template\)**

1. Sign into the AWS CloudFormation [console](https://console.aws.amazon.com/cloudformation/)\.

1. If this is your first time using AWS CloudFormation, on the **Select Template** page, choose **Specify an Amazon S3 template URL** and then enter https://s3\.amazonaws\.com/solutions\-reference/aws\-waf\-security\-automations/latest/aws\-waf\-security\-automations\.template\. If you've used AWS CloudFormation in the past, choose **Create stack**, and then choose **Specify an Amazon S3 template URL** and enter https://s3\.amazonaws\.com/solutions\-reference/aws\-waf\-security\-automations/latest/aws\-waf\-security\-automations\.template\.

1.  Choose **Next**\.

1. On the **Specify Details** page, specify the following values:  
**Stack Name**  
Type a name for the AWS WAF configuration\. This will also be the name of the web ACL that the template creates, for example, `MyWebsiteACL`\.  
**Activate SQL Injection Protection**  
Choose **yes** to enable the component that is designed to block common SQL injection attacks\.  
**Activate Cross\-site Scripting Protection**  
Choose **yes** to enable the component that is designed to block common XSS attacks\.   
**Activate HTTP Flood Protection**  
Choose **no**\. The rate\-based rules provided by AWS WAF are a more efficient way to set up this protection\. After completing this tutorial, if you want to add rate\-based rules, you can find more information in [How AWS WAF Works](http://docs.aws.amazon.com/waf/latest/developerguide/how-aws-waf-works.html)\.  
**Activate Scanner & Probe Protection**  
Choose **yes** to enable the component that is designed to block scanners and probes\.  
**Activate Reputation List Protection**  
Choose **yes** to block requests from IP addresses on third\-party reputation lists \(supported lists: spamhaus, torproject, and emergingthreats\)\.   
**Activate Bad Bot Protection**  
Choose **yes**\. The template requires this protection to be enabled\. However, to take full advantage of this protection, you must complete additional steps outside the scope of this tutorial, such as creating a honeypot link\. Those steps are described [AWS WAF Security Automations, Step 3\. Embed the Honeypot Link in Your Web Application](http://docs.aws.amazon.com/solutions/latest/aws-waf-security-automations/deployment.html)\. These additional steps are optional and are not required to complete this tutorial\. If you choose to perform these additional steps, complete this tutorial first, and then you can set up the honeypot link\.  
**CloudFront Access Log Bucket Name**  
Type a name for the Amazon S3 bucket where you want to store access logs for your CloudFront distribution\. This is the name of a new bucket that the template creates during stack launch\. Do not use an existing name\.   
**Request Threshold**  
This is used for the HTTP flood protection, so is not applicable for this tutorial\. You can leave the default, which is `400`\.  
**Error Threshold**  
Type `50`\. This is the maximum acceptable bad requests per minute per IP address\. This is used by the scanner and probe protection\.  
**WAF Block Period**  
Type `240`\. This is the period \(in minutes\) to block applicable IP addresses that are identified by the scanner and probe protection\.  
**Send Anonymous Usage Data**  
Choose **yes** to send anonymous data to AWS to help us understand solution usage across our customer base as a whole\. To opt out of this feature, choose **no**\. 

1. Choose **Next**\.

1. Make no changes on the **Options** page\.

1. Choose **Next**\.

1. On the **Review** page, review and confirm the settings\. Be sure to select the check box acknowledging that the template will create AWS Identity and Access Management \(IAM\) resources\. 

1. Choose **Create** to deploy the stack\.

You can view the status of the stack in the AWS CloudFormation console in the **Status** column\. You should see a status of **CREATE\_COMPLETE** in about fifteen \(15\) minutes\. 

## Associate the Web ACL with Your Web Application<a name="tutorials-ddos-cross-service-waf-associate"></a>

Now associate your Amazon CloudFront web distribution with the web ACL\. 

**To associate the web ACL with your web application**

1. Sign in to the AWS Management Console and open the AWS WAF console at [https://console\.aws\.amazon\.com/waf/](https://console.aws.amazon.com/waf/)\. 

1. In the navigation pane, choose **Web ACLs**\.

1. Select your newly created WebACL\. The name of this ACL is the name that you specified in the previous step, for example, `MyWebsiteACL`\.

1. Choose the **Rules** tab\.

1. Choose **Add association**\.

1. For **AWS resources using this web ACL**, choose the CloudFront distribution that you created earlier in this tutorial\.

1. Choose **Add** to save your changes\.

## Configure Web Access Logging<a name="tutorials-ddos-cross-service-waf-store"></a>

As the last step of this tutorial, you configure Amazon CloudFront to send web access logs to the appropriate Amazon S3 bucket so that this data is available for the Log Parser AWS Lambda function\. 

**To store web access logs from a CloudFront distribution**

1. Open the Amazon CloudFront console at [https://console\.aws\.amazon\.com/cloudfront/](https://console.aws.amazon.com/cloudfront/)\.

1. Choose the check box next to your distribution, and then choose **Distribution Settings**\.

1. On the **General** tab, choose **Edit**\.

1. Confirm that for **AWS WAF Web ACL**, the web ACL that the solution created \(the same name that you assigned to the stack during initial configuration\) is already entered\.

1. For **Logging**, choose **On**\. 

1. For **Bucket for Logs**, choose the Amazon S3 bucket that you want use to store web access logs \(that you defined in [Launch the Stack \(Template\)](#tutorials-ddos-cross-service-waf-launch)\)\.

1. Choose **Yes, edit** to save your changes\.

Next: [Additional Best Practices](tutorials-ddos-cross-service-best-practices.md)\.