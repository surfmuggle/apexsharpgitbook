---
description: An opensource implementation of Salesforce Functions for the C# community.
---

# ApexSharp Functions

**Goal**

Provide C# (Microsoft .NET) support for Salesforce developers and at the same time keep close compatibility with [Salesforce functions ](https://developer.salesforce.com/docs/platform/functions/overview)

Use open standards such as

* [Net Core](https://dotnet.microsoft.com/en-us/)
* [Cloud Events](https://cloudevents.io/)
* [0MQ](https://zeromq.org/)
* [Docker](https://www.docker.com/) and [Kubernetes ](https://kubernetes.io/)

Run anywhere you can run .Net Code

* Amazon
* Azure
* Google
* Container providers
* Local Datacenter&#x20;

Advantages of ApexSharp Functions over Salesforce Functions&#x20;

* Opensource&#x20;
* C# has a very large 3rd party library support, over 350K on [NuGet](https://www.nuget.org/)
* Visual Studio is the most advanced IDE&#x20;
* Allows Client and Server to be developed locally
  * Use[ Ngrok ](https://ngrok.com/product/secure-tunnels)or [Tailscale](https://tailscale.com/pricing/) if you are behind a firewall
* No cloud vendor lock-in as code runs anywhere their is .NET support
* Ability to scale the cloud services (1 CPU Cores to [96 Cores](https://www.amd.com/en/products/cpu/amd-epyc-9654p))
* Use Kubernetes for unlimited scalability.&#x20;
* Build AI applications using[ GPU Cloud](https://lambdalabs.com/service/gpu-cloud) and Microsoft [ML Framework](https://learn.microsoft.com/en-us/dotnet/machine-learning/)
* Integrate with applications written in other programming languages using [0MQ](https://zeromq.org/)
* Centralized logging, Salesforce and ApexFunctions log combined on [Seq](https://datalust.co/seq)&#x20;
  * We are using [NebulaLogger ](https://github.com/jongpie/NebulaLogger)and [Serilog](https://serilog.net/)



**High Level ArchArchitecture**

<figure><img src="../.gitbook/assets/image.png" alt=""><figcaption></figcaption></figure>

**How it all works**

ApexSharp contains two parts, client SDK and server SDK. The client SDK runs on your Salesforce org (and optionally the LWC code on the users browser). The server SDK is a C# based SDK that is compatible with any .NET languages and runs on any envirnment where you can run .NET&#x20;

**Apex Client SDK**

Apex

The Apex code runs in your Salesforce environment and implements all the [Salesforce functions API](https://developer.salesforce.com/docs/atlas.en-us.apexref.meta/apexref/apex\_namespace\_functions.htm). If you have existing Salesforce Apex code for functions, you will only need to make very small modification

Orginal Salesforce Code

```apex
public static string SalesforceFunctionGet() {
  functions.Function accountFunction = functions.Function.get('MyProject.AccountFunction');
  functions.FunctionInvocation invocation = accountFunction.invoke('{ "accountName" : "Acct", "contactName" : "MyContact", "opportunityName" : "Oppty" }');
  return invocation.getResponse();     
}
```

Modified Code to work with ApexSharp Functions

```csharp
public static string ApexSharpFunctionGet() {
  Function accountFunction = Function.get('MyProject.AccountFunction');
  FunctionInvocation invocation = accountFunction.invoke('{ "accountName" : "Acct", "contactName" : "MyContact", "opportunityName" : "Oppty" }');
  return invocation.getResponse();     
}
```

As you see all we have to do is to remove the "functions." namespace from our existing code. A simple search and replace will do the trick.&#x20;



**LWC**

We have also developed a UI-less LWC SDK that can be used on the users browser. This allows the user to bypass SF and call the functions directly with Salesforce data



**Server Side SDK**

The Server SDK is a .NET Nuget package or Opensource C# code. The primary services provided by the SDK are&#x20;

* Bi directional connection to Salesforce
* Logging&#x20;
* User defined Security
* Accesss to Salesforce objects through Metadata and Tooling API (This uses ApesSharp Api library)
* 0MQ support for interportablity with other programing languges

