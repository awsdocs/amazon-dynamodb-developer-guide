# Step 4: Clean Up Resources<a name="TicTacToe.AppClosure"></a>

Now you have completed the Tic\-Tac\-Toe application deployment and testing\. The application covers end\-to\-end web application development on Amazon DynamoDB, except for user authentication\. The application uses the login information on the home page only to add a player name when creating a game\. In a production application, you would add the necessary code to perform user login and authentication\. 

If you are done testing, you can remove the resources you created to test the Tic\-Tac\-Toe application to avoid incurring any charges\.

**To remove resources you created**

1. Remove the Games table you created in DynamoDB\.

1. Terminate the Elastic Beanstalk environment to free up the Amazon EC2 instances\.

1. Delete the IAM role you created\.

1. Remove the object you created in Amazon S3\.