---
description: A Inteligent C# Wrapper for Salesforce REST API
---

# ApexSharp API

**Goals**

Take advantage of [.NET 8](https://learn.microsoft.com/en-us/dotnet/core/whats-new/dotnet-8)

Use the latest features developed upto [C# 12](https://learn.microsoft.com/en-us/dotnet/csharp/whats-new/csharp-12)

Use some of the most used Nuget Opensource libraries

* _Serilog_
* _RestSharp_
* _JSON.Net_
* _Noda Time_
* _Fluent Validation_

**Keep it simple**

```csharp
List<Contact> contactList = con.Select<Contact>();
```

The above code will get all the **Contact** objects and all the fields on each Contact object. The REST calls and pagination is hidden from the developer.&#x20;

```csharp
Contact contact = con.SelectByID<Contact>("003Ho00001ftlLyIAI");
```

The above code will get the **Contact** object with a given ID

**Lazy Loading**

{% code lineNumbers="true" %}
```csharp
Contact contact = con.Select<Contact>(x => new { x.Id, x.Name, x.CreatedById })
	.FirstOrDefault();
User user = contact.CreatedBy;
Console.WriteLine(user.Name);

String userName = con.SelectByID<User>(contact.CreatedById).Name;
Console.WriteLine(userName);

ContactDTO contactDTO = con.Select<Contact, ContactDTO>((x, y) => new ContactDTO
{
	Id = x.Id,
	Name = x.Name,
	CreatedbyName = x.CreatedBy.Name
});
Console.WriteLine(contactDTO.Name);


```
{% endcode %}

1. Let us say we want to retrieve a **Contact** object and want to find the name of the user who created it
2. Note lines 3, 6 and 10
3. CreatedBy returns a **User** object. When this is called we make another REST call to get details about the User object.&#x20;

This way, we prevent calling the entire object tree.&#x20;

