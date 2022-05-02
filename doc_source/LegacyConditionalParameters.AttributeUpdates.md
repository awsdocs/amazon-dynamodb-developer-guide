# AttributeUpdates<a name="LegacyConditionalParameters.AttributeUpdates"></a>

In an `UpdateItem` operation, `AttributeUpdates` are the names of attributes to be modified, the action to perform on each, and the new value for each\. If you are updating an attribute that is an index key attribute for any indexes on that table, the attribute type must match the index key type defined in the `AttributesDefinition` of the table description\. You can use `UpdateItem` to update any non\-key attributes\.

Attribute values cannot be null\. String and Binary type attributes must have lengths greater than zero\. Set type attributes must not be empty\. Requests with empty values will be rejected with a `ValidationException` exception\.

Each `AttributeUpdates` element consists of an attribute name to modify, along with the following:
+  `Value` \- The new value, if applicable, for this attribute\.
+  `Action` \- A value that specifies how to perform the update\. This action is only valid for an existing attribute whose data type is Number or is a set; do not use `ADD` for other data types\. 

  If an item with the specified primary key is found in the table, the following values perform the following actions:
  +  `PUT` \- Adds the specified attribute to the item\. If the attribute already exists, it is replaced by the new value\. 
  +  `DELETE` \- Removes the attribute and its value, if no value is specified for `DELETE`\. The data type of the specified value must match the existing value's data type\.

    If a set of values is specified, then those values are subtracted from the old set\. For example, if the attribute value was the set `[a,b,c]` and the `DELETE` action specifies `[a,c]`, then the final attribute value is `[b]`\. Specifying an empty set is an error\.
  +  `ADD` \- Adds the specified value to the item, if the attribute does not already exist\. If the attribute does exist, then the behavior of `ADD` depends on the data type of the attribute:
    + If the existing attribute is a number, and if `Value` is also a number, then `Value` is mathematically added to the existing attribute\. If `Value` is a negative number, then it is subtracted from the existing attribute\.
**Note**  
If you use `ADD` to increment or decrement a number value for an item that doesn't exist before the update, DynamoDB uses 0 as the initial value\.  
Similarly, if you use `ADD` for an existing item to increment or decrement an attribute value that doesn't exist before the update, DynamoDB uses `0` as the initial value\. For example, suppose that the item you want to update doesn't have an attribute named *itemcount*, but you decide to `ADD` the number `3` to this attribute anyway\. DynamoDB will create the *itemcount* attribute, set its initial value to `0`, and finally add `3` to it\. The result will be a new *itemcount* attribute, with a value of `3`\.
    + If the existing data type is a set, and if `Value` is also a set, then `Value` is appended to the existing set\. For example, if the attribute value is the set `[1,2]`, and the `ADD` action specified `[3]`, then the final attribute value is `[1,2,3]`\. An error occurs if an `ADD` action is specified for a set attribute and the attribute type specified does not match the existing set type\. 

      Both sets must have the same primitive data type\. For example, if the existing data type is a set of strings, `Value` must also be a set of strings\.

  If no item with the specified key is found in the table, the following values perform the following actions:
  +  `PUT` \- Causes DynamoDB to create a new item with the specified primary key, and then adds the attribute\. 
  +  `DELETE` \- Nothing happens, because attributes cannot be deleted from a nonexistent item\. The operation succeeds, but DynamoDB does not create a new item\.
  +  `ADD` \- Causes DynamoDB to create an item with the supplied primary key and number \(or set of numbers\) for the attribute value\. The only data types allowed are Number and Number Set\.

If you provide any attributes that are part of an index key, then the data types for those attributes must match those of the schema in the table's attribute definition\.

## Use *UpdateExpression* Instead<a name="w615aac41c23c19c13"></a>

Suppose you wanted to modify an item in the *Music* table\. You could use an `UpdateItem` request with an `AttributeUpdates` parameter, as in this AWS CLI example:

```
aws dynamodb update-item \
    --table-name Music \
    --key '{
        "SongTitle": {"S":"Call Me Today"}, 
        "Artist": {"S":"No One You Know"}
    }' \
    --attribute-updates '{
        "Genre": {
            "Action": "PUT", 
            "Value": {"S":"Rock"}
        }   
    }'
```

But you could use a `UpdateExpression` instead:

```
aws dynamodb update-item \
    --table-name Music \
    --key '{
        "SongTitle": {"S":"Call Me Today"}, 
        "Artist": {"S":"No One You Know"}
    }' \
    --update-expression 'SET Genre = :g' \ 
    --expression-attribute-values '{
        ":g": {"S":"Rock"}
    }'
```