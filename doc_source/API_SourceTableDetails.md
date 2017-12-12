# SourceTableDetails<a name="API_SourceTableDetails"></a>

Contains the details of the table when the backup was created\. 

## Contents<a name="API_SourceTableDetails_Contents"></a>

**Note**  
In the following list, the required parameters are described first\.

 **KeySchema**   
Schema of the table\.   
Type: Array of [KeySchemaElement](API_KeySchemaElement.md) objects  
Array Members: Minimum number of 1 item\. Maximum number of 2 items\.  
Required: Yes

 **ProvisionedThroughput**   
Read IOPs and Write IOPS on the table when the backup was created\.  
Type: [ProvisionedThroughput](API_ProvisionedThroughput.md) object  
Required: Yes

 **TableCreationDateTime**   
Time when the source table was created\.   
Type: Timestamp  
Required: Yes

 **TableId**   
Unique identifier for the table for which the backup was created\.   
Type: String  
Pattern: `[0-9a-f]{8}-[0-9a-f]{4}-[0-9a-f]{4}-[0-9a-f]{4}-[0-9a-f]{12}`   
Required: Yes

 **TableName**   
The name of the table for which the backup was created\.   
Type: String  
Length Constraints: Minimum length of 3\. Maximum length of 255\.  
Pattern: `[a-zA-Z0-9_.-]+`   
Required: Yes

 **ItemCount**   
Number of items in the table\. Please note this is an approximate value\.   
Type: Long  
Valid Range: Minimum value of 0\.  
Required: No

 **TableArn**   
ARN of the table for which backup was created\.   
Type: String  
Required: No

 **TableSizeBytes**   
Size of the table in bytes\. Please note this is an approximate value\.  
Type: Long  
Required: No

## See Also<a name="API_SourceTableDetails_SeeAlso"></a>

For more information about using this API in one of the language\-specific AWS SDKs, see the following:

+  [AWS SDK for C\+\+](http://docs.aws.amazon.com/goto/SdkForCpp/dynamodb-2012-08-10/SourceTableDetails) 

+  [AWS SDK for Go](http://docs.aws.amazon.com/goto/SdkForGoV1/dynamodb-2012-08-10/SourceTableDetails) 

+  [AWS SDK for Java](http://docs.aws.amazon.com/goto/SdkForJava/dynamodb-2012-08-10/SourceTableDetails) 

+  [AWS SDK for Ruby V2](http://docs.aws.amazon.com/goto/SdkForRubyV2/dynamodb-2012-08-10/SourceTableDetails) 