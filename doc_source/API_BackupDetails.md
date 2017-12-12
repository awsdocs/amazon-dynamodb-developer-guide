# BackupDetails<a name="API_BackupDetails"></a>

Contains the details of the backup created for the table\.

## Contents<a name="API_BackupDetails_Contents"></a>

**Note**  
In the following list, the required parameters are described first\.

 **BackupArn**   
ARN associated with the backup\.  
Type: String  
Length Constraints: Minimum length of 37\. Maximum length of 1024\.  
Required: Yes

 **BackupCreationDateTime**   
Time at which the backup was created\. This is the request time of the backup\.   
Type: Timestamp  
Required: Yes

 **BackupName**   
Name of the requested backup\.  
Type: String  
Length Constraints: Minimum length of 3\. Maximum length of 255\.  
Required: Yes

 **BackupStatus**   
Backup can be in one of the following states: CREATING, ACTIVE, DELETED\.   
Type: String  
Valid Values:` CREATING | DELETED | AVAILABLE`   
Required: Yes

 **BackupSizeBytes**   
Size of the backup in bytes\.  
Type: Long  
Valid Range: Minimum value of 0\.  
Required: No

## See Also<a name="API_BackupDetails_SeeAlso"></a>

For more information about using this API in one of the language\-specific AWS SDKs, see the following:

+  [AWS SDK for C\+\+](http://docs.aws.amazon.com/goto/SdkForCpp/dynamodb-2012-08-10/BackupDetails) 

+  [AWS SDK for Go](http://docs.aws.amazon.com/goto/SdkForGoV1/dynamodb-2012-08-10/BackupDetails) 

+  [AWS SDK for Java](http://docs.aws.amazon.com/goto/SdkForJava/dynamodb-2012-08-10/BackupDetails) 

+  [AWS SDK for Ruby V2](http://docs.aws.amazon.com/goto/SdkForRubyV2/dynamodb-2012-08-10/BackupDetails) 