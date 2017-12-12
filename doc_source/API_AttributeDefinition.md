# AttributeDefinition<a name="API_AttributeDefinition"></a>

Represents an attribute for describing the key schema for the table and indexes\.

## Contents<a name="API_AttributeDefinition_Contents"></a>

**Note**  
In the following list, the required parameters are described first\.

 **AttributeName**   
A name for the attribute\.  
Type: String  
Length Constraints: Minimum length of 1\. Maximum length of 255\.  
Required: Yes

 **AttributeType**   
The data type for the attribute, where:  

+  `S` \- the attribute is of type String

+  `N` \- the attribute is of type Number

+  `B` \- the attribute is of type Binary
Type: String  
Valid Values:` S | N | B`   
Required: Yes

## See Also<a name="API_AttributeDefinition_SeeAlso"></a>

For more information about using this API in one of the language\-specific AWS SDKs, see the following:

+  [AWS SDK for C\+\+](http://docs.aws.amazon.com/goto/SdkForCpp/dynamodb-2012-08-10/AttributeDefinition) 

+  [AWS SDK for Go](http://docs.aws.amazon.com/goto/SdkForGoV1/dynamodb-2012-08-10/AttributeDefinition) 

+  [AWS SDK for Java](http://docs.aws.amazon.com/goto/SdkForJava/dynamodb-2012-08-10/AttributeDefinition) 

+  [AWS SDK for Ruby V2](http://docs.aws.amazon.com/goto/SdkForRubyV2/dynamodb-2012-08-10/AttributeDefinition) 