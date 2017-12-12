# Tagging Operations<a name="Tagging.Operations"></a>

 This section describes how to use the DynamoDB console or CLI to add, list, edit, or delete tags\. You can then activate these user\-defined tags so that they appear on the Billing and Cost Management console for cost allocation tracking\. For more information, see [Cost Allocation Reports](CostAllocationReports.md)\. 

 For bulk editing, you can also use the Tag Editor in the AWS Management Console\. For more information, see [Working with Tag Editor](http://docs.aws.amazon.com/awsconsolehelpdocs/latest/gsg/tag-editor.html)\. 

 To use the API instead, see [Amazon DynamoDB API Reference](http://docs.aws.amazon.com/amazondynamodb/latest/APIReference/)\. 


+ [Tagging \(console\)](#Tagging.Operations.using-console)
+ [Tagging \(CLI\)](#Tagging.Operations.using-cli)

## Tagging \(console\)<a name="Tagging.Operations.using-console"></a>

 To use the console to add, list, edit, or delete tags: 

1.  Open the DynamoDB console at [https://console\.aws\.amazon\.com/dynamodb/home](https://console.aws.amazon.com/dynamodb/home)\. 

1.  Choose a table, and then choose the **Settings** tab\. 

    You can add, list, edit, or delete tags here\. In this example, the `Movies` tag was created with a value of `moviesProd` for the Movies table\.   
![\[Image NOT FOUND\]](http://docs.aws.amazon.com/amazondynamodb/latest/developerguide/)

## Tagging \(CLI\)<a name="Tagging.Operations.using-cli"></a>

 To add the `Owner` tag with a value of `blueTeam` for the Movies table: 

```
aws dynamodb tag-resource \
--resource-arn arn:aws:dynamodb:us-east-1:123456789012:table/Movies \
--tags Key=Owner,Value=blueTeam
```

 To list all of the tags associated with the Movies table: 

```
aws dynamodb list-tags-of-resource \
--resource-arn arn:aws:dynamodb:us-east-1:123456789012:table/Movies
```