# Step 2: Load Data into Tables<a name="SampleData.LoadData"></a>

In this step, you load sample data into the tables that you created\. You could enter the data manually into the Amazon DynamoDB console\. However, to save time, you use the AWS Command Line Interface \(AWS CLI\) instead\.

**Note**  
If you have not yet set up the AWS CLI, see [Using the AWS CLI](Tools.CLI.md) for instructions\.

You will download a `.zip` archive that contains JSON files with sample data for each table\. For each file, you use the AWS CLI to load the data into DynamoDB\. Each successful data load produces the following output\.

```
{
    "UnprocessedItems": {}
}
```

## Download the Sample Data File Archive<a name="SampleData.LoadData.DownloadArchive"></a>

1. Download the sample data archive \(`sampledata.zip`\) using this link:
   +  [sampledata\.zip](samples/sampledata.zip) 

1. Extract the `.json` data files from the archive\.

1. Copy the `.json` data files to your current directory\.

## Load the Sample Data into DynamoDB Tables<a name="SampleData.LoadData.LoadData"></a>

1. To load the `ProductCatalog` table with data, enter the following command\.

   `aws dynamodb batch-write-item --request-items file://ProductCatalog.json`

1. To load the `Forum` table with data, enter the following command\.

   `aws dynamodb batch-write-item --request-items file://Forum.json`

1. To load the `Thread` table with data, enter the following command\.

   `aws dynamodb batch-write-item --request-items file://Thread.json`

1. To load the `Reply` table with data, enter the following command\.

   `aws dynamodb batch-write-item --request-items file://Reply.json`

## Verify Data Load<a name="SampleData.Verify"></a>

You can use the AWS Management Console to verify the data that you loaded into the tables\.

**To verify the data using the AWS Management Console**

1. Open the DynamoDB console at [https://console\.aws\.amazon\.com/dynamodb/](https://console.aws.amazon.com/dynamodb/)\.

1. In the navigation pane, choose **Tables**\.

1. In the list of tables, choose **ProductCatalog**\.

1. Choose the **Items** tab to view the data that you loaded into the table\.

1. To view an item in the table, choose its `Id`\. \(If you want, you can also edit the item\.\)

1. To return to the list of tables, choose **Cancel**\.

Repeat this procedure for each of the other tables you created:
+ `Forum`
+ `Thread`
+ `Reply`