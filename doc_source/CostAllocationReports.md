# Cost allocation reports<a name="CostAllocationReports"></a>

AWS uses tags to organize resource costs on your cost allocation report\. AWS provides two types of cost allocation tags:
+ An AWS\-generated tag\. AWS defines, creates, and applies this tag for you\.
+ User\-defined tags\. You define, create, and apply these tags\.

You must activate both types of tags separately before they can appear in Cost Explorer or on a cost allocation report\. 

 To activate AWS\-generated tags: 

1.  Sign in to the AWS Management Console and open the Billing and Cost Management console at [https://console\.aws\.amazon\.com/billing/home\#/\.](https://console.aws.amazon.com/billing/home#/.) 

1.  In the navigation pane, choose **Cost Allocation Tags**\. 

1.  Under **AWS\-Generated Cost Allocation Tags**, choose **Activate**\. 

 To activate user\-defined tags: 

1.  Sign in to the AWS Management Console and open the Billing and Cost Management console at [https://console\.aws\.amazon\.com/billing/home\#/\.](https://console.aws.amazon.com/billing/home#/.) 

1.  In the navigation pane, choose **Cost Allocation Tags**\. 

1.  Under **User\-Defined Cost Allocation Tags**, choose **Activate**\. 

 After you create and activate tags, AWS generates a cost allocation report with your usage and costs grouped by your active tags\. The cost allocation report includes all of your AWS costs for each billing period\. The report includes both tagged and untagged resources, so that you can clearly organize the charges for resources\. 

**Note**  
 Currently, any data transferred out from DynamoDB won't be broken down by tags on cost allocation reports\. 

 For more information, see [Using cost allocation tags](https://docs.aws.amazon.com/awsaccountbilling/latest/aboutv2/cost-alloc-tags.html)\. 