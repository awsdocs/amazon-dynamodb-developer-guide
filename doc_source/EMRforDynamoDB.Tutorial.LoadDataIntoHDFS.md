# Step 4: Load Data into HDFS<a name="EMRforDynamoDB.Tutorial.LoadDataIntoHDFS"></a>

In this step, you will copy a data file into Hadoop Distributed File System \(HDFS\), and then create an external Hive table that maps to the data file\.

**Download the Sample Data**

1. Download the sample data archive \(`features.zip`\):

   ```
   wget https://docs.aws.amazon.com/amazondynamodb/latest/developerguide/samples/features.zip
   ```

1. Extract the `features.txt` file from the archive:

   ```
   unzip features.zip
   ```

1. View the first few lines of the `features.txt` file:

   ```
   head features.txt
   ```

   The result should look similar to this:

   ```
   1535908|Big Run|Stream|WV|38.6370428|-80.8595469|794
   875609|Constable Hook|Cape|NJ|40.657881|-74.0990309|7
   1217998|Gooseberry Island|Island|RI|41.4534361|-71.3253284|10
   26603|Boone Moore Spring|Spring|AZ|34.0895692|-111.410065|3681
   1506738|Missouri Flat|Flat|WA|46.7634987|-117.0346113|2605
   1181348|Minnow Run|Stream|PA|40.0820178|-79.3800349|1558
   1288759|Hunting Creek|Stream|TN|36.343969|-83.8029682|1024
   533060|Big Charles Bayou|Bay|LA|29.6046517|-91.9828654|0
   829689|Greenwood Creek|Stream|NE|41.596086|-103.0499296|3671
   541692|Button Willow Island|Island|LA|31.9579389|-93.0648847|98
   ```

   The `features.txt` file contains a subset of data from the United States Board on Geographic Names \([http://geonames\.usgs\.gov/domestic/download\_data\.htm](http://geonames.usgs.gov/domestic/download_data.htm)\)\. The fields in each line represent the following:
   + Feature ID \(unique identifier\)
   + Name
   + Class \(lake; forest; stream; and so on\)
   + State
   + Latitude \(degrees\)
   + Longitude \(degrees\)
   + Height \(in feet\)

1. At the command prompt, enter the following command:

   ```
   hive
   ```

   The command prompt changes to this: `hive>` 

1. Enter the following HiveQL statement to create a native Hive table:

   ```
   CREATE TABLE hive_features
       (feature_id             BIGINT,
       feature_name            STRING ,
       feature_class           STRING ,
       state_alpha             STRING,
       prim_lat_dec            DOUBLE ,
       prim_long_dec           DOUBLE ,
       elev_in_ft              BIGINT)
       ROW FORMAT DELIMITED
       FIELDS TERMINATED BY '|'
       LINES TERMINATED BY '\n';
   ```

1. Enter the following HiveQL statement to load the table with data:

   ```
   LOAD DATA
   LOCAL
   INPATH './features.txt'
   OVERWRITE
   INTO TABLE hive_features;
   ```

1. You now have a native Hive table populated with data from the `features.txt` file\. To verify, enter the following HiveQL statement:

   ```
   SELECT state_alpha, COUNT(*)
   FROM hive_features
   GROUP BY state_alpha;
   ```

   The output should show a list of states and the number of geographic features in each\.

**Next Step**  
[Step 5: Copy Data to DynamoDB](EMRforDynamoDB.Tutorial.CopyDataToDDB.md)