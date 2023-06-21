---
description: A Inteligent C# Wrapper for Salesforce REST API
---

# ApexSharp Salesforce API

**Goals**



Take advantage of [.NET 8](https://learn.microsoft.com/en-us/dotnet/core/whats-new/dotnet-8)

Use the latest features developed upto [C# 12](https://learn.microsoft.com/en-us/dotnet/csharp/whats-new/csharp-12)

Use some of the most used Nuget Opensource libraries

* [_Serilog_](https://serilog.net/)
* [_RestSharp_](https://restsharp.dev/)
* [_JSON.Net_](https://www.newtonsoft.com/json)
* [_Noda Time_](https://nodatime.org/)
* [_Fluent Validation_](https://docs.fluentvalidation.net/en/latest/)
* [_Lamar IoC_](https://jasperfx.github.io/lamar/)
* [_PostSharp Metalama_](https://www.postsharp.net/metalama)
* [_Benchmark .Net_ ](https://github.com/dotnet/BenchmarkDotNet)

**Keep it simple**

```csharp
// Get all the Contact objects and all the fields on each Contact object
List<Contact> contactList = con.Select<Contact>();
// Get the first 100 Contact objects
List<Contact> contactList = con.Select<Contact>().Limit(100);
```

The REST calls and pagination is hidden from the developer. If there are 50K records and if you do not put a limit, you will get all 50K records.

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



