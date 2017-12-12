# TimeToLiveSpecification<a name="API_TimeToLiveSpecification"></a>

Represents the settings used to enable or disable Time to Live for the specified table\.

## Contents<a name="API_TimeToLiveSpecification_Contents"></a>

**Note**  
In the following list, the required parameters are described first\.

 **AttributeName**   
The name of the Time to Live attribute used to store the expiration time for items in the table\.  
Type: String  
Length Constraints: Minimum length of 1\. Maximum length of 255\.  
Required: Yes

 **Enabled**   
Indicates whether Time To Live is to be enabled \(true\) or disabled \(false\) on the table\.  
Type: Boolean  
Required: Yes

## See Also<a name="API_TimeToLiveSpecification_SeeAlso"></a>

For more information about using this API in one of the language\-specific AWS SDKs, see the following:

+  [AWS SDK for C\+\+](http://docs.aws.amazon.com/goto/SdkForCpp/dynamodb-2012-08-10/TimeToLiveSpecification) 

+  [AWS SDK for Go](http://docs.aws.amazon.com/goto/SdkForGoV1/dynamodb-2012-08-10/TimeToLiveSpecification) 

+  [AWS SDK for Java](http://docs.aws.amazon.com/goto/SdkForJava/dynamodb-2012-08-10/TimeToLiveSpecification) 

+  [AWS SDK for Ruby V2](http://docs.aws.amazon.com/goto/SdkForRubyV2/dynamodb-2012-08-10/TimeToLiveSpecification) 