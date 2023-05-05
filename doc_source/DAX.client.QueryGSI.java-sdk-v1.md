# Querying global secondary indexes with SDK for Java 1\.x<a name="DAX.client.QueryGSI.java-sdk-v1"></a>

You can use Amazon DynamoDB Accelerator \(DAX\) to query [global secondary indexes](https://docs.aws.amazon.com/amazondynamodb/latest/developerguide/GSI.html) using DynamoDB [programmatic interfaces](https://docs.aws.amazon.com/amazondynamodb/latest/developerguide/Programming.SDKs.Interfaces.html)\.

The following example demonstrates how to use DAX to query the `CreateDateIndex` global secondary index that is created in [Example: Global secondary indexes using the AWS SDK for Java document API](https://docs.aws.amazon.com/amazondynamodb/latest/developerguide/GSIJavaDocumentAPI.Example.html)\. 

The `DAXClient` class instantiates the client objects that are needed to interact with the DynamoDB programming interfaces\.

```
import com.amazon.dax.client.dynamodbv2.AmazonDaxClientBuilder;
import com.amazonaws.services.dynamodbv2.datamodeling.DynamoDBMapper;
import com.amazonaws.services.dynamodbv2.document.DynamoDB;
import com.amazonaws.util.EC2MetadataUtils;
import com.amazonaws.services.dynamodbv2.AmazonDynamoDB;

public class DaxClient {

	private static final String region = EC2MetadataUtils.getEC2InstanceRegion();

	DynamoDB getDaxDocClient(String daxEndpoint) {
		System.out.println("Creating a DAX client with cluster endpoint " + daxEndpoint);
		AmazonDaxClientBuilder daxClientBuilder = AmazonDaxClientBuilder.standard();

		daxClientBuilder.withRegion(region).withEndpointConfiguration(daxEndpoint);
		AmazonDynamoDB client = daxClientBuilder.build();

		return new DynamoDB(client);
	}

	DynamoDBMapper getDaxMapperClient(String daxEndpoint) {
		System.out.println("Creating a DAX client with cluster endpoint " + daxEndpoint);
		AmazonDaxClientBuilder daxClientBuilder = AmazonDaxClientBuilder.standard();

		daxClientBuilder.withRegion(region).withEndpointConfiguration(daxEndpoint);
		AmazonDynamoDB client = daxClientBuilder.build();

		return new DynamoDBMapper(client);
	}
}
```

You can query a global secondary index in the following ways: 
+ Use the `queryIndex` method on the `QueryIndexDax` class defined in the following example\. The `QueryIndexDax` takes as a parameter the client object that is returned by the `getDaxDocClient` method on the `DaxClient` class\.
+ If you are using the [object persistence interface](https://docs.aws.amazon.com/amazondynamodb/latest/developerguide/Programming.SDKs.Interfaces.Mapper.html), use the `queryIndexMapper` method on the `QueryIndexDax` class defined in the following example\. The `queryIndexMapper` takes as a parameter the client object that is returned by the `getDaxMapperClient` method defined on the `DaxClient` class\.

```
import java.util.Iterator;
import com.amazonaws.services.dynamodbv2.datamodeling.DynamoDBMapper;
import java.util.List;
import com.amazonaws.services.dynamodbv2.datamodeling.DynamoDBQueryExpression;
import com.amazonaws.services.dynamodbv2.model.AttributeValue;
import java.util.HashMap;
import com.amazonaws.services.dynamodbv2.document.Item;
import com.amazonaws.services.dynamodbv2.document.utils.ValueMap;
import com.amazonaws.services.dynamodbv2.document.spec.QuerySpec;
import com.amazonaws.services.dynamodbv2.document.QueryOutcome;
import com.amazonaws.services.dynamodbv2.document.ItemCollection;
import com.amazonaws.services.dynamodbv2.document.Index;
import com.amazonaws.services.dynamodbv2.document.Table;
import com.amazonaws.services.dynamodbv2.document.DynamoDB;

public class QueryIndexDax {

	//This is used to query Index using the low-level interface.
	public static void queryIndex(DynamoDB client, String tableName, String indexName) {
		Table table = client.getTable(tableName);

		System.out.println("\n***********************************************************\n");
		System.out.print("Querying index " + indexName + "...");

		Index index = table.getIndex(indexName);

		ItemCollection<QueryOutcome> items = null;

		QuerySpec querySpec = new QuerySpec();

		if (indexName == "CreateDateIndex") {
			System.out.println("Issues filed on 2013-11-01");
			querySpec.withKeyConditionExpression("CreateDate = :v_date and begins_with(IssueId, :v_issue)")
					.withValueMap(new ValueMap().withString(":v_date", "2013-11-01").withString(":v_issue", "A-"));
			items = index.query(querySpec);
		} else {
			System.out.println("\nNo valid index name provided");
			return;
		}

		Iterator<Item> iterator = items.iterator();

		System.out.println("Query: printing results...");

		while (iterator.hasNext()) {
			System.out.println(iterator.next().toJSONPretty());
		}

	}

	//This is used to query Index using the high-level mapper interface.
	public static void queryIndexMapper(DynamoDBMapper mapper, String tableName, String indexName) {
		HashMap<String, AttributeValue> eav = new HashMap<String, AttributeValue>();
		eav.put(":v_date", new AttributeValue().withS("2013-11-01"));
		eav.put(":v_issue", new AttributeValue().withS("A-"));
		DynamoDBQueryExpression<CreateDate> queryExpression = new DynamoDBQueryExpression<CreateDate>()
				.withIndexName("CreateDateIndex").withConsistentRead(false)
				.withKeyConditionExpression("CreateDate = :v_date and begins_with(IssueId, :v_issue)")
				.withExpressionAttributeValues(eav);

		List<CreateDate> items = mapper.query(CreateDate.class, queryExpression);
		Iterator<CreateDate> iterator = items.iterator();

		System.out.println("Query: printing results...");

		while (iterator.hasNext()) {
			CreateDate iterObj = iterator.next();
			System.out.println(iterObj.getCreateDate());
			System.out.println(iterObj.getIssueId());
		}
	}
}
```

The class definition below represents the Issues table and is used in the `queryIndexMapper` method\.

```
import com.amazonaws.services.dynamodbv2.datamodeling.DynamoDBTable;
import com.amazonaws.services.dynamodbv2.datamodeling.DynamoDBIndexHashKey;
import com.amazonaws.services.dynamodbv2.datamodeling.DynamoDBIndexRangeKey;
import com.amazonaws.services.dynamodbv2.datamodeling.DynamoDBHashKey;

@DynamoDBTable(tableName = "Issues")
public class CreateDate {
	private String createDate;
	@DynamoDBHashKey(attributeName = "IssueId")
	private String issueId;

	@DynamoDBIndexHashKey(globalSecondaryIndexName = "CreateDateIndex", attributeName = "CreateDate")
	public String getCreateDate() {
		return createDate;
	}

	public void setCreateDate(String createDate) {
		this.createDate = createDate;
	}

	@DynamoDBIndexRangeKey(globalSecondaryIndexName = "CreateDateIndex", attributeName = "IssueId")
	public String getIssueId() {
		return issueId;
	}

	public void setIssueId(String issueId) {
		this.issueId = issueId;
	}
}
```