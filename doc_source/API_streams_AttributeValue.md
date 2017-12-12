# AttributeValue<a name="API_streams_AttributeValue"></a>

Represents the data for an attribute\. You can set one, and only one, of the elements\.

Each attribute in an item is a name\-value pair\. An attribute can be single\-valued or multi\-valued set\. For example, a book item can have title and authors attributes\. Each book has one title but can have many authors\. The multi\-valued attribute is a set; duplicate values are not allowed\.

## Contents<a name="API_streams_AttributeValue_Contents"></a>

**Note**  
In the following list, the required parameters are described first\.

 **B**   
A Binary data type\.  
Type: Base64\-encoded binary data object  
Required: No

 **BOOL**   
A Boolean data type\.  
Type: Boolean  
Required: No

 **BS**   
A Binary Set data type\.  
Type: Array of Base64\-encoded binary data objects  
Required: No

 **L**   
A List data type\.  
Type: Array of [AttributeValue](#API_streams_AttributeValue) objects  
Required: No

 **M**   
A Map data type\.  
Type: String to [AttributeValue](#API_streams_AttributeValue) object map  
Key Length Constraints: Maximum length of 65535\.  
Required: No

 **N**   
A Number data type\.  
Type: String  
Required: No

 **NS**   
A Number Set data type\.  
Type: Array of strings  
Required: No

 **NULL**   
A Null data type\.  
Type: Boolean  
Required: No

 **S**   
A String data type\.  
Type: String  
Required: No

 **SS**   
A String Set data type\.  
Type: Array of strings  
Required: No

## See Also<a name="API_streams_AttributeValue_SeeAlso"></a>

For more information about using this API in one of the language\-specific AWS SDKs, see the following:

+  [AWS SDK for C\+\+](http://docs.aws.amazon.com/goto/SdkForCpp/streams-dynamodb-2012-08-10/AttributeValue) 

+  [AWS SDK for Go](http://docs.aws.amazon.com/goto/SdkForGoV1/streams-dynamodb-2012-08-10/AttributeValue) 

+  [AWS SDK for Java](http://docs.aws.amazon.com/goto/SdkForJava/streams-dynamodb-2012-08-10/AttributeValue) 

+  [AWS SDK for Ruby V2](http://docs.aws.amazon.com/goto/SdkForRubyV2/streams-dynamodb-2012-08-10/AttributeValue) 