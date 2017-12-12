# ItemCollectionMetrics<a name="API_ItemCollectionMetrics"></a>

Information about item collections, if any, that were affected by the operation\. `ItemCollectionMetrics` is only returned if the request asked for it\. If the table does not have any local secondary indexes, this information is not returned in the response\.

## Contents<a name="API_ItemCollectionMetrics_Contents"></a>

**Note**  
In the following list, the required parameters are described first\.

 **ItemCollectionKey**   
The partition key value of the item collection\. This value is the same as the partition key value of the item\.  
Type: String to [AttributeValue](API_AttributeValue.md) object map  
Key Length Constraints: Maximum length of 65535\.  
Required: No

 **SizeEstimateRangeGB**   
An estimate of item collection size, in gigabytes\. This value is a two\-element array containing a lower bound and an upper bound for the estimate\. The estimate includes the size of all the items in the table, plus the size of all attributes projected into all of the local secondary indexes on that table\. Use this estimate to measure whether a local secondary index is approaching its size limit\.  
The estimate is subject to change over time; therefore, do not rely on the precision or accuracy of the estimate\.  
Type: Array of doubles  
Required: No

## See Also<a name="API_ItemCollectionMetrics_SeeAlso"></a>

For more information about using this API in one of the language\-specific AWS SDKs, see the following:

+  [AWS SDK for C\+\+](http://docs.aws.amazon.com/goto/SdkForCpp/dynamodb-2012-08-10/ItemCollectionMetrics) 

+  [AWS SDK for Go](http://docs.aws.amazon.com/goto/SdkForGoV1/dynamodb-2012-08-10/ItemCollectionMetrics) 

+  [AWS SDK for Java](http://docs.aws.amazon.com/goto/SdkForJava/dynamodb-2012-08-10/ItemCollectionMetrics) 

+  [AWS SDK for Ruby V2](http://docs.aws.amazon.com/goto/SdkForRubyV2/dynamodb-2012-08-10/ItemCollectionMetrics) 