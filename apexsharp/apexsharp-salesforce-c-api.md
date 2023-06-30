---
description: A Inteligent C# Wrapper for Salesforce REST API
---

# ApexSharp Salesforce C# API

**Feedback**

To provide feedback or ask questions, kindly utilize the GitHub discussion function. Follow this link to access the GitHub discussion and leave your comments. Your feedback is highly appreciated.&#x20;

[Go to the GitHub discussion](https://github.com/apexsharp/SalesforceNetApi/discussions/categories/ideas)

**Goals**

Take advantage of [.NET 8](https://learn.microsoft.com/en-us/dotnet/core/whats-new/dotnet-8)

Use the latest features developed upto [C# 12](https://learn.microsoft.com/en-us/dotnet/csharp/whats-new/csharp-12)

Use features unique to C#. For example

* LINQ, instead of dealing with SOQL
* Take advantage of async / await
* Hide the complexity: For example `Select<Contact>()` will return all the contact objects. the API chooses the best path based on the number of records; this can be 1 record or 100 million.

Use some of the most used Nuget Opensource libraries

* [_Serilog_](https://serilog.net/)
* [_RestSharp_](https://restsharp.dev/)
* [_JSON.Net_](https://www.newtonsoft.com/json)
* [_Noda Time_](https://nodatime.org/)
* [_Fluent Validation_](https://docs.fluentvalidation.net/en/latest/)
* [_Lamar IoC_](https://jasperfx.github.io/lamar/)
* [_PostSharp Metalama_](https://www.postsharp.net/metalama)
* [_Polly_](https://github.com/App-vNext/Polly)
* [_Benchmark .Net_ ](https://github.com/dotnet/BenchmarkDotNet)

**Read Records - Select**

```csharp
// Get all the Contact objects and all the fields on each Contact object
List<Contact> contactList = con.Select<Contact>();
```

The REST calls and pagination is hidden from the developer. If there are 50K records and if you do not put a limit, you will get all 50K records.

For example, the above code most of us will assume it will generate the following SOQL

```sql
SELECT FIELDS(ALL) FROM Contact 
```

But that is not valid as there is a limit of 200 records when you get all the fields. To overcome this, first, we make the following call to find the record count

```sql
SELECT Count(Id) FROM Contact 
```

If the record count is less than 200, the following SOQL will be run

```sql
SELECT FIELDS(ALL) FROM Contact LIMIT 200
```

If the record count is between 200 and 2200, multiple calls are made

```sql
SELECT FIELDS(ALL) FROM Contact LIMIT 200 OFFSET 0
SELECT FIELDS(ALL) FROM Contact LIMIT 200 OFFSET 200
SELECT FIELDS(ALL) FROM Contact LIMIT 200 OFFSET 400
....
SELECT FIELDS(ALL) FROM Contact LIMIT 200 OFFSET 2000
```



If it's more than 2200 records, things get a bit complicated.&#x20;

1. Since we will know all the field names, we will create a SOQL with all the field names
2. Then query 2000 records at a time and append to a List
3. The user will get ALL the records.&#x20;
4.

**Safety**

Salesforce allocates a limit on the API use per 24-hour time frame. If you run out of them, it's bad news, as many things that depend on the REST API will stop working. Due to this, you want to check before making a Rest call.&#x20;

Due to this, we support an estimate on large CRUD operations. For example, the following code shows how you can get an estimate on the API usage, how many API calls are left and then add a 20% margin and make the call

<pre class="language-csharp"><code class="lang-csharp">// Get the API calls needed for this query
var apiUsage = connection.Select&#x3C;Contact>().ApiUsage();
// Get the API calls left
<strong>var apiLeft = connection.ApiLeft();
</strong>// Add a 20% safety net
apiLeft = apiLeft * 1.2;
// Only run the query if it's safe
if( apiLeft > apiUsage) {
	// SELECT FIELDS(ALL) FROM Contact
	List&#x3C;Contact> contactList = connection.Select&#x3C;Contact>();
}
</code></pre>

We can't estimate if the call has a where clause.&#x20;

```csharp
// Get the API calls left
var apiLeft = connection.ApiLeft();
// SELECT Id FROM Contact WHERE Name = 'Jay'
var contactList = connection.Select<Contact>((x => x.Id))
.Where(x => x.Name == "Jay").StopAt(apiLeft * 0.9);
```

By adding StopAt(), you are telling when to stop. In the above example, once the API left number reaches 90%, we will not make any more API calls and will return what has been received.



`Todo: How will the developer know this is a partial return?`





**Lazy Loading**

{% code overflow="wrap" fullWidth="false" %}
```csharp
Contact contactById = connection.Select<Contact>(contact.Id);
Console.WriteLine(contactById.Account.Name);
Console.WriteLine(contactById.AccountId);
```
{% endcode %}

1. Let us say we want to retrieve a **Contact** object and want to find the name of the user who created it
2. Note `Console.WriteLine(contactById.Account.Name),` CreatedBy returns a **User** object. When this is called, we make another REST call to get details about the Name field in the Account object.&#x20;
3. However `Console.WriteLine(contactById.AccountId)` does not require another REST call, as the first call would have obtained that value

This way, we prevent calling the entire object tree when the **Contact** object is retrieved.&#x20;

However, the following will make only a single call and only retrieve the fields we have specified.&#x20;

{% code overflow="wrap" lineNumbers="true" %}
```csharp
List<ContactDTO> contactDtoList = connection.Select<Contact, ContactDTO>((x, y) => new ContactDTO
{
    ContactName = x.Name,
    AccountName = x.Account.Name
});
```
{% endcode %}

**Insert**

Salesforce object inserts API supports multiple ways of inserting an object or list of objects. How inserts are done, and errors are managed is abstracted from the developer. The three methods are

```csharp
// Insert a single Contact object
var reply = Insert<Contact>(contact);
	
// Insert a list of Contact objects with a batch size of 200
List<Reply> replyListOne = Insert<Contact>(contactList);

// Insert a list of Contacts with a custom batch size; 
// batch size can't be larger than 200
List<Reply> replyListTwo = Insert<Contact>(contactList, batchSize);
```

In Salesforce, you can pass up to 200 objects in a single REST call. By default, if you pass a list of 1000 Contact objects, we split it into 5 REST calls of 200 records each. All REST calls are made concurrently.&#x20;

Optionally you can specify a smaller batch size. For example, f you pass a list of 1000 Contact objects and a batch size of 10, we will make 100 REST calls concurrently

In testing, we have found smaller batch size will lead to faster performance. The downside is you will be using your API limits.&#x20;

Here is an example of inserting a Contact object with Two Fields.

<table><thead><tr><th width="309.3333333333333">Insert Type</th><th>Time in Millisecond</th><th># of API Calls</th></tr></thead><tbody><tr><td>One Record</td><td>1,801</td><td>1</td></tr><tr><td>1000 Records, Batch Size 200</td><td>6,604</td><td>5</td></tr><tr><td>1000 Records, Batch Size 10</td><td>155</td><td>100</td></tr></tbody></table>

One of the biggest issues is error management. If you try inserting 1M records at 200 batch size this is 5000 REST calls, which can be made concurrently. Unfortunately, the whole insert process going fine is slim.&#x20;

One of the issues with this approach is that we will get "ServerError" from Salesforce, and the records will not be inserted, updated, or deleted.&#x20;

For every insert we keep track of and failed inserts are retried, When this happens we will use [Polly ](https://github.com/App-vNext/Polly)to retry, and if that fails, log all the failed objects.&#x20;

When this happens we will use [Polly ](https://github.com/App-vNext/Polly)to retry, and if that fails, log all the failed objects.&#x20;

_A local database such as_ [_LiteDb_](http://www.litedb.org/) _will be used. The way this will work is each object will be given a GUID, and if there is a failure, those objects will be saved to LiteDB. Please feel free to start a conversation on the GitHub discussion section_ [_here_](https://github.com/apexsharp/SalesforceNetApi/discussions/categories/ideas)_._ &#x20;

_The same logic is applied to Delete and Update_





**SELECT**

```csharp
// SELECT Id FROM Contact
connection.Select<Contact>(x => x.Id).Run();
// SELECT Id, Name FROM Contact
connection.Select<Contact>(x => new { x.Id, x.Name }).Run();
// SELECT Id FROM Contact WHERE Name = 'Jay'
connection.Select<Contact>((x => x.Id)).Where(x => x.Name == "Jay").Run();
// SELECT Id FROM Contact WHERE MailingLatitude = 5.5
connection.Select<Contact>((x => x.Id)).Where(x => x.MailingLatitude == 5.5).Run();
// SELECT Id FROM Contact WHERE MailingLatitude NOT 5.5
connection.Select<Contact>((x => x.Id)).Where(x => x.MailingLatitude != 5.5).Run();
// SELECT Id FROM Contact WHERE MailingLatitude <= 5
connection.Select<Contact>((x => x.Id)).Where(x => x.MailingLatitude <= 5.0).Run();
// SELECT Id FROM Contact WHERE MailingLatitude >= 5
connection.Select<Contact>((x => x.Id)).Where(x => x.MailingLatitude >= 5.0).Run();
// SELECT Id FROM Contact WHERE MailingLatitude > 5
connection.Select<Contact>((x => x.Id)).Where(x => x.MailingLatitude > 5.0).Run();
// SELECT Id FROM Contact WHERE MailingLatitude < 5
connection.Select<Contact>((x => x.Id)).Where(x => x.MailingLatitude < 5.0).Run();
// SELECT Id FROM Contact WHERE Name = 'Jay' OR Name = 'John'
connection.Select<Contact>((x => x.Id)).Where(x => x.Name == "Jay" || x.Name == "John").Run();
// SELECT Id FROM Contact WHERE Name = 'Jay' NOT DoNotCall = True
connection.Select<Contact>((x => x.Id)).Where(x => x.Name == "Jay" != x.DoNotCall == true).Run();
// SELECT Id FROM Contact WHERE Name = 'Jay' AND DoNotCall = True
connection.Select<Contact>((x => x.Id)).Where(x => x.Name == "Jay" && x.DoNotCall == true).Run();
// SELECT Id FROM Contact WHERE Name = 'Jay' AND CreatedBy.Name = 'Jay'
connection.Select<Contact>(x => x.Id).Where(x => x.Name == "Jay" && x.CreatedBy.Name == "Jay").Run();
// SELECT FIELDS(ALL) FROM Contact WHERE CreatedBy.Name == "Jay"
connection.Select<Contact>(x => x.Id).Where(x => x.CreatedBy.Name == "Jay").Run();
// SELECT Id, Name, CreatedById FROM  Contact
connection.Select<Contact>(x => new { x.Id, x.Name, x.CreatedById }).Run();
```

**Raw SELECT**

{% code lineNumbers="true" %}
```csharp
// SELECT Id FROM Contact
connection.Select<Contact>("SELECT Id FROM Contact").Run();
// SELECT Id FROM Contact
connection.Select("SELECT Id FROM Contact");
// SELECT FIELDS(ALL) FROM Contact WHERE Id='003Ho00001ftlLyIAI' LIMIT 1
connection.SelectById<Contact>("003Ho00001ftlLyIAI").Run();        
```
{% endcode %}

* Line 2: The ability to submit raw SOQL and get the results bound to a class
* Line 4: Same as above, but you get the actual JSON
* Line 6: Get a single record by its ID

**Upsert**

Send a list of objects; if the Id is null or empty, it will be an insert. If we have an Id on the record it will be an update

```csharp
// Upsert single record
var reply = connection.Upsert<Contact>(contact);
	
// Upsert a list of objects. This will set the default batch size to 200 records
List<Reply> replyListOne = connection.Upsert<Contact>(contactList);

// Upsert a list of Contacts with a custom batch size; 
// Batch size can't be larger than 200
List<Reply> replyListTwo = connection.Upsert<Contact>(contactList, batchSize);
```

**Delete**

```csharp
// Delete all records
var reply = connection.Delete<Contact>();

// Pass a list of record ID's to be deleted
var reply = connection.Delete<Contact>(List<string> Id);

// Delete a single record given an Id
var reply = connection.Delete<Contact>(String Id);

```

**Bulk API**

```csharp
List<Contact> contactList = new List<Contact>();
contactList.Add(new Contact(){Name="Jay1"});
contactList.Add(new Contact(){Name="Jay2"});
contactList.Add(new Contact(){Name="Jay3"});
		
var bulkInsertStatus = BulkInsert<Contact>(contactList);

if(bulkInsertStatus.GetJobInfo().State == "JobComplete") {
	var SuccessfulResults = bulkInsertStatus.GetSuccessfulResults();
	var failedResults = bulkInsertStatus.GetFailedResults();
	var unprocessedrecords = bulkInsertStatus.GetUnprocessedrecords();
}
```

1. If you have a very large data set, you can use [Bulk API 2.0](https://developer.salesforce.com/docs/atlas.en-us.api\_asynch.meta/api\_asynch/bulk\_api\_2\_0.htm)
2. C# objects to CSV conversion are done automatically&#x20;
3. If the CSV File is more than 100MB, the files will be automatically chunked
4. Parent / Child relationships are automatically mapped from C# object
5. GZip Support for responses in the background&#x20;
6. You can pass a Delegate callback to get incremental updates on your bulk api request





**Convenience Methods**&#x20;



**Feedback**

To provide feedback or ask questions, kindly utilize the GitHub discussion function. Follow this link to access the GitHub discussion and leave your comments. Your feedback is highly appreciated.&#x20;

[Go to the GitHub discussion](https://github.com/apexsharp/SalesforceNetApi/discussions/categories/ideas)

