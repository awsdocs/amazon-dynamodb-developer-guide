# Writing Data To a Table<a name="SQLtoNoSQL.WriteData"></a>

Relational database tables contain *rows* of data\. Rows are composed of *columns*\. 

DynamoDB tables contain *items*\, which are composed of *attributes*\. This section describes how to write one row \(or item\) to a table\.

## SQL<a name="SQLtoNoSQL.WriteData.SQL"></a>

A table in a relational database is a two\-dimensional data structure composed of rows and columns\. Some database management systems also provide support for semistructured data, usually with native JSON or XML data types\. However, the implementation details vary among vendors\.

In SQL, you use the `INSERT` statement to add a row to a table.

```
INSERT INTO Music 
    (Artist, SongTitle, AlbumTitle, 
    Year, Price, Genre, 
    Tags)
VALUES(
    'No One You Know', 'Call Me Today', 'Somewhat Famous',
    2015, 2.14, 'Country',
    '{"Composers": ["Smith", "Jones", "Davis"],"LengthInSeconds": 214}'
);
```

The primary key for this table consists of *Artist* and *SongTitle*\. You must specify values for these columns\.

**Note**  
In this example table, we use the *Tags* column to store semistructured data about the songs in the *Music* table\. We have defined the *Tags* column as type TEXT, which can store up to 65535 characters in MySQL\. 

## DynamoDB<a name="SQLtoNoSQL.WriteData.DynamoDB"></a>

In Amazon DynamoDB, you use the `PutItem` action to add an item to a table.

```
{
    TableName: "Music",
    Item: {
        "Artist":"No One You Know",
        "SongTitle":"Call Me Today",
        "AlbumTitle":"Somewhat Famous",
        "Year": 2015,
        "Price": 2.14,
        "Genre": "Country",
        "Tags": {
            "Composers": [
                  "Smith",
                  "Jones",
                  "Davis"
            ],
            "LengthInSeconds": 214
        }
    }
}
```

The primary key for this table consists of *Artist* and *SongTitle*\. You must specify values for these attributes\.

Here are some key things to know about this `PutItem` example:
+ DynamoDB provides native support for documents, using JSON\. This makes DynamoDB ideal for storing semistructured data, such as *Tags*\. You also can retrieve and manipulate data from within JSON documents\.
+ The *Music* table does not have any predefined attributes, other than the primary key \(*Artist* and *SongTitle*\)\.
+ Most SQL databases are transaction oriented\. When you issue an `INSERT` statement, the data modifications are not permanent until you issue a `COMMIT` statement\. With Amazon DynamoDB, the effects of a `PutItem` action are permanent when DynamoDB replies with an HTTP 200 status code \(`OK`\)\.

**Note**  
For code samples using `PutItem`, see [Getting Started with DynamoDB](GettingStarted.md)\.

The following are some other `PutItem` examples\.

```
{
    TableName: "Music",
    Item: {
        "Artist": "No One You Know",
        "SongTitle": "My Dog Spot",
        "AlbumTitle":"Hey Now",
        "Price": 1.98,
        "Genre": "Country",
        "CriticRating": 8.4
    }
}
```

```
{
    TableName: "Music",
    Item: {
        "Artist": "No One You Know",
        "SongTitle": "Somewhere Down The Road",
        "AlbumTitle":"Somewhat Famous",
        "Genre": "Country",
        "CriticRating": 8.4,
        "Year": 1984
    }
}
```

```
{
    TableName: "Music",
    Item: {
        "Artist": "The Acme Band",
        "SongTitle": "Still In Love",
        "AlbumTitle":"The Buck Starts Here",
        "Price": 2.47,
        "Genre": "Rock",
        "PromotionInfo": {
            "RadioStationsPlaying":[
                 "KHCR", "KBQX", "WTNR", "WJJH"
            ],
            "TourDates": {
                "Seattle": "20150625",
                "Cleveland": "20150630"
            },
            "Rotation": "Heavy"
        }
    }
}
```

```
{
    TableName: "Music",
    Item: {
        "Artist": "The Acme Band",
        "SongTitle": "Look Out, World",
        "AlbumTitle":"The Buck Starts Here",
        "Price": 0.99,
        "Genre": "Rock"
    }
}
```

**Note**  
In addition to `PutItem`, DynamoDB supports a `BatchWriteItem` action for writing multiple items at the same time\.
