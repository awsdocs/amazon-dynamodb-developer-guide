# DynamoDB example application using the AWS SDK for Python \(Boto\): Tic\-tac\-toe<a name="TicTacToe"></a>

**Topics**
+ [Step 1: Deploy and test locally](TicTacToe.Phase1.md)
+ [Step 2: Examine the data model and implementation details](TicTacToe.Phase2.md)
+ [Step 3: Deploy in production using the DynamoDB service](TicTacToe.Phase3.md)
+ [Step 4: Clean up resources](TicTacToe.AppClosure.md)

The Tic\-Tac\-Toe game is an example web application built on Amazon DynamoDB\. The application uses the AWS SDK for Python \(Boto\) to make the necessary DynamoDB calls to store game data in a DynamoDB table, and the Python web framework Flask to illustrate end\-to\-end application development in DynamoDB, including how to model data\. It also demonstrates best practices when it comes to modeling data in DynamoDB, including the table you create for the game application, the primary key you define, additional indexes you need based on your query requirements, and the use of concatenated value attributes\.

You play the Tic\-Tac\-Toe application on the web as follows:

1. You log in to the application home page\.

1. You then invite another user to play the game as your opponent\.

   Until another user accepts your invitation, the game status remains as `PENDING`\. After an opponent accepts the invite, the game status changes to `IN_PROGRESS`\.

1. The game begins after the opponent logs in and accepts the invite\.

1. The application stores all game moves and status information in a DynamoDB table\.

1. The game ends with a win or a draw, which sets the game status to `FINISHED`\.

The end\-to\-end application building exercise is described in steps:
+ **[Step 1: Deploy and test locally](TicTacToe.Phase1.md)** – In this section, you download, deploy, and test the application on your local computer\. You will create the required tables in the downloadable version of DynamoDB\.
+ **[Step 2: Examine the data model and implementation details ](TicTacToe.Phase2.md)** – This section first describes in detail the data model, including the indexes and the use of the concatenated value attribute\. Then the section explains how the application works\. 
+ **[Step 3: Deploy in production using the DynamoDB service](TicTacToe.Phase3.md)** – This section focuses on deployment considerations in production\. In this step, you create a table using the Amazon DynamoDB service and deploy the application using AWS Elastic Beanstalk\. When you have the application in production, you also grant appropriate permissions so the application can access the DynamoDB table\. The instructions in this section walk you through the end\-to\-end production deployment\.
+ **[Step 4: Clean up resources](TicTacToe.AppClosure.md)** – This section highlights areas that are not covered by this example\. The section also provides steps for you to remove the AWS resources you created in the preceding steps so that you avoid incurring any charges\.