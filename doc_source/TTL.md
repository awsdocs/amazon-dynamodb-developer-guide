# Expiring Items Using Time to Live \(TTL\)<a name="TTL"></a>

Time to Live \(TTL\) for Amazon DynamoDB lets you define when items in a table expire so that they can be automatically deleted from the database\. With TTL enabled on a table, you can set a timestamp for deletion on a per\-item basis, allowing you to limit storage usage to only those records that are relevant\.

TTL is useful if you have continuously accumulating data that loses relevance after a specific time period \(for example, session data, event logs, usage patterns, and other temporary data\)\. If you have sensitive data that must be retained only for a certain amount of time according to contractual or regulatory obligations, TTL helps you ensure that it is removed promptly and as scheduled\.

To get started with TTL:

1. Understand [Time to Live: How It Works](howitworks-ttl.md)\.

1. Read the [Before You Begin Using Time to Live](time-to-live-ttl-before-you-start.md) section\. 

1. Enable TTL on a specific table, and choose the name of an attribute to hold the expiration timestamp\. Then, you can add or update items in the table with timestamps in the attribute that you chose\. For more information, see [Enabling Time to Live \(TTL\)](time-to-live-ttl-how-to.md)\.