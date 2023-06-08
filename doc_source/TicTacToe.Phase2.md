# Step 2: Examine the data model and implementation details<a name="TicTacToe.Phase2"></a>

**Topics**
+ [2\.1: Basic data model](#TicTacToe.Phase2.DataModel)
+ [2\.2: Application in action \(code walkthrough\)](#TicTacToe.Phase2.AppInAction)

## 2\.1: Basic data model<a name="TicTacToe.Phase2.DataModel"></a>

This example application highlights the following DynamoDB data model concepts:
+ ****Table**** – In DynamoDB, a table is a collection of items \(that is, records\), and each item is a collection of name\-value pairs called attributes\.

  In this Tic\-Tac\-Toe example, the application stores all game data in a table, `Games`\. The application creates one item in the table per game and stores all game data as attributes\. A tic\-tac\-toe game can have up to nine moves\. Because DynamoDB tables do not have a schema in cases where only the primary key is the required attribute, the application can store varying number of attributes per game item\.

  The `Games` table has a simple primary key made of one attribute, `GameId`, of string type\. The application assigns a unique ID to each game\. For more information on DynamoDB primary keys, see [Primary key](HowItWorks.CoreComponents.md#HowItWorks.CoreComponents.PrimaryKey)\. 

  When a user initiates a tic\-tac\-toe game by inviting another user to play, the application creates a new item in the `Games` table with attributes storing game metadata, such as the following:
  + `HostId`, the user who initiated the game\.
  + `Opponent`, the user who was invited to play\.
  + The user whose turn it is to play\. The user who initiated the game plays first\.
  + The user who uses the **O** symbol on the board\. The user who initiates the games uses the **O** symbol\.

  In addition, the application creates a `StatusDate` concatenated attribute, marking the initial game state as `PENDING`\. The following screenshot shows an example item as it appears in the DynamoDB console:  
![\[Console screenshot of the attributes table.\]](http://docs.aws.amazon.com/amazondynamodb/latest/developerguide/images/tic-tac-toe-10.png)![\[Console screenshot of the attributes table.\]](http://docs.aws.amazon.com/amazondynamodb/latest/developerguide/)![\[Console screenshot of the attributes table.\]](http://docs.aws.amazon.com/amazondynamodb/latest/developerguide/)

  As the game progresses, the application adds one attribute to the table for each game move\. The attribute name is the board position, for example `TopLeft` or `BottomRight`\. For example, a move might have a `TopLeft` attribute with the value `O`, a `TopRight` attribute with the value `O`, and a `BottomRight` attribute with the value `X`\. The attribute value is either `O` or `X`, depending on which user made the move\. For example, consider the following board\.  
![\[Screenshot showing a finished tic-tac-toe game that ended in a tie.\]](http://docs.aws.amazon.com/amazondynamodb/latest/developerguide/images/tic-tac-toe-30.png)![\[Screenshot showing a finished tic-tac-toe game that ended in a tie.\]](http://docs.aws.amazon.com/amazondynamodb/latest/developerguide/)![\[Screenshot showing a finished tic-tac-toe game that ended in a tie.\]](http://docs.aws.amazon.com/amazondynamodb/latest/developerguide/)
+ ****Concatenated value attributes**** – The `StatusDate` attribute illustrates a concatenated value attribute\. In this approach, instead of creating separate attributes to store game status \(`PENDING`, `IN_PROGRESS`, and `FINISHED`\) and date \(when the last move was made\), you combine them as single attribute, for example `IN_PROGRESS_2014-04-30 10:20:32`\.

  The application then uses the `StatusDate` attribute in creating secondary indexes by specifying `StatusDate` as a sort key for the index\. The benefit of using the `StatusDate` concatenated value attribute is further illustrated in the indexes discussed next\.
+ ****Global secondary indexes**** – You can use the table's primary key, `GameId`, to efficiently query the table to find a game item\. To query the table on attributes other than the primary key attributes, DynamoDB supports the creation of secondary indexes\. In this example application, you build the following two secondary indexes:   
![\[Screenshot showing the hostStatusDate and oppStatusDate global secondary indexes created in the example application.\]](http://docs.aws.amazon.com/amazondynamodb/latest/developerguide/images/tic-tac-toe-indexes-10.png)![\[Screenshot showing the hostStatusDate and oppStatusDate global secondary indexes created in the example application.\]](http://docs.aws.amazon.com/amazondynamodb/latest/developerguide/)![\[Screenshot showing the hostStatusDate and oppStatusDate global secondary indexes created in the example application.\]](http://docs.aws.amazon.com/amazondynamodb/latest/developerguide/)
  + **HostId\-StatusDate\-index**\. This index has `HostId` as a partition key and `StatusDate` as a sort key\. You can use this index to query on `HostId`, for example to find games hosted by a particular user\. 
  + **OpponentId\-StatusDate\-index**\. This index has `OpponentId` as a partition key and `StatusDate` as a sort key\. You can use this index to query on `Opponent`, for example to find games where a particular user is the opponent\.

  These indexes are called global secondary indexes because the partition key in these indexes is not the same the partition key \(`GameId`\), used in the primary key of the table\. 

  Note that both the indexes specify `StatusDate` as a sort key\. Doing this enables the following:
  + You can query using the `BEGINS_WITH` comparison operator\. For example, you can find all games with the `IN_PROGRESS` attribute hosted by a particular user\. In this case, the `BEGINS_WITH` operator checks for the `StatusDate` value that begins with `IN_PROGRESS`\.
  + DynamoDB stores the items in the index in sorted order, by sort key value\. So if all status prefixes are the same \(for example, `IN_PROGRESS`\), the ISO format used for the date part will have items sorted from oldest to the newest\. This approach enables certain queries to be performed efficiently, for example the following: 
    + Retrieve up to 10 of the most recent `IN_PROGRESS` games hosted by the user who is logged in\. For this query, you specify the `HostId-StatusDate-index` index\.
    + Retrieve up to 10 of the most recent `IN_PROGRESS` games where the user logged in is the opponent\. For this query, you specify the `OpponentId-StatusDate-index` index\.

For more information about secondary indexes, see [Improving data access with secondary indexes](SecondaryIndexes.md)\.

## 2\.2: Application in action \(code walkthrough\)<a name="TicTacToe.Phase2.AppInAction"></a>

This application has two main pages:
+ ****Home page**** – This page provides the user a simple login, a **CREATE** button to create a new tic\-tac\-toe game, a list of games in progress, game history, and any active pending game invitations\. 

  The home page is not refreshed automatically; you must refresh the page to refresh the lists\.
+ ****Game page**** – This page shows the tic\-tac\-toe grid where users play\. 

  The application updates the game page automatically every second\. The JavaScript in your browser calls the Python web server every second to query the Games table whether the game items in the table have changed\. If they have, JavaScript triggers a page refresh so that the user sees the updated board\.

Let us see in detail how the application works\.

### Home page<a name="TicTacToe.Phase2.AppInAction.HomePage"></a>

After the user logs in, the application displays the following three lists of information\.

![\[Screenshot showing the application home page with 3 lists: pending invitations, games in progress, and recent history.\]](http://docs.aws.amazon.com/amazondynamodb/latest/developerguide/images/tic-tac-toe-homepage-10.png)![\[Screenshot showing the application home page with 3 lists: pending invitations, games in progress, and recent history.\]](http://docs.aws.amazon.com/amazondynamodb/latest/developerguide/)![\[Screenshot showing the application home page with 3 lists: pending invitations, games in progress, and recent history.\]](http://docs.aws.amazon.com/amazondynamodb/latest/developerguide/)
+ ****Invitations**** – This list shows up to the 10 most recent invitations from others that are pending acceptance by the user who is logged in\. In the preceding screenshot, user1 has invitations from user5 and user2 pending\.
+ ****Games in\-progress**** – This list shows up to the 10 most recent games that are in progress\. These are games that the user is actively playing, which have the status `IN_PROGRESS`\. In the screenshot, user1 is actively playing a tic\-tac\-toe game with user3 and user4\.
+ ****Recent history**** – This list shows up to the 10 most recent games that the user finished, which have the status `FINISHED`\. In game shown in the screenshot, user1 has previously played with user2\. For each completed game, the list shows the game result\.

In the code, the `index` function \(in `application.py`\) makes the following three calls to retrieve game status information:

```
inviteGames     = controller.getGameInvites(session["username"])
inProgressGames = controller.getGamesWithStatus(session["username"], "IN_PROGRESS")
finishedGames   = controller.getGamesWithStatus(session["username"], "FINISHED")
```

Each of these calls returns a list of items from DynamoDB that are wrapped by the `Game` objects\. It is easy to extract data from these objects in the view\. The index function passes these object lists to the view to render the HTML\.

```
return render_template("index.html",
                       user=session["username"],
                       invites=inviteGames,
                       inprogress=inProgressGames,
                       finished=finishedGames)
```

The Tic\-Tac\-Toe application defines the `Game` class primarily to store game data retrieved from DynamoDB\. These functions return lists of `Game` objects that enable you to isolate the rest of the application from code related to Amazon DynamoDB items\. Thus, these functions help you decouple your application code from the details of the data store layer\. 

The architectural pattern described here is also referred as the model\-view\-controller \(MVC\) UI pattern\. In this case, the `Game` object instances \(representing data\) are the model, and the HTML page is the view\. The controller is divided into two files\. The `application.py` file has the controller logic for the Flask framework, and the business logic is isolated in the `gameController.py` file\. That is, the application stores everything that has to do with DynamoDB SDK in its own separate file in the `dynamodb` folder\.

Let us review the three functions and how they query the Games table using global secondary indexes to retrieve relevant data\.

#### Using getGameInvites to get the list of pending game invitations<a name="TicTacToe.Phase2.GameInAction.ListInvitations"></a>

The `getGameInvites` function retrieves the list of the 10 most recent pending invitations\. These games have been created by users, but the opponents have not accepted the game invitations\. For these games, the status remains `PENDING` until the opponent accepts the invite\. If the opponent declines the invite, the application removes the corresponding item from the table\. 

The function specifies the query as follows:
+ It specifies the `OpponentId-StatusDate-index` index to use with the following index key values and comparison operators:
  + The partition key is `OpponentId` and takes the index key `user ID`\.
  + The sort key is `StatusDate` and takes the comparison operator and index key value `beginswith="PENDING_"`\.

  You use the `OpponentId-StatusDate-index` index to retrieve games to which the logged\-in user is invited—that is, where the logged\-in user is the opponent\.
+ The query limits the result to 10 items\.

```
gameInvitesIndex = self.cm.getGamesTable().query(
                                            Opponent__eq=user,
                                            StatusDate__beginswith="PENDING_",
                                            index="OpponentId-StatusDate-index",
                                            limit=10)
```

In the index, for each `OpponentId` \(the partition key\) DynamoDB keeps items sorted by `StatusDate` \(the sort key\)\. Therefore, the games that the query returns will be the 10 most recent games\.

#### Using getGamesWithStatus to get the list of games with a specific status<a name="TicTacToe.Phase2.GameInAction.ListGamesInProgressHistory"></a>

After an opponent accepts a game invitation, the game status changes to `IN_PROGRESS`\. After the game completes, the status changes to `FINISHED`\. 

Queries to find games that are either in progress or finished are the same except for the different status value\. Therefore, the application defines the `getGamesWithStatus` function, which takes the status value as a parameter\. 

```
inProgressGames = controller.getGamesWithStatus(session["username"], "IN_PROGRESS")
finishedGames   = controller.getGamesWithStatus(session["username"], "FINISHED")
```

The following section discusses in\-progress games, but the same description also applies to finished games\.

A list of in\-progress games for a given user includes both the following: 
+ In\-progress games hosted by the user 
+ In\-progress games where the user is the opponent 

The `getGamesWithStatus` function runs the following two queries, each time using the appropriate secondary index\. 
+ The function queries the `Games` table using the `HostId-StatusDate-index` index\. For the index, the query specifies primary key values—both the partition key \(`HostId`\) and sort key \(`StatusDate`\) values, along with comparison operators\. 

  ```
  hostGamesInProgress = self.cm.getGamesTable ().query(HostId__eq=user,
                                                 StatusDate__beginswith=status,
                                                 index="HostId-StatusDate-index",
                                                 limit=10)
  ```

  Note the Python syntax for comparison operators:
  + `HostId__eq=user` specifies the equality comparison operator\.
  + `StatusDate__beginswith=status` specifies the `BEGINS_WITH` comparison operator\.
+ The function queries the `Games` table using the `OpponentId-StatusDate-index` index\. 

  ```
  oppGamesInProgress = self.cm.getGamesTable().query(Opponent__eq=user,
                                               StatusDate__beginswith=status,
                                               index="OpponentId-StatusDate-index",
                                               limit=10)
  ```
+ The function then combines the two lists, sorts, and for the first 0 to 10 items creates a list of the `Game` objects and returns the list to the calling function \(that is, the index\)\.

  ```
  games = self.mergeQueries(hostGamesInProgress,
                          oppGamesInProgress)
  return games
  ```

### Game page<a name="TicTacToe.Phase2.AppInAction.GamePage"></a>

The game page is where the user plays tic\-tac\-toe games\. It shows the game grid along with game\-relevant information\. The following screenshot shows an example game in progress:

![\[Screenshot showing a tic-tac-toe game in progress.\]](http://docs.aws.amazon.com/amazondynamodb/latest/developerguide/images/tic-tac-toe-example-board-10.png)![\[Screenshot showing a tic-tac-toe game in progress.\]](http://docs.aws.amazon.com/amazondynamodb/latest/developerguide/)![\[Screenshot showing a tic-tac-toe game in progress.\]](http://docs.aws.amazon.com/amazondynamodb/latest/developerguide/)

The application displays the game page in the following situations:
+ The user creates a game inviting another user to play\. 

  In this case, the page shows the user as host and the game status as `PENDING`, waiting for the opponent to accept\.
+ The user accepts one of the pending invitations on the home page\. 

  In this case, the page shows the user as the opponent and game status as `IN_PROGRESS`\.

A user selection on the board generates a form `POST` request to the application\. That is, Flask calls the `selectSquare` function \(in `application.py`\) with the HTML form data\. This function, in turn, calls the `updateBoardAndTurn` function \(in `gameController.py`\) to update the game item as follows:
+ It adds a new attribute specific to the move\.
+ It updates the `Turn` attribute value to the user whose turn is next\.

```
controller.updateBoardAndTurn(item, value, session["username"])
```

The function returns true if the item update was successful; otherwise, it returns false\. Note the following about the `updateBoardAndTurn` function:
+ The function calls the `update_item` function of the SDK for Python to make a finite set of updates to an existing item\. The function maps to the `UpdateItem` operation in DynamoDB\. For more information, see [UpdateItem](https://docs.aws.amazon.com/amazondynamodb/latest/APIReference/API_UpdateItem.html)\. 
**Note**  
The difference between the `UpdateItem` and `PutItem` operations is that `PutItem` replaces the entire item\. For more information, see [PutItem](https://docs.aws.amazon.com/amazondynamodb/latest/APIReference/API_PutItem.html)\.

For the `update_item` call, the code identifies the following:
+ The primary key of the `Games` table \(that is, `ItemId`\)\.

  ```
  key = { "GameId" : { "S" : gameId } }
  ```
+ The new attribute to add, specific to the current user move, and its value \(for example, `TopLeft="X"`\)\.

  ```
  attributeUpdates = {
      position : {
          "Action" : "PUT",
          "Value" : { "S" : representation }
      }
  }
  ```
+ Conditions that must be true for the update to take place:
  + The game must be in progress\. That is, the `StatusDate` attribute value must begin with `IN_PROGRESS`\.
  + The current turn must be a valid user turn as specified by the `Turn` attribute\. 
  + The square that the user chose must be available\. That is, the attribute corresponding to the square must not exist\.

  ```
  expectations = {"StatusDate" : {"AttributeValueList": [{"S" : "IN_PROGRESS_"}],
      "ComparisonOperator": "BEGINS_WITH"},
      "Turn" : {"Value" : {"S" : current_player}},
      position : {"Exists" : False}}
  ```

Now the function calls `update_item` to update the item\.

```
self.cm.db.update_item("Games", key=key,
    attribute_updates=attributeUpdates,
    expected=expectations)
```

After the function returns, the `selectSquare` function calls redirect, as shown in the following example\.

```
redirect("/game="+gameId) 
```

This call causes the browser to refresh\. As part of this refresh, the application checks to see if the game has ended in a win or draw\. If it has, the application updates the game item accordingly\. 