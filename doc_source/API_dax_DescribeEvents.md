# DescribeEvents<a name="API_dax_DescribeEvents"></a>

Returns events related to DAX clusters and parameter groups\. You can obtain events specific to a particular DAX cluster or parameter group by providing the name as a parameter\.

By default, only the events occurring within the last hour are returned; however, you can retrieve up to 14 days' worth of events if necessary\.

## Request Syntax<a name="API_dax_DescribeEvents_RequestSyntax"></a>

```
{
   "Duration": number,
   "EndTime": number,
   "MaxResults": number,
   "NextToken": "string",
   "SourceName": "string",
   "SourceType": "string",
   "StartTime": number
}
```

## Request Parameters<a name="API_dax_DescribeEvents_RequestParameters"></a>

The request accepts the following data in JSON format\.

**Note**  
In the following list, the required parameters are described first\.

 ** Duration **   
The number of minutes' worth of events to retrieve\.  
Type: Integer  
Required: No

 ** EndTime **   
The end of the time interval for which to retrieve events, specified in ISO 8601 format\.  
Type: Timestamp  
Required: No

 ** MaxResults **   
The maximum number of results to include in the response\. If more results exist than the specified `MaxResults` value, a token is included in the response so that the remaining results can be retrieved\.  
The value for `MaxResults` must be between 20 and 100\.  
Type: Integer  
Required: No

 ** NextToken **   
An optional token returned from a prior request\. Use this token for pagination of results from this action\. If this parameter is specified, the response includes only results beyond the token, up to the value specified by `MaxResults`\.  
Type: String  
Required: No

 ** SourceName **   
The identifier of the event source for which events will be returned\. If not specified, then all sources are included in the response\.  
Type: String  
Required: No

 ** SourceType **   
The event source to retrieve events for\. If no value is specified, all events are returned\.  
Type: String  
Valid Values:` CLUSTER | PARAMETER_GROUP | SUBNET_GROUP`   
Required: No

 ** StartTime **   
The beginning of the time interval to retrieve events for, specified in ISO 8601 format\.  
Type: Timestamp  
Required: No

## Response Syntax<a name="API_dax_DescribeEvents_ResponseSyntax"></a>

```
{
   "Events": [ 
      { 
         "Date": number,
         "Message": "string",
         "SourceName": "string",
         "SourceType": "string"
      }
   ],
   "NextToken": "string"
}
```

## Response Elements<a name="API_dax_DescribeEvents_ResponseElements"></a>

If the action is successful, the service sends back an HTTP 200 response\.

The following data is returned in JSON format by the service\.

 ** Events **   
An array of events\. Each element in the array represents one event\.  
Type: Array of [Event](API_dax_Event.md) objects

 ** NextToken **   
Provides an identifier to allow retrieval of paginated results\.  
Type: String

## Errors<a name="API_dax_DescribeEvents_Errors"></a>

For information about the errors that are common to all actions, see [Common Errors](CommonErrors.md)\.

 **InvalidParameterCombinationException**   
Two or more incompatible parameters were specified\.  
HTTP Status Code: 400

 **InvalidParameterValueException**   
The value for a parameter is invalid\.  
HTTP Status Code: 400

## See Also<a name="API_dax_DescribeEvents_SeeAlso"></a>

For more information about using this API in one of the language\-specific AWS SDKs, see the following:

+  [AWS Command Line Interface](http://docs.aws.amazon.com/goto/aws-cli/dax-2017-04-19/DescribeEvents) 

+  [AWS SDK for \.NET](http://docs.aws.amazon.com/goto/DotNetSDKV3/dax-2017-04-19/DescribeEvents) 

+  [AWS SDK for C\+\+](http://docs.aws.amazon.com/goto/SdkForCpp/dax-2017-04-19/DescribeEvents) 

+  [AWS SDK for Go](http://docs.aws.amazon.com/goto/SdkForGoV1/dax-2017-04-19/DescribeEvents) 

+  [AWS SDK for Java](http://docs.aws.amazon.com/goto/SdkForJava/dax-2017-04-19/DescribeEvents) 

+  [AWS SDK for JavaScript](http://docs.aws.amazon.com/goto/AWSJavaScriptSDK/dax-2017-04-19/DescribeEvents) 

+  [AWS SDK for PHP V3](http://docs.aws.amazon.com/goto/SdkForPHPV3/dax-2017-04-19/DescribeEvents) 

+  [AWS SDK for Python](http://docs.aws.amazon.com/goto/boto3/dax-2017-04-19/DescribeEvents) 

+  [AWS SDK for Ruby V2](http://docs.aws.amazon.com/goto/SdkForRubyV2/dax-2017-04-19/DescribeEvents) 