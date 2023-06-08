# Tagging resources in DynamoDB<a name="Tagging.Operations"></a>

You can use the Amazon DynamoDB console or the AWS Command Line Interface \(AWS CLI\) to add, list, edit, or delete tags\. You can then activate these user\-defined tags so that they appear on the AWS Billing and Cost Management console for cost allocation tracking\. For more information, see [Cost allocation reports](CostAllocationReports.md)\. 

 For bulk editing, you can also use Tag Editor on the AWS Management Console\. For more information, see [Working with Tag Editor](http://docs.aws.amazon.com/awsconsolehelpdocs/latest/gsg/tag-editor.html)\. 

 To use the DynamoDB API instead, see the following operations in the [Amazon DynamoDB API Reference](https://docs.aws.amazon.com/amazondynamodb/latest/APIReference/):
+ [TagResource](https://docs.aws.amazon.com/amazondynamodb/latest/APIReference/API_TagResource.html)
+ [UntagResource](https://docs.aws.amazon.com/amazondynamodb/latest/APIReference/API_UntagResource.html)
+ [ListTagsOfResource](https://docs.aws.amazon.com/amazondynamodb/latest/APIReference/API_ListTagsOfResource.html)

**Topics**
+ [Setting permissions to filter by tags](#Tagging.Operations.permissions)
+ [Adding tags to new or existing tables \(AWS Management Console\)](#Tagging.Operations.using-console)
+ [Adding tags to new or existing tables \(AWS CLI\)](#Tagging.Operations.using-cli)

## Setting permissions to filter by tags<a name="Tagging.Operations.permissions"></a>

To use tags to filter your table list in the DynamoDB console, make sure your user's policies include access to the following operations:
+ `tag:GetTagKeys`
+ `tag:GetTagValues`

You can access these operations by attaching a new IAM policy to your user by following the steps below\.

1. Go to the [IAM console](https://console.aws.amazon.com/iam/) with an Admin user\.

1. Select "Policies" in the left navigation menu\.

1. Select "Create policy\."

1. Paste the following policy into the JSON editor\.

   ```
   {
       "Version": "2012-10-17",
       "Statement": [
           {
               "Effect": "Allow",
               "Action": [
                   "tag:GetTagKeys",
                   "tag:GetTagValues"
               ],
               "Resource": "*"
           }
       ]
   }
   ```

1. Complete the wizard and assign a name to the policy \(for example, `TagKeysAndValuesReadAccess`\)\.

1. Select "Users" in the left navigation menu\.

1. From the list, select the user you normally use to access the DynamoDB console\.

1. Select "Add permissions\."

1. Select "Attach existing policies directly\."

1. From the list, select the policy you created previously\.

1. Complete the wizard\.

## Adding tags to new or existing tables \(AWS Management Console\)<a name="Tagging.Operations.using-console"></a>

You can use the DynamoDB console to add tags to new tables when you create them, or to add, edit, or delete tags for existing tables\.

**To tag resources on creation \(console\)**

1. Sign in to the AWS Management Console and open the DynamoDB console at [https://console\.aws\.amazon\.com/dynamodb/](https://console.aws.amazon.com/dynamodb/)\.

1. In the navigation pane, choose **Tables**, and then choose **Create table**\.

1. On the **Create DynamoDB table** page, provide a name and primary key\. In the **Tags** section, choose **Add new tag** and enter the tags that you want to use\.

   For information about tag structure, see [Tagging restrictions in DynamoDB](TaggingRestrictions.md)\. 

   For more information about creating tables, see [Basic operations on DynamoDB tables](WorkingWithTables.Basics.md)\.

**To tag existing resources \(console\)**

Open the DynamoDB console at [https://console\.aws\.amazon\.com/dynamodb/](https://console.aws.amazon.com/dynamodb/)\.

1. In the navigation pane, choose **Tables**\.

1. Choose a table in the list, and then choose the **Additional settings** tab\. You can add, edit, or delete your tags in the **Tags** section at the bottom of the page\.

## Adding tags to new or existing tables \(AWS CLI\)<a name="Tagging.Operations.using-cli"></a>

The following examples show how to use the AWS CLI to specify tags when you create tables and indexes, and to tag existing resources\.

**To tag resources on creation \(AWS CLI\)**
+ The following example creates a new `Movies` table and adds the `Owner` tag with a value of `blueTeam`: 

  ```
  aws dynamodb create-table \
      --table-name Movies \
      --attribute-definitions AttributeName=Title,AttributeType=S \
      --key-schema AttributeName=Title,KeyType=HASH \
      --provisioned-throughput ReadCapacityUnits=5,WriteCapacityUnits=5 \
      --tags Key=Owner,Value=blueTeam
  ```

**To tag existing resources \(AWS CLI\)**
+ The following example adds the `Owner` tag with a value of `blueTeam` for the `Movies` table: 

  ```
  aws dynamodb tag-resource \
      --resource-arn arn:aws:dynamodb:us-east-1:123456789012:table/Movies \
      --tags Key=Owner,Value=blueTeam
  ```

**To list all tags for a table \(AWS CLI\)**
+ The following example lists all the tags that are associated with the `Movies` table:

  ```
  aws dynamodb list-tags-of-resource \
      --resource-arn arn:aws:dynamodb:us-east-1:123456789012:table/Movies
  ```