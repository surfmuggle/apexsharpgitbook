---
description: An open-source API specification for Salesforce Functions
---

# ApexSharp Functions API

### Goal

Allow anyone to use **any** Apex Functions library and **any** Server side code in **any** programming language in **any** cloud provider.

The server should provide an API Post Link with the URI /functions&#x20;

E.g.:&#x20;

From Salesforce, the above URL will be called with a POST method.

###

### Request JSON (Work in Progress)



```json
{
  "userSessionId" : "SESSION_ID_REMOVED",
  "userName" : "apexfunctions@demo.com",
  "type" : "Azure.QRCode",
  "timeStamp" : "4/29/2023, 11:02:21 AM PDT",
  "specversion" : "1.0",
  "source" : "https://server.develop.my.salesforce.com",
  "orgId" : "00DDo000001AEWSMA5",
  "id" : "01GZ72W7GFRMQDYCHM6ZZ9C0D1",
  "datacontenttype" : "application/json",
  "data_base64" : null,
  "data" : "{\"AccountId\":\"123\"}"
}
```

### Response JSON (Work in Progress)

```json
{
  "userName" : "apexfunctions@demo.com",
  "totalTime" : 123,
  "timeCompleted" : "2023-04-29T18:02:21.598Z",
  "serverName" : "Azure Lambada Server One",
  "response" : "{\"AccountId\":\"123\"}",
  "orgId" : "00DDo000001AEWSMA5",
  "invocationStatus" : "SUCCESS",
  "invocationId" : "01GZ72W7GFRMQDYCHM6ZZ9C0D1",
  "function" : "Azure.QRCode",
  "error" : ""
}
```



* [**Function Class**](https://developer.salesforce.com/docs/atlas.en-us.apexref.meta/apexref/apex\_class\_functions\_Function.htm#apex\_class\_functions\_Function)\
  Use the Function class to access deployed Salesforce Functions and invoke them synchronously or asynchronously.

This is the primary class all users will use. The sample code will look as follows.&#x20;

```apex
public static string ApexSharpFunctionGet() {
  Function accountFunction = Function.get('MyProject.AccountFunction');
  FunctionInvocation invocation = accountFunction.invoke('{"AccountId":"123"}'a);
  return invocation.getResponse();     
}
```

Note the line _Function.get('MyProject.AccountFunction');_ . The value we are passing MyProject.AccountFunction.&#x20;

MyProject is the URL that we set it up in

AccountFunction is the name of the function that is passed to the server



* [**FunctionCallback Interface**](https://developer.salesforce.com/docs/atlas.en-us.apexref.meta/apexref/apex\_interface\_functions\_FunctionCallback.htm#apex\_interface\_functions\_FunctionCallback)\
  Represents the callback Salesforce calls when an asynchronous, queued Function invocation has been completed.
* [**FunctionErrorType Enum**](https://developer.salesforce.com/docs/atlas.en-us.apexref.meta/apexref/apex\_enum\_functions\_FunctionErrorType.htm)\
  Represents the error type of FunctionInvocationError.
* [**FunctionInvocation Interface**](https://developer.salesforce.com/docs/atlas.en-us.apexref.meta/apexref/apex\_interface\_functions\_FunctionInvocation.htm#apex\_interface\_functions\_FunctionInvocation)\
  Use FunctionInvocation to get the status and results of a synchronous or asynchronous Function invocation.
* [**FunctionInvocationError Interface**](https://developer.salesforce.com/docs/atlas.en-us.apexref.meta/apexref/apex\_interface\_functions\_FunctionInvocationError.htm#apex\_interface\_functions\_FunctionInvocationError)\
  Use FunctionInvocationError to get detailed error information about a failed Function invocation.
* [**FunctionInvocationStatus Enum**](https://developer.salesforce.com/docs/atlas.en-us.apexref.meta/apexref/apex\_enum\_functions\_FunctionInvocationStatus.htm)\
  Represents the status of a Function invocation.
* [**FunctionInvokeMock Interface**](https://developer.salesforce.com/docs/atlas.en-us.apexref.meta/apexref/apex\_interface\_functions\_FunctionInvokeMock.htm#apex\_interface\_functions\_FunctionInvokeMock)\
  Use the FunctionInvokeMock interface to mock Salesforce Functions responses during testing.
* [**MockFunctionInvocationFactory Class**](https://developer.salesforce.com/docs/atlas.en-us.apexref.meta/apexref/apex\_class\_functions\_MockFunctionInvocationFactory.htm#apex\_class\_functions\_MockFunctionInvocationFactory)\
  Use the MockFunctionInvocationFactory methods to generate appropriate mock responses for testing Salesforce Functions.

##

##

##
