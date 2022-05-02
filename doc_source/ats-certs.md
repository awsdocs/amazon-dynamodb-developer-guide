# Troubleshooting SSL/TLS connection establishment issues<a name="ats-certs"></a>

 Amazon DynamoDB is in the process of moving our endpoints to secure certificates signed by the Amazon Trust Services \(ATS\) Certificate Authority instead of third\-party Certificate Authority\. In December 2017, we launched the EU\-WEST\-3 \(Paris\) Region with the secure certificates issued by the Amazon Trust Services\. All new regions launched after December 2017 have endpoints with the certificates issued by the Amazon Trust Services\. This guide shows you how to validate and troubleshoot SSL/TLS connection issues\. 

## Testing your application or service<a name="w615aac41b5b5"></a>

 Most AWS SDKs and Command Line Interfaces \(CLIs\) support the Amazon Trust Services Certificate Authority\. If you are using a version of the AWS SDK for Python or CLI released before October 29, 2013, you must upgrade\. The \.NET, Java, PHP, Go, JavaScript, and C\+\+ SDKs and CLIs do not bundle any certificates, their certificates come from the underlying operating system\. The Ruby SDK has included at least one of the required CAs since June 10, 2015\. Before that date, the Ruby V2 SDK did not bundle certificates\. If you use an unsupported, custom, or modified version of the AWS SDK, or if you use custom trust store, you might not have the support needed for Amazon Trust Services Certificate Authority\. 

To validate access to DynamoDB endpoints, you will need to develop a test that accesses DynamoDB API or DynamoDB Streams API in the EU\-WEST\-3 region and validate that the TLS handshake succeeds\. The specific endpoints you will need to access in such test are: 
+  DynamoDB: [https://dynamodb\.eu\-west\-3\.amazonaws\.com](https://dynamodb.eu-west-3.amazonaws.com) 
+  DynamoDB Streams: [https://streams\.dynamodb\.eu\-west\-3\.amazonaws\.com](https://streams.dynamodb.eu-west-3.amazonaws.com) 

 If your application does not support Amazon Trust Services Certificate Authority you will see one of the following failures: 
+  SSL/TLS Negotiation errors 
+  A long delay before your software receives an error indicating SSL/TLS negotiation failure\. The delay time depends on the retry strategy and timeout configuration of your client\. 

## Testing your client browser<a name="w615aac41b5b7"></a>

 To verify that your browser can connect to Amazon DynamoDB, open the following URL: [https://dynamodb\.eu\-west\-3\.amazonaws\.com](https://dynamodb.eu-west-3.amazonaws.com)\. If the test is successful, you will see a message like this: 

```
healthy: dynamodb.eu-west-3.amazonaws.com
```

 If the test is unsuccessful, it will display an error similar to this: [https://untrusted\-root\.badssl\.com/](https://untrusted-root.badssl.com/)\. 

## Updating your software application client<a name="w615aac41b5b9"></a>

 Applications accessing DynamoDB or DynamoDB Streams API endpoints \(whether through browsers or programmatically\) will need to update the trusted CA list on the client machines if they do not already support any of the following CAs: 
+  Amazon Root CA 1 
+  Starfield Services Root Certificate Authority \- G2 
+  Starfield Class 2 Certification Authority 

 If the clients already trust ANY of the above three CAs then these will trust certificates used by DynamoDB and no action is required\. However, if your clients do not already trust any of the above CAs, the HTTPS connections to the DynamoDB or DynamoDB Streams APIs will fail\. For more information, please visit this blog post: [https://aws\.amazon\.com/blogs/security/how\-to\-prepare\-for\-aws\-move\-to\-its\-own\-certificate\-authority/](https://aws.amazon.com/blogs/security/how-to-prepare-for-aws-move-to-its-own-certificate-authority/)\. 

## Updating your client browser<a name="w615aac41b5c11"></a>

 You can update the certificate bundle in your browser simply by updating your browser\. Instructions for the most common browsers can be found on the browsers’ websites: 
+  Chrome: [https://support\.google\.com/chrome/answer/95414?hl=en](https://support.google.com/chrome/answer/95414?hl=en) 
+  Firefox: [https://support\.mozilla\.org/en\-US/kb/update\-firefox\-latest\-version](https://support.mozilla.org/en-US/kb/update-firefox-latest-version) 
+  Safari: [https://support\.apple\.com/en\-us/HT204416](https://support.apple.com/en-us/HT204416) 
+  Internet Explorer: [https://support\.microsoft\.com/en\-us/help/17295/windows\-internet\-explorer\-which\-version\#ie=other](https://support.microsoft.com/en-us/help/17295/windows-internet-explorer-which-version#ie=other) 

## Manually updating your certificate bundle<a name="w615aac41b5c13"></a>

 If you cannot access the DynamoDB API or DynamoDB Streams API then you need to update your certificate bundle\. To do this, you need to import at least one of the required CAs\. You can find these at [https://www\.amazontrust\.com/repository/](https://www.amazontrust.com/repository/)\. 

 The following operating systems and programming languages support Amazon Trust Services certificates: 
+  Microsoft Windows versions that have January 2005 or later updates installed, Windows Vista, Windows 7, Windows Server 2008, and newer versions\. 
+  MacOS X 10\.4 with Java for MacOS X 10\.4 Release 5, MacOS X 10\.5 and newer versions\. 
+  Red Hat Enterprise Linux 5 \(March 2007\), Linux 6, and Linux 7 and CentOS 5, CentOS 6, and CentOS 7 
+  Ubuntu 8\.10 
+  Debian 5\.0 
+  Amazon Linux \(all versions\) 
+  Java 1\.4\.2\_12, Java 5 update 2, and all newer versions, including Java 6, Java 7, and Java 8 

 If you are still unable to connect please consult your software documentation, OS Vendor or contact AWS Support [https://aws\.amazon\.com/support](https://aws.amazon.com/support) for further assistance\. 