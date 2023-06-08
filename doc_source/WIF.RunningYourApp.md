# Writing your app to use web identity federation<a name="WIF.RunningYourApp"></a>

To use web identity federation, your app must assume the IAM role that you created\. From that point on, the app honors the access policy that you attached to the role\.

At runtime, if your app uses web identity federation, it must follow these steps:

1. **Authenticate with a third\-party identity provider\.** Your app must call the identity provider using an interface that they provide\. The exact way in which you authenticate the user depends on the provider and on what platform your app is running\. Typically, if the user is not already signed in, the identity provider takes care of displaying a sign\-in page for that provider\.

   After the identity provider authenticates the user, the provider returns a web identity token to your app\. The format of this token depends on the provider, but is typically a very long string of characters\.

1. **Obtain temporary AWS security credentials\.** To do this, your app sends a `AssumeRoleWithWebIdentity` request to AWS Security Token Service \(AWS STS\)\. This request contains the following:
   + The web identity token from the previous step
   + The app ID from the identity provider
   + The Amazon Resource Name \(ARN\) of the IAM role that you created for this identity provider for this app

   AWS STS returns a set of AWS security credentials that expire after a certain amount of time \(3,600 seconds, by default\)\.

   The following is a sample request and response from a `AssumeRoleWithWebIdentity` action in AWS STS\. The web identity token was obtained from the Login with Amazon identity provider\.

   ```
   GET / HTTP/1.1
   Host: sts.amazonaws.com
   Content-Type: application/json; charset=utf-8
   URL: https://sts.amazonaws.com/?ProviderId=www.amazon.com
   &DurationSeconds=900&Action=AssumeRoleWithWebIdentity
   &Version=2011-06-15&RoleSessionName=web-identity-federation
   &RoleArn=arn:aws:iam::123456789012:role/GameRole
   &WebIdentityToken=Atza|IQEBLjAsAhQluyKqyBiYZ8-kclvGTYM81e...(remaining characters omitted)
   ```

   

   ```
   <AssumeRoleWithWebIdentityResponse
     xmlns="https://sts.amazonaws.com/doc/2011-06-15/">
     <AssumeRoleWithWebIdentityResult>
       <SubjectFromWebIdentityToken>amzn1.account.AGJZDKHJKAUUSW6C44CHPEXAMPLE</SubjectFromWebIdentityToken>
       <Credentials>
         <SessionToken>AQoDYXdzEMf//////////wEa8AP6nNDwcSLnf+cHupC...(remaining characters omitted)</SessionToken>
         <SecretAccessKey>8Jhi60+EWUUbbUShTEsjTxqQtM8UKvsM6XAjdA==</SecretAccessKey>
         <Expiration>2013-10-01T22:14:35Z</Expiration>
         <AccessKeyId>06198791C436IEXAMPLE</AccessKeyId>
       </Credentials>
       <AssumedRoleUser>
         <Arn>arn:aws:sts::123456789012:assumed-role/GameRole/web-identity-federation</Arn>
         <AssumedRoleId>AROAJU4SA2VW5SZRF2YMG:web-identity-federation</AssumedRoleId>
       </AssumedRoleUser>
     </AssumeRoleWithWebIdentityResult>
     <ResponseMetadata>
       <RequestId>c265ac8e-2ae4-11e3-8775-6969323a932d</RequestId>
     </ResponseMetadata>
   </AssumeRoleWithWebIdentityResponse>
   ```

1. **Access AWS resources\.** The response from AWS STS contains information that your app requires in order to access DynamoDB resources:
   + The `AccessKeyID`, `SecretAccessKey`, and `SessionToken` fields contain security credentials that are valid for this user and this app only\. 
   + The `Expiration` field signifies the time limit for these credentials, after which they are no longer valid\.
   + The `AssumedRoleId` field contains the name of a session\-specific IAM role that has been assumed by the app\. The app honors the access controls in the IAM policy document for the duration of this session\.
   + The `SubjectFromWebIdentityToken` field contains the unique ID that appears in an IAM policy variable for this particular identity provider\. The following are the IAM policy variables for supported providers, and some example values for them:  
****    
[\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/amazondynamodb/latest/developerguide/WIF.RunningYourApp.html)

For example IAM policies where these policy variables are used, see [Example policies: Using conditions for fine\-grained access control](specifying-conditions.md#FGAC_DDB.Examples)\.

For more information about how AWS STS generates temporary access credentials, see [Requesting Temporary Security Credentials](https://docs.aws.amazon.com/IAM/latest/UserGuide/id_credentials_temp_request.html) in *IAM User Guide*\.