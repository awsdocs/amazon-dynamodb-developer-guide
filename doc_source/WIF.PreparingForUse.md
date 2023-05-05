# Preparing to use web identity federation<a name="WIF.PreparingForUse"></a>

If you are an application developer and want to use web identity federation for your app, follow these steps:

1. **Sign up as a developer with a third\-party identity provider\.** The following external links provide information about signing up with supported identity providers:
   + [Login with Amazon Developer Center](http://login.amazon.com/)
   + [Registration](https://developers.facebook.com/docs/plugins/registration/) on the Facebook site
   + [Using OAuth 2\.0 to Access Google APIs](https://developers.google.com/accounts/docs/OAuth2) on the Google site

1. **Register your app with the identity provider\.** When you do this, the provider gives you an ID that's unique to your app\. If you want your app to work with multiple identity providers, you need to obtain an app ID from each provider\.

1. **Create one or more IAM roles\. **You need one role for each identity provider for each app\. For example, you might create a role that can be assumed by an app where the user signed in using Login with Amazon, a second role for the same app where the user has signed in using Facebook, and a third role for the app where users sign in using Google\.

   As part of the role creation process, you need to attach an IAM policy to the role\. Your policy document should define the DynamoDB resources required by your app, and the permissions for accessing those resources\.

For more information, see [About Web Identity Federation](https://docs.aws.amazon.com/IAM/latest/UserGuide/id_roles_providers_oidc.html) in *IAM User Guide*\. 

**Note**  
As an alternative to AWS Security Token Service, you can use Amazon Cognito\. Amazon Cognito is the preferred service for managing temporary credentials for mobile apps\. For more information, see the following pages:  
[How to Authenticate Users \(AWS Mobile SDK for iOS\)](https://docs.aws.amazon.com/mobile/sdkforios/developerguide/cognito-auth.html)
[How to Authenticate Users \(AWS Mobile SDK for Android\)](https://docs.aws.amazon.com/mobile/sdkforandroid/developerguide/cognito-auth.html)

## Generating an IAM policy using the DynamoDB console<a name="WIF.PreparingForUse.DDBConsole"></a>

The DynamoDB console can help you create an IAM policy for use with web identity federation\. To do this, you choose a DynamoDB table and specify the identity provider, actions, and attributes to be included in the policy\. The DynamoDB console then generates a policy that you can attach to an IAM role\.

1. Sign in to the AWS Management Console and open the DynamoDB console at [https://console\.aws\.amazon\.com/dynamodb/](https://console.aws.amazon.com/dynamodb/)\.

1. In the navigation pane, choose **Tables**\.

1. In the list of tables, choose the table for which you want to create the IAM policy\.

1. Choose the **Access control** tab\.

1. Choose the identity provider, actions, and attributes for the policy\.

   When the settings are as you want them, click **Create policy**\. The generated policy appears\.

1. Click **Attach policy instructions**, and follow the steps required to attach the generated policy to an IAM role\.