# Tagging Restrictions<a name="TaggingRestrictions"></a>

 Each tag consists of a key and a value, both of which you define\. The following restrictions apply: 
+  Each DynamoDB table can have only one tag with the same key\. If you try to add an existing tag \(same key\), the existing tag value will be updated to the new value\. 
+  Tag keys and values are case sensitive\. 
+  Maximum key length: 128 Unicode characters 
+  Maximum value length: 256 Unicode characters 
+  Allowed characters are letters, whitespace, and numbers, plus the following special characters: \+ \- = \. \_ : / 
+  Maximum number of tags per resource: 50 
+  AWS\-assigned tag names and values are automatically assigned the aws: prefix, which you cannot assign\. AWS\-assigned tag names do not count toward the tag limit of 50\. User\-assigned tag names have the prefix `user:` in the cost allocation report\. 
+  You cannot backdate the application of a tag\. 