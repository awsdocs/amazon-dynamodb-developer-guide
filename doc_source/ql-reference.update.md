# PartiQL update statements for DynamoDB<a name="ql-reference.update"></a>

Use the `UPDATE` statement to modify the value of one or more attributes within an item in an Amazon DynamoDB table\. 

**Note**  
You can only update one item at a time; you cannot issue a single DynamoDB PartiQL statement that updates multiple items\. For information on updating multiple items, see [Performing transactions with PartiQL for DynamoDB](ql-reference.multiplestatements.transactions.md) or [Running batch operations with PartiQL for DynamoDB](ql-reference.multiplestatements.batching.md)\.

**Topics**
+ [Syntax](#ql-reference.update.syntax)
+ [Parameters](#ql-reference.update.parameters)
+ [Return value](#ql-reference.update.return)
+ [Examples](#ql-reference.update.examples)

## Syntax<a name="ql-reference.update.syntax"></a>

```
UPDATE  table  
[SET | REMOVE]  path  [=  data] […]
WHERE condition [RETURNING returnvalues]
<returnvalues>  ::= [ALL OLD | MODIFIED OLD | ALL NEW | MODIFIED NEW] *
```

## Parameters<a name="ql-reference.update.parameters"></a>

***table***  
\(Required\) The table containing the data to be modified\.

***path***  
\(Required\) An attribute name or document path to be created or modified\.

***data***  
\(Required\) An attribute value or the result of an operation\.  
The supported operations to use with SET:  
+ LIST\_APPEND: adds a value to a list type\.
+ SET\_ADD: adds a value to a number or string set\.
+ SET\_DELETE: removes a value from a number or string set\.

***condition***  
\(Required\) The selection criteria for the item to be modified\. This condition must resolve to a single primary key value\.

***returnvalues***  
\(Optional\) Use `returnvalues` if you want to get the item attributes as they appear before or after they are updated\. The valid values are:   
+ `ALL OLD *`\- Returns all of the attributes of the item, as they appeared before the update operation\.
+ `MODIFIED OLD *`\- Returns only the updated attributes, as they appeared before the update operation\.
+ `ALL NEW *`\- Returns all of the attributes of the item, as they appear after the update operation\.
+ `MODIFIED NEW *`\- Returns only the updated attributes, as they appear after the `UpdateItem` operation\.

## Return value<a name="ql-reference.update.return"></a>

This statement does not return a value unless `returnvalues` parameter is specified\.

**Note**  
If the WHERE clause of the UPDATE statement does not evaluate to true for any item in the DynamoDB table, `ConditionalCheckFailedException` is returned\.

## Examples<a name="ql-reference.update.examples"></a>

Update an attribute value in an existing item\. If the attribute does not exist, it is created\.

The following query updates an item in the `"Music"` table by adding an attribute of type number \(`AwardsWon`\) and an attribute of type map \(`AwardDetail`\)\.

```
UPDATE "Music" 
SET AwardsWon=1 
SET AwardDetail={'Grammys':[2020, 2018]}  
WHERE Artist='Acme Band' AND SongTitle='PartiQL Rocks'
```

You can add `RETURNING ALL OLD *` to return the attributes as they appeared before the `Update` operation\.

```
UPDATE "Music" 
SET AwardsWon=1 
SET AwardDetail={'Grammys':[2020, 2018]}  
WHERE Artist='Acme Band' AND SongTitle='PartiQL Rocks'
RETURNING ALL OLD *
```

This returns the following:

```
{
    "Items": [
        {
            "Artist": {
                "S": "Acme Band"
            },
            "SongTitle": {
                "S": "PartiQL Rocks"
            }
        }
    ]
}
```

You can add `RETURNING ALL NEW *` to return the attributes as they appeared after the `Update` operation\.

```
UPDATE "Music" 
SET AwardsWon=1 
SET AwardDetail={'Grammys':[2020, 2018]}  
WHERE Artist='Acme Band' AND SongTitle='PartiQL Rocks'
RETURNING ALL NEW *
```

This returns the following:

```
{
    "Items": [
        {
            "AwardDetail": {
                "M": {
                    "Grammys": {
                        "L": [
                            {
                                "N": "2020"
                            },
                            {
                                "N": "2018"
                            }
                        ]
                    }
                }
            },
            "AwardsWon": {
                "N": "1"
            }
        }
    ]
}
```

The following query updates an item in the `"Music"` table by appending to a list `AwardDetail.Grammys`\.

```
UPDATE "Music" 
SET AwardDetail.Grammys =list_append(AwardDetail.Grammys,[2016])  
WHERE Artist='Acme Band' AND SongTitle='PartiQL Rocks'
```

The following query updates an item in the `"Music"` table by removing from a list `AwardDetail.Grammys`\.

```
UPDATE "Music" 
REMOVE AwardDetail.Grammys[2]   
WHERE Artist='Acme Band' AND SongTitle='PartiQL Rocks'
```

The following query updates an item in the `"Music"` table by adding `BillBoard` to the map `AwardDetail`\.

```
UPDATE "Music" 
SET AwardDetail.BillBoard=[2020] 
WHERE Artist='Acme Band' AND SongTitle='PartiQL Rocks'
```

The following query updates an item in the `"Music"` table by adding the string set attribute `BandMembers`\.

```
UPDATE "Music" 
SET BandMembers =<<'member1', 'member2'>> 
WHERE Artist='Acme Band' AND SongTitle='PartiQL Rocks'
```

The following query updates an item in the `"Music"` table by adding `newbandmember` to the string set `BandMembers`\.

```
UPDATE "Music" 
SET BandMembers =set_add(BandMembers, <<'newbandmember'>>) 
WHERE Artist='Acme Band' AND SongTitle='PartiQL Rocks'
```