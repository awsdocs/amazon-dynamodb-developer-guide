# Save EXIF and other image information using an AWS SDK<a name="example_cross_DetectLabels_section"></a>

The following code example shows how to:
+ Get EXIF information from a a JPG, JPEG, or PNG file\.
+ Upload the image file to an Amazon Simple Storage Service \(Amazon S3\) bucket\.
+ Use Amazon Rekognition \(Amazon Rekognition\) to identify the three top attributes \(labels in Amazon Rekognition\) in the file\.
+ Add the EXIF and label information to a Amazon DynamoDB \(DynamoDB\) table in the Region\.

**Note**  
The source code for these examples is in the [AWS Code Examples GitHub repository](https://github.com/awsdocs/aws-doc-sdk-examples)\. Have feedback on a code example? [Create an Issue](https://github.com/awsdocs/aws-doc-sdk-examples/issues/new/choose) in the code examples repo\. 

------
#### [ Rust ]

**SDK for Rust**  
This documentation is for an SDK in preview release\. The SDK is subject to change and should not be used in production\.
 Get EXIF information from a JPG, JPEG, or PNG file, upload the image file to an Amazon Simple Storage Service bucket, use Amazon Rekognition to identify the three top attributes \(*labels* in Amazon Rekognition\) in the file, and add the EXIF and label information to a Amazon DynamoDB table in the Region\.   
 For complete source code and instructions on how to set up and run, see the full example on [GitHub](https://github.com/awsdocs/aws-doc-sdk-examples/blob/main/rust_dev_preview/cross_service/detect_labels/src/main.rs)\.   

**Services used in this example**
+ DynamoDB
+ Amazon Rekognition
+ Amazon S3

------

For a complete list of AWS SDK developer guides and code examples, see [Using DynamoDB with an AWS SDK](sdk-general-information-section.md)\. This topic also includes information about getting started and details about previous SDK versions\.