# Step 6: Query the Data in the DynamoDB Table<a name="EMRforDynamoDB.Tutorial.QueryDataInDynamoDB"></a>

In this step, you will use HiveQL to query the Features table in DynamoDB\. Try the following Hive queries:

1. All of the feature types \(`feature_class`\) in alphabetical order:

   ```
   SELECT DISTINCT feature_class
   FROM ddb_features
   ORDER BY feature_class;
   ```

1. All of the lakes that begin with the letter "M":

   ```
   SELECT feature_name, state_alpha
   FROM ddb_features
   WHERE feature_class = 'Lake'
   AND feature_name LIKE 'M%'
   ORDER BY feature_name;
   ```

1. States with at least three features higher than a mile \(5,280 feet\):

   ```
   SELECT state_alpha, feature_class, COUNT(*)
   FROM ddb_features
   WHERE elev_in_ft > 5280
   GROUP by state_alpha, feature_class
   HAVING COUNT(*) >= 3
   ORDER BY state_alpha, feature_class;
   ```

**Next Step**  
[Step 7: \(Optional\) Clean Up](EMRforDynamoDB.Tutorial.CleanUp.md)