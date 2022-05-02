# Detect PPE in images with Amazon Rekognition using an AWS SDK<a name="example_cross_RekognitionPhotoAnalyzerPPE_section"></a>

The following code examples show how to build an app that uses Amazon Rekognition to detect Personal Protective Equipment \(PPE\) in images\.

**Note**  
The source code for these examples is in the [AWS Code Examples GitHub repository](https://github.com/awsdocs/aws-doc-sdk-examples)\. Have feedback on a code example? [Create an Issue](https://github.com/awsdocs/aws-doc-sdk-examples/issues/new/choose) in the code examples repo\. 

------
#### [ Java ]

**SDK for Java 2\.x**  
 Shows how to create an AWS Lambda function that detects images with Personal Protective Equipment\.   
 For complete source code and instructions on how to set up and run, see the full example on [GitHub](https://github.com/awsdocs/aws-doc-sdk-examples/tree/main/javav2/usecases/creating_lambda_ppe)\.   

**Services used in this example**
+ DynamoDB
+ Amazon Rekognition
+ Amazon S3
+ Amazon SES

------
#### [ JavaScript ]

**SDK for JavaScript V3**  
 Shows how to use Amazon Rekognition with the AWS SDK for JavaScript to create an application to detect personal protective equipment \(PPE\) in images located in an Amazon Simple Storage Service \(Amazon S3\) bucket\. The app saves the results to an Amazon DynamoDB table, and sends the admin an email notification with the results using Amazon Simple Email Service \(Amazon SES\)\.   
Learn how to:  
+ Create an unauthenticated user using Amazon Cognito\.
+ Analyze images for PPE using Amazon Rekognition\.
+ Verify an email address for Amazon SES\.
+ Update a DynamoDB table with results\.
+ Send an email notification using Amazon SES\.
For complete source code and instructions on how to set up and run, see the full example on [GitHub](https://github.com/awsdocs/aws-doc-sdk-examples/tree/main/javascriptv3/example_code/cross-services/photo-analyzer-ppe)\.   

**Services used in this example**
+ DynamoDB
+ Amazon Rekognition
+ Amazon S3
+ Amazon SES

------

For a complete list of AWS SDK developer guides and code examples, see [Using DynamoDB with an AWS SDK](sdk-general-information-section.md)\. This topic also includes information about getting started and details about previous SDK versions\.