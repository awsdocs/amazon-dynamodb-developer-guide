# Object Persistence Interface<a name="Programming.SDKs.Interfaces.Mapper"></a>

Some AWS SDKs provide an object persistence interface where you do not directly perform data plane operations\. Instead, you create objects that represent items in Amazon DynamoDB tables and indexes, and interact only with those objects\. This allows you to write object\-centric code, rather than database\-centric code\.

**Note**  
Object persistence interfaces are available in the AWS SDKs for Java and \.NET\. For more information, see [Higher\-Level Programming Interfaces for DynamoDB](HigherLevelInterfaces.md)\.

The following Java program uses `DynamoDBMapper`, the object persistence interface of the AWS SDK for Java\. The `MusicItem` class represents an item in the `Music` table\.

```
package com.amazonaws.codesamples;

import com.amazonaws.services.dynamodbv2.datamodeling.DynamoDBAttribute;
import com.amazonaws.services.dynamodbv2.datamodeling.DynamoDBHashKey;
import com.amazonaws.services.dynamodbv2.datamodeling.DynamoDBRangeKey;
import com.amazonaws.services.dynamodbv2.datamodeling.DynamoDBTable;

@DynamoDBTable(tableName="Music")
public class MusicItem {
    private String artist;
    private String songTitle;
    private String albumTitle;
    private int year;

    @DynamoDBHashKey(attributeName="Artist")
    public String getArtist() { return artist;}
    public void setArtist(String artist) {this.artist = artist;}

    @DynamoDBRangeKey(attributeName="SongTitle")
    public String getSongTitle() { return songTitle;}
    public void setSongTitle(String songTitle) {this.songTitle = songTitle;}

    @DynamoDBAttribute(attributeName = "AlbumTitle")
    public String getAlbumTitle() { return albumTitle;}
    public void setAlbumTitle(String albumTitle) {this.albumTitle = albumTitle;}

    @DynamoDBAttribute(attributeName = "Year")
    public int getYear() { return year; }
    public void setYear(int year) { this.year = year; }
}
```

You can then instantiate a `MusicItem` object, and retrieve a song using the `load()` method of `DynamoDBMapper`\. The program then prints the year that the song was released\.

The `com.amazonaws.services.dynamodbv2.datamodeling.DynamoDBMapper` class implements the DynamoDB object persistence interface\. Note how `DynamoDBMapper` acts as a wrapper around the low\-level client \(`AmazonDynamoDB`\)\.

```
package com.amazonaws.codesamples;

import com.amazonaws.services.dynamodbv2.AmazonDynamoDB;
import com.amazonaws.services.dynamodbv2.AmazonDynamoDBClientBuilder;
import com.amazonaws.services.dynamodbv2.datamodeling.DynamoDBMapper;


public class MusicMapperDemo {

    public static void main(String[] args) {

        AmazonDynamoDB client = AmazonDynamoDBClientBuilder.standard().build();

        DynamoDBMapper mapper = new DynamoDBMapper(client);

        MusicItem keySchema = new MusicItem();
        keySchema.setArtist("No One You Know");
        keySchema.setSongTitle("Call Me Today");

        try {
            MusicItem result = mapper.load(keySchema);
            if (result != null) {
                System.out.println(
                "The song was released in "+ result.getYear());
            } else {
                System.out.println("No matching song was found");
            }
        } catch (Exception e) {
            System.err.println("Unable to retrieve data: ");
            System.err.println(e.getMessage());
        }

    }

}
```