# BackupSummary<a name="API_BackupSummary"></a>

Contains details for the backup\.

## Contents<a name="API_BackupSummary_Contents"></a>

**Note**  
In the following list, the required parameters are described first\.

 **BackupArn**   
ARN associated with the backup\.  
Type: String  
Length Constraints: Minimum length of 37\. Maximum length of 1024\.  
Required: No

 **BackupCreationDateTime**   
Time at which the backup was created\.  
Type: Timestamp  
Required: No

 **BackupName**   
Name of the specified backup\.  
Type: String  
Length Constraints: Minimum length of 3\. Maximum length of 255\.  
Required: No

 **BackupSizeBytes**   
Size of the backup in bytes\.  
Type: Long  
Valid Range: Minimum value of 0\.  
Required: No

 **BackupStatus**   
Backup can be in one of the following states: CREATING, ACTIVE, DELETED\.  
Type: String  
Valid Values:` CREATING | DELETED | AVAILABLE`   
Required: No

 **TableArn**   
ARN associated with the table\.  
Type: String  
Required: No

 **TableId**   
Unique identifier for the table\.  
Type: String  
Pattern: `[0-9a-f]{8}-[0-9a-f]{4}-[0-9a-f]{4}-[0-9a-f]{4}-[0-9a-f]{12}`   
Required: No

 **TableName**   
Name of the table\.  
Type: String  
Length Constraints: Minimum length of 3\. Maximum length of 255\.  
Pattern: `[a-zA-Z0-9_.-]+`   
Required: No

## See Also<a name="API_BackupSummary_SeeAlso"></a>

For more information about using this API in one of the language\-specific AWS SDKs, see the following:

+  [AWS SDK for C\+\+](http://docs.aws.amazon.com/goto/SdkForCpp/dynamodb-2012-08-10/BackupSummary) 

+  [AWS SDK for Go](http://docs.aws.amazon.com/goto/SdkForGoV1/dynamodb-2012-08-10/BackupSummary) 

+  [AWS SDK for Java](http://docs.aws.amazon.com/goto/SdkForJava/dynamodb-2012-08-10/BackupSummary) 

+  [AWS SDK for Ruby V2](http://docs.aws.amazon.com/goto/SdkForRubyV2/dynamodb-2012-08-10/BackupSummary) 