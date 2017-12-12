# AttributeValue<a name="API_AttributeValue"></a>

Represents the data for an attribute\.

Each attribute value is described as a name\-value pair\. The name is the data type, and the value is the data itself\.

For more information, see [Data Types](http://docs.aws.amazon.com/amazondynamodb/latest/developerguide/HowItWorks.NamingRulesDataTypes.html#HowItWorks.DataTypes) in the *Amazon DynamoDB Developer Guide*\.

## Contents<a name="API_AttributeValue_Contents"></a>

**Note**  
In the following list, the required parameters are described first\.

 **B**   
An attribute of type Binary\. For example:  
 `"B": "dGhpcyB0ZXh0IGlzIGJhc2U2NC1lbmNvZGVk"`   
Type: Base64\-encoded binary data object  
Required: No

 **BOOL**   
An attribute of type Boolean\. For example:  
 `"BOOL": true`   
Type: Boolean  
Required: No

 **BS**   
An attribute of type Binary Set\. For example:  
 `"BS": ["U3Vubnk=", "UmFpbnk=", "U25vd3k="]`   
Type: Array of Base64\-encoded binary data objects  
Required: No

 **L**   
An attribute of type List\. For example:  
 `"L": ["Cookies", "Coffee", 3.14159]`   
Type: Array of [AttributeValue](#API_AttributeValue) objects  
Required: No

 **M**   
An attribute of type Map\. For example:  
 `"M": {"Name": {"S": "Joe"}, "Age": {"N": "35"}}`   
Type: String to [AttributeValue](#API_AttributeValue) object map  
Key Length Constraints: Maximum length of 65535\.  
Required: No

 **N**   
An attribute of type Number\. For example:  
 `"N": "123.45"`   
Numbers are sent across the network to DynamoDB as strings, to maximize compatibility across languages and libraries\. However, DynamoDB treats them as number type attributes for mathematical operations\.  
Type: String  
Required: No

 **NS**   
An attribute of type Number Set\. For example:  
 `"NS": ["42.2", "-19", "7.5", "3.14"]`   
Numbers are sent across the network to DynamoDB as strings, to maximize compatibility across languages and libraries\. However, DynamoDB treats them as number type attributes for mathematical operations\.  
Type: Array of strings  
Required: No

 **NULL**   
An attribute of type Null\. For example:  
 `"NULL": true`   
Type: Boolean  
Required: No

 **S**   
An attribute of type String\. For example:  
 `"S": "Hello"`   
Type: String  
Required: No

 **SS**   
An attribute of type String Set\. For example:  
 `"SS": ["Giraffe", "Hippo" ,"Zebra"]`   
Type: Array of strings  
Required: No

## See Also<a name="API_AttributeValue_SeeAlso"></a>

For more information about using this API in one of the language\-specific AWS SDKs, see the following:

+  [AWS SDK for C\+\+](http://docs.aws.amazon.com/goto/SdkForCpp/dynamodb-2012-08-10/AttributeValue) 

+  [AWS SDK for Go](http://docs.aws.amazon.com/goto/SdkForGoV1/dynamodb-2012-08-10/AttributeValue) 

+  [AWS SDK for Java](http://docs.aws.amazon.com/goto/SdkForJava/dynamodb-2012-08-10/AttributeValue) 

+  [AWS SDK for Ruby V2](http://docs.aws.amazon.com/goto/SdkForRubyV2/dynamodb-2012-08-10/AttributeValue) 