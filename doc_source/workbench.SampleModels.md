# Sample data models for NoSQL Workbench<a name="workbench.SampleModels"></a>

The home page for the modeler and visualizer display a number of sample models that ship with the NoSQL Workbench\. This section describes these models and their potential uses\.

**Topics**
+ [Employee data model](#workbench.SampleModels.EmployeeDataModel)
+ [Discussion forum data model](#workbench.SampleModels.DiscussionForumDataModel)
+ [Music library data model](#workbench.SampleModels.MusicLibraryDataModel)
+ [Ski resort data model](#workbench.SampleModels.SkiResortDataModel)
+ [Credit card offers data model](#workbench.SampleModels.CreditCardOffersDataModel)
+ [Bookmarks data model](#workbench.SampleModels.BookmarksDataModel)

## Employee data model<a name="workbench.SampleModels.EmployeeDataModel"></a>

This data model is an introductory model\. It represents an employee’s basic details such as a unique alias, first name, last name, designation, manager, and skills\.

This data model depicts a few techniques such as handling complex attribute such as having more than one skill\. This model is also an example of one\-to\-many relationship through the manager and thier reporting employees that has been achieved by the secondary index DirectReports\.

The access patterns facilitated by this data model are:
+ Retrieval of an employee record using the employee’s login alias, facilitated by a table called `Employee`\.
+ Search for employees by name, facilitated by the Employee table’s global secondary index called `Name`\.
+ Retrieval of all direct reports of a manager using the manager’s login alias, facilitated by the Employee table’s global secondary index called `DirectReports`\.

## Discussion forum data model<a name="workbench.SampleModels.DiscussionForumDataModel"></a>

This data model represents a discussion forums\. Using this model customers can engage with the developer community, ask questions, and respond to other customers' posts\. Each AWS service has a dedicated forum\. Anyone can start a new discussion thread by posting a message in a forum, and each thread receives any number of replies\.

The access patterns facilitated by this data model are:
+ Retrieval of a forum record using the forum’s name, facilitated by a table called `Forum`\.
+ Retrieval of a specific thread or all threads for a forum, facilitated by a table called `Thread`\.
+ Search for replies using the posting user’s email address, facilitated by the Reply table’s global secondary index called `PostedBy-Message-Index`\.

## Music library data model<a name="workbench.SampleModels.MusicLibraryDataModel"></a>

This data model represents a music library that has a large collection of songs and showcases its most downloaded songs in near\-real time\.

The access patterns facilitated by this data model are:
+ Retrieval of a song record, facilitated by a table called `Songs`\.
+ Retrieval of a specific download record or all download records for a song, facilitated by a table called `Songs`\.
+ Retrieval of a specific monthly download count record or all monthly download count records for a song, facilitated by a table called `Song`\.
+ Retrieval of all records \(including song record, download records, and monthly download count records\) for a song, facilitated by a table called `Songs`\.
+ Search for most downloaded songs, facilitated by the Songs table’s global secondary index called `DownloadsByMonth`\.

## Ski resort data model<a name="workbench.SampleModels.SkiResortDataModel"></a>

This data model represents a ski resort that has a large collection of data for each ski lift collected daily\.

The access patterns facilitated by this data model are:
+ Retrieval of all data for a given ski lift or overall resort, dynamic and static, facilitated by a table called `SkiLifts`\.
+ Retrieval of all dynamic data \(including unique lift riders, snow coverage, avalanche danger, and lift status\) for a ski lift or the overall resort on a specific date, facilitated by a table called `SkiLifts`\.
+ Retrieval of all static data \(including if the lift is for experienced riders only, vertical feet the lift rises, and lift riding time\) for a specific ski lift, facilitated by a table called `SkiLifts`\.
+ Retrieval of date of data recorded for a specific ski lift or the overall resort sorted by total unique riders, facilitated by the SkiLifts table's global secondary index called `SkiLiftsByRiderss`\.

## Credit card offers data model<a name="workbench.SampleModels.CreditCardOffersDataModel"></a>

This data model is used by a Credit Card Offers Application\.

A credit card provider produces offers over time\. These offers include balance transfers without fees, increased credit limits, lower interest rates, cash back, and airline miles\. After a customer accepts or declines these offers, the respective offer status is updated accordingly\.

The access patterns facilitated by this data model are:
+ Retrieval of account records using `AccountId`, as facilitated by the main table\.
+ Retrieval of all the accounts with few projected items, as facilitated by the secondary index `AccountIndex`\.
+ Retrieval of accounts and all the offer records associated with those accounts by using `AccountId`, as facilitated by the main table\.
+ Retrieval of accounts and specific offer records associated with those accounts by using `AccountId` and `OfferId`, as facilitated by the main table\.
+ Retrieval of all `ACCEPTED/DECLINED` offer records of specific `OfferType` associated with accounts using `AccountId`, `OfferType`, and `Status`, as facilitated by the secondary index `GSI1`\.
+ Retrieval of offers and associated offer item records using `OfferId`, as facilitated by the main table\.

## Bookmarks data model<a name="workbench.SampleModels.BookmarksDataModel"></a>

This data model is used store bookmarks for customers\.

A customer can have many bookmarks and a bookmark can belong to many customers\. This data model represents a many\-to\-many relationship\. 

The access patterns facilitated by this data model are:
+ A single query by `customerId` can now return customer data as well as bookmarks\.
+ A query `ByEmail` index returns customer data by email address\. Note that bookmarks are not retrieved by this index\.
+ A query `ByUrl` index gets bookmarks data by URL\. Note that we have customerId as the sort key for the index because the same URL can be bookmarked by multiple customers\.
+ A query `ByCustomerFolder` index gets bookmarks by folder for each customer\.