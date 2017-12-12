# RestoreSummary<a name="API_RestoreSummary"></a>

Contains details for the restore\.

## Contents<a name="API_RestoreSummary_Contents"></a>

**Note**  
In the following list, the required parameters are described first\.

 **RestoreDateTime**   
Point in time or source backup time\.  
Type: Timestamp  
Required: Yes

 **RestoreInProgress**   
Indicates if a restore is in progress or not\.  
Type: Boolean  
Required: Yes

 **SourceBackupArn**   
ARN of the backup from which the table was restored\.  
Type: String  
Length Constraints: Minimum length of 37\. Maximum length of 1024\.  
Required: No

 **SourceTableArn**   
ARN of the source table of the backup that is being restored\.  
Type: String  
Required: No

## See Also<a name="API_RestoreSummary_SeeAlso"></a>

For more information about using this API in one of the language\-specific AWS SDKs, see the following:

+  [AWS SDK for C\+\+](http://docs.aws.amazon.com/goto/SdkForCpp/dynamodb-2012-08-10/RestoreSummary) 

+  [AWS SDK for Go](http://docs.aws.amazon.com/goto/SdkForGoV1/dynamodb-2012-08-10/RestoreSummary) 

+  [AWS SDK for Java](http://docs.aws.amazon.com/goto/SdkForJava/dynamodb-2012-08-10/RestoreSummary) 

+  [AWS SDK for Ruby V2](http://docs.aws.amazon.com/goto/SdkForRubyV2/dynamodb-2012-08-10/RestoreSummary) 