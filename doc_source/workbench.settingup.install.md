# Install NoSQL Workbench for DynamoDB<a name="workbench.settingup.install"></a>

 Follow these steps to install NoSQL Workbench and DynamoDB local on a supported platform\. 

------
#### [ Windows ]

**To install NoSQL Workbench on Windows**

1.  Run the NoSQL Workbench installer application and choose the setup language\. Then choose **OK** to begin the setup\. For more information about downloading NoSQL Workbench, see [Download NoSQL Workbench for DynamoDB](workbench.settingup.md)\. 

1.  Choose **Next** to continue the setup, and then choose **Next** on the following screen\. 

1.  By default, the **Install DynamoDB Local** check box is selected to include DynamoDB local as part of the installation\. Keeping this option selected ensures that DynamoDB local will be installed, and the destination path will be the same as the installation path of NoSQL Workbench\. Clearing the check box for this option will skip the installation of DynamoDB local, and the installation path will be for NoSQL Workbench only\. 

   Choose the destination where you want the software installed, and choose **Next**\. 
**Note**  
 If you opted to not include DynamoDB local as part of the setup, clear the **Install DynamoDB Local** check box, choose **Next**, and skip to step 8\. You can download DynamoDB local separately as a standalone installation at a later time\. For more information, see [Setting up DynamoDB local \(downloadable version\)](DynamoDBLocal.md)\.   
![\[\]](http://docs.aws.amazon.com/amazondynamodb/latest/developerguide/images/workbench/Install-Win1.png)

1.  Choose the Region for the download of DynamoDB local and choose **Next**\. 

1.  Configure your AWS account by selecting the **Yes, I would like to enter my AWS credentials** check box\. If you don't have an AWS account, or would like to set this up later, you can keep the check box clear and choose **Next**\.   
![\[\]](http://docs.aws.amazon.com/amazondynamodb/latest/developerguide/images/workbench/Install-Win2.png)

1.  You are shown a confirmation of your chosen AWS credentials\. Choose **OK** to continue\. 

1.  Choose the port number for DynamoDB local to use\. The default port is 8000\. After you enter the port number, choose **Next**\. 

1. Choose **Next** to begin setup\.

1. When the setup has completed, choose **Finish** to close the setup screen\.

------
#### [ macOS ]

**To install NoSQL Workbench on macOS**

1.  Run the NoSQL Workbench installer application and choose the setup language\. Then choose **OK** to begin the setup\. For more information about downloading NoSQL Workbench, see [Download NoSQL Workbench for DynamoDB](workbench.settingup.md)\. 

1.  Choose **Next** to continue the setup, and then choose **Next** on the following screen\. 

1.  By default, the **Install DynamoDB local** check box is selected to include DynamoDB local as part of the installation\. Keeping this option selected ensures that DynamoDB local will be installed, and the destination path will be the same as the installation path of NoSQL Workbench\. Clearing this option will skip the installation of DynamoDB local, and the installation path will be for NoSQL Workbench only\. 

   Choose the destination where you want the software installed, and choose **Next**\. 
**Note**  
 If you opted to not include DynamoDB local as part of the setup, clear the **Install DynamoDB local** check box, choose **Next**, and skip to step 8\. You can download DynamoDB local separately as a standalone installation at a later time\. For more information, see [Setting up DynamoDB local \(downloadable version\)](DynamoDBLocal.md)\.   
![\[\]](http://docs.aws.amazon.com/amazondynamodb/latest/developerguide/images/workbench/Install-Mac1.png)

1.  Select the Region for the download of DynamoDB local and choose **Next**\. 

1.  Configure your AWS account by selecting the **Yes, I would like to enter my AWS credentials** check box\. If you don't have an AWS account, or would like to set this up later, you can keep the check box clear and choose **Next**\.   
![\[\]](http://docs.aws.amazon.com/amazondynamodb/latest/developerguide/images/workbench/Install-Mac2.png)

1.  You are shown a confirmation of your chosen AWS credentials\. Choose **OK** to continue\. 

1.  Choose the port number for DynamoDB local to use\. The default port is 8000\. After you enter the port number, choose **Next**\. 

1. Choose **Next** to begin setup\.

1. When the setup has completed, choose **Finish** to close the setup screen\.

**Note**  
 NoSQL Workbench for macOS performs auto\-updates\. To get notification about updates, enable notification access to NoSQL Workbench in System Preferences > Notifications\. 

------
#### [ Linux ]

**To install NoSQL Workbench on Linux**

1.  Run the NoSQL Workbench installer application and choose the setup language\. Then choose **OK** to begin the setup\. For more information about downloading NoSQL Workbench, see [Download NoSQL Workbench for DynamoDB](workbench.settingup.md)\. 

1.  Choose **Forward** to continue the setup, and choose **Forward** on the following screen\. 

1.  By default, the **Install DynamoDB local** check box is selected to include DynamoDB local as part of the installation\. Keeping this option selected ensures that DynamoDB local will be installed, and the destination path will be the same as the installation path of NoSQL Workbench\. Clearing this option will skip the installation of DynamoDB local, and the installation path will be for NoSQL Workbench only\. 

   Choose the destination where you want the software installed, and choose **Forward**\. 
**Note**  
 If you opted to not include DynamoDB local as part of the setup, clear the **Install DynamoDB local** check box, choose **Forward**, and skip to step 8\. You can download DynamoDB local separately as a standalone installation at a later time\. For more information, see [Setting up DynamoDB local \(downloadable version\)](DynamoDBLocal.md)\.   
![\[\]](http://docs.aws.amazon.com/amazondynamodb/latest/developerguide/images/workbench/Install-Linux1.png)

1.  Select the Region for the download of DynamoDB local and choose **Forward**\. 

1.  Configure your AWS account by selecting the **Yes, I would like to enter my AWS credentials** check box\. If you don't have an AWS account, or would like to set this up later, you can keep the check box clear and choose **Forward**\.   
![\[\]](http://docs.aws.amazon.com/amazondynamodb/latest/developerguide/images/workbench/Install-Linux2.png)

1.  You will be shown a confirmation of your chosen AWS credentials\. Choose **OK** to continue\. 

1.  Choose the port number for DynamoDB local to use\. The default port is 8000\. After you enter the port number entered, choose **Forward**\. 

1. Choose **Forward** to begin setup\.

1. When the setup has completed, choose **Finish** to close the setup screen\.

------

**Note**  
 If you opted to install DynamoDB local as part of the installation of NoSQL Workbench, DynamoDB local will be preconfigured with default options\. To edit the default options, modify the *DDBLocalStart* script located in the */resources/DDBLocal\_Scripts/* directory\. You can find this in the path that you provided during installation\. To learn more about DynamoDB local options, see [DynamoDB local usage notes](DynamoDBLocal.UsageNotes.md)\. 

 If you opted to install DynamoDB local as part of the NoSQL Workbench installation, you will have access to a toggle to enable and disable DynamoDB local as shown in the following image\. 

![\[Image NOT FOUND\]](http://docs.aws.amazon.com/amazondynamodb/latest/developerguide/images/workbench/WorkbenchToggle.png)