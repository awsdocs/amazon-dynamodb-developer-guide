# Projection<a name="API_Projection"></a>

Represents attributes that are copied \(projected\) from the table into an index\. These are in addition to the primary key attributes and index key attributes, which are automatically projected\.

## Contents<a name="API_Projection_Contents"></a>

**Note**  
In the following list, the required parameters are described first\.

 **NonKeyAttributes**   
Represents the non\-key attribute names which will be projected into the index\.  
For local secondary indexes, the total count of `NonKeyAttributes` summed across all of the local secondary indexes, must not exceed 20\. If you project the same attribute into two different indexes, this counts as two distinct attributes when determining the total\.  
Type: Array of strings  
Array Members: Minimum number of 1 item\. Maximum number of 20 items\.  
Length Constraints: Minimum length of 1\. Maximum length of 255\.  
Required: No

 **ProjectionType**   
The set of attributes that are projected into the index:  

+  `KEYS_ONLY` \- Only the index and primary keys are projected into the index\.

+  `INCLUDE` \- Only the specified table attributes are projected into the index\. The list of projected attributes are in `NonKeyAttributes`\.

+  `ALL` \- All of the table attributes are projected into the index\.
Type: String  
Valid Values:` ALL | KEYS_ONLY | INCLUDE`   
Required: No

## See Also<a name="API_Projection_SeeAlso"></a>

For more information about using this API in one of the language\-specific AWS SDKs, see the following:

+  [AWS SDK for C\+\+](http://docs.aws.amazon.com/goto/SdkForCpp/dynamodb-2012-08-10/Projection) 

+  [AWS SDK for Go](http://docs.aws.amazon.com/goto/SdkForGoV1/dynamodb-2012-08-10/Projection) 

+  [AWS SDK for Java](http://docs.aws.amazon.com/goto/SdkForJava/dynamodb-2012-08-10/Projection) 

+  [AWS SDK for Ruby V2](http://docs.aws.amazon.com/goto/SdkForRubyV2/dynamodb-2012-08-10/Projection) 