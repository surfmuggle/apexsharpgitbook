---
description: Anyone who wants to implement Functions on Server or Client side.
---

# ApexSharp Functions API

##

## Request JSON



```json
{
  "specversion": "1.0",
  "type": "com.example.someotherevent",
  "source": "/mycontext/9",
  "id": "C234-1234-1234",
  "time": "2018-04-05T17:31:05Z",
  "comexampleextension1": "value",
  "comexampleothervalue": 5,
  "datacontenttype": "application/json",
  "data": "base64"
}n
```



## Function Class

The following are methods for Function.

* [**get(functionName)**](https://developer.salesforce.com/docs/atlas.en-us.apexref.meta/apexref/apex\_class\_functions\_Function.htm#apex\_functions\_Function\_get)\
  Returns the Function instance for the named Function and Project. The Function must be properly deployed and have appropriate permissions to work with the org running your Apex code.
* [**get(namespace, functionName)**](https://developer.salesforce.com/docs/atlas.en-us.apexref.meta/apexref/apex\_class\_functions\_Function.htm#apex\_functions\_Function\_get\_2)\
  Returns the Function instance for the named Function, Project, and Namespace. The Function must be properly deployed and have appropriate permissions to work with the org running your Apex code.
* [**invoke(payload, callback)**](https://developer.salesforce.com/docs/atlas.en-us.apexref.meta/apexref/apex\_class\_functions\_Function.htm#apex\_functions\_Function\_invoke)\
  Invokes the Function asynchronously.
* [**invoke(payload)**](https://developer.salesforce.com/docs/atlas.en-us.apexref.meta/apexref/apex\_class\_functions\_Function.htm#apex\_functions\_Function\_invoke\_2)\
  Invokes the Function synchronously.

### FunctionCallback Class

The following are methods for FunctionCallback.

* [**handleResponse(var1)**](https://developer.salesforce.com/docs/atlas.en-us.apexref.meta/apexref/apex\_interface\_functions\_FunctionCallback.htm#apex\_functions\_FunctionCallback\_handleResponse)\
  Called when an asynchronous Function invocation has been completed.
