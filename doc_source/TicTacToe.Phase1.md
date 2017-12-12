# Step 1: Deploy and Test Locally<a name="TicTacToe.Phase1"></a>


+ [1\.1: Download and Install Required Packages](#TicTacToe.Phase1.InstallApp)
+ [1\.2: Test the Game Application](#TicTacToe.RunAppLocally)

In this step you download, deploy, and test the Tic\-Tac\-Toe game application on your local computer\. Instead of using the Amazon DynamoDB web service, you will download DynamoDB to your computer, and create the required table there\. 

## 1\.1: Download and Install Required Packages<a name="TicTacToe.Phase1.InstallApp"></a>

You will need the following to test this application locally:

+ Python 

+ Flask \(a microframework for Python\)

+ AWS SDK for Python \(Boto\)

+ DynamoDB running on your computer

+ Git 

To get these tools, do the following:

1. Install Python\. For step\-by\-step instructions, go to [Download Python](https://www.python.org/downloads/)\. 

   The Tic\-Tac\-Toe application has been tested using Python version 2\.7\. 

1. Install Flask and AWS SDK for Python \(Boto\) using the Python Package Installer \(PIP\):

   + Install PIP\. 

     For instructions, go to [Install PIP](http://pip.readthedocs.org/en/stable/installing/)\. On the installation page, choose the **get\-pip\.py** link, and then save the file\. Then open a command terminal as an administrator, and type the following at the command prompt:

     ```
     python.exe get-pip.py
     ```

      On Linux, you don't specify the `.exe` extension\. You only specify `python get-pip.py`\. 

   + Using PIP, install the Flask and Boto packages using the following code:

     ```
     pip install Flask
     pip install boto
     pip install configparser
     ```

1. Download DynamoDB to your computer\. For instructions on how to run it, see [Setting Up DynamoDB Local \(Downloadable Version\)](DynamoDBLocal.md)\.

1. Download the Tic\-Tac\-Toe application:

   1. Install Git\. For instructions, go to [git Downloads](http://git-scm.com/downloads)\.

   1. Execute the following code to download the application:

      ```
      git clone https://github.com/awslabs/dynamodb-tictactoe-example-app.git
      ```

## 1\.2: Test the Game Application<a name="TicTacToe.RunAppLocally"></a>

To test the Tic\-Tac\-Toe application, you need to run DynamoDB locally on your computer\. 

**To run the Tic\-Tac\-Toe application**

1. Start DynamoDB\. 

1. Start the web server for the Tic\-Tac\-Toe application\. 

   To do so, open a command terminal, navigate to the folder where you downloaded the Tic\-Tac\-Toe application, and run the application locally using the following code:

   ```
   python.exe application.py --mode local --serverPort 5000 --port 8000  
   ```

   On Linux, you don't specify the `.exe` extension\. 

1. Open your web browser, and type the following:

   ```
   http://localhost:5000/ 
   ```

   The browser shows the home page:  
![\[Image NOT FOUND\]](http://docs.aws.amazon.com/amazondynamodb/latest/developerguide/)![\[Image NOT FOUND\]](http://docs.aws.amazon.com/amazondynamodb/latest/developerguide/)![\[Image NOT FOUND\]](http://docs.aws.amazon.com/amazondynamodb/latest/developerguide/)

1. Type **user1** in the **Log in** box to log in as user1\.
**Note**  
This example application does not perform any user authentication\. The user ID is only used to identify players\. If two players log in with the same alias, the application works as if you are playing in two different browsers\.

1. If this is your first time playing the game, a page appears requesting you to create the required table \(Games\) in DynamoDB\. Choose **CREATE TABLE**\.  
![\[Image NOT FOUND\]](http://docs.aws.amazon.com/amazondynamodb/latest/developerguide/)![\[Image NOT FOUND\]](http://docs.aws.amazon.com/amazondynamodb/latest/developerguide/)![\[Image NOT FOUND\]](http://docs.aws.amazon.com/amazondynamodb/latest/developerguide/)

1. Choose **CREATE** to create the first tic\-tac\-toe game\.

1. Type **user2** in the **Choose an Opponent** box, and choose **Create Game\!**  
![\[Image NOT FOUND\]](http://docs.aws.amazon.com/amazondynamodb/latest/developerguide/)![\[Image NOT FOUND\]](http://docs.aws.amazon.com/amazondynamodb/latest/developerguide/)![\[Image NOT FOUND\]](http://docs.aws.amazon.com/amazondynamodb/latest/developerguide/)

   Doing this creates the game by adding an item in the Games table\. It sets the game status to `PENDING`\. 

1. Open another browser window, and type the following\.

   ```
   http://localhost:5000/ 
   ```

   The browser passes information through cookies, so you should use incognito mode or private browsing so that your cookies don't carry over\.

1. Log in as user2\.

   A page appears that shows a pending invitation from user1\.   
![\[Image NOT FOUND\]](http://docs.aws.amazon.com/amazondynamodb/latest/developerguide/)![\[Image NOT FOUND\]](http://docs.aws.amazon.com/amazondynamodb/latest/developerguide/)![\[Image NOT FOUND\]](http://docs.aws.amazon.com/amazondynamodb/latest/developerguide/)

1. Choose **accept** to accept the invitation\.   
![\[Image NOT FOUND\]](http://docs.aws.amazon.com/amazondynamodb/latest/developerguide/)![\[Image NOT FOUND\]](http://docs.aws.amazon.com/amazondynamodb/latest/developerguide/)![\[Image NOT FOUND\]](http://docs.aws.amazon.com/amazondynamodb/latest/developerguide/)

   The game page appears with an empty tic\-tac\-toe grid\. The page also shows relevant game information such as the game ID, whose turn it is, and game status\. 

1. Play the game\.

For each user move, the web service sends a request to DynamoDB to conditionally update the game item in the Games table\. For example, the conditions ensure the move is valid, the square the user chose is available, and it was the turn of the user who made the move\. For a valid move, the update operation adds a new attribute corresponding to the selection on the board\. The update operation also sets the value of the existing attribute to the user who can make the next move\. 

On the game page, the application makes asynchronous JavaScript calls every second, for up to five minutes, to check if the game state in DynamoDB has changed\. If it has, the application updates the page with new information\. After five minutes, the application stops making the requests and you need to refresh the page to get updated information\.