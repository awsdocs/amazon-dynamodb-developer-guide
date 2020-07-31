# Working with User Preferences<a name="user-preferences"></a>

You can configure some of the default settings in the Amazon DynamoDB console\. For example, you can change the default query type that is used when you access the **Items** tab\. If you're signed in to the console as an IAM user, you can store information about how you prefer to use the console\. This information, also known as your *user preferences*, is stored and applied every time you use the console\. Any time you access the DynamoDB console, these preferences are applied to all tables in all AWS Regions for your IAM user\. They are not table\-specific or Region\-specific\. They don't affect your interactions with the AWS CLI, DynamoDB API, or other services that interact with DynamoDB\.

You can still change individual settings on console pages without having saved any user preferences\. Those choices persist until you close the console window\. When you return to the console, any saved user preferences are applied\.

**Note**  
User preferences are available only for IAM users\. You can't set preferences if you use federated access, temporary access, or an AWS account root user to access the console\.

User preferences include the following:
+ **Table detail view mode:** View all the table\-specific information vertically, horizontally, or covering the full screen \(if enabled, the navigation bar still appears\)\. 
+ **Show navigation bar:** Enable this option to show the navigation bar on the left side \(expanded\)\. Disable it to automatically collapse the navigation bar \(you can expand it using the right chevron\)\. 
+ **Default entry page \(Dashboard or Tables\):** Choose the page that loads when you access DynamoDB\. This option automatically loads the Dashboard or the Tables page, respectively\.
+ **Items editor mode \(Tree or Text\):** Choose the default editor mode to use when you create or edit an item\. 
+ **Items default query type \(Scan or Query\):** Choose the default query type to use when you access the **Items** tab\. Choose **Scan** if you want to either enable or disable the automatic scan operation that occurs when accessing the **Items** tab\. 
+ **Automatic scan operation when accessing the items tab:** If **Scan** is the default query type for items and you enable this setting, an automatic scan operation occurs when you access the **Items** tab\. If you disable this setting, you can perform a scan by choosing **Start search** on the **Items** tab\. 

## View and Save User Preferences<a name="user-preferences-how-to"></a>

You can view and change your user preferences for the DynamoDB console\. These settings apply only to your IAM user in the DynamoDB console\. They don't affect other IAM users in your AWS account\. 

**To view and save preferences on the DynamoDB console for your IAM user**

1. Open the DynamoDB console at [https://console\.aws\.amazon\.com/dynamodb/](https://console.aws.amazon.com/dynamodb/)\.

   Sign in as an IAM user\. You can't configure user preferences for other user types\.

1. In the page navigation, choose **Preferences**\.

1. In **Preferences**, configure your preferences\.

   Do one of the following:
   + To save and apply your changes, choose **Save**\.
   + To view the DynamoDB console default settings, choose **Restore**\. These defaults are applied if you choose **Save**\. 

     The default settings are as follows:
     + **Table detail view mode:** Vertical
     + **Show navigation bar:** Yes
     + **Default entry page:** Dashboard
     + **Items editor mode:** Tree
     + **Items default query type:** Scan
     + **Automatic scan operation when accessing the items tab:** Yes
   + To return to the console page that you were on previously, choose **Back**\. Or, choose **Dashboard** to go to the DynamoDB Dashboard page\.