---
description: C# (Microsoft .NET) support for Salesforce functions developers.
---

# ApexSharp Functions

**Open source tools we use**

* [Net Core](https://dotnet.microsoft.com/en-us/)
* [Cloud Events](https://cloudevents.io/)
* [0MQ](https://zeromq.org/)
* [Kubernetes ](https://kubernetes.io/)and [Paketo](https://paketo.io/)

The client-server communication is based on open standards ([Cloud Events](https://cloudevents.io/)) . The server application you have written can be used by any [Cloud Events](https://cloudevents.io/) client. Cloud Events provides SDKs for Go, JavaScript, Java, C#, Ruby, PHP, PowerShell, Rust, and Python

The client Apex code has very simple open API. This allows Salesforce to call the client API from Apex, LWC, Flow, Flow for Industry (Omniscript, Integration Procedure, Flex cards)&#x20;



**Sinc server-sider side SDK is written in C#; it can run anywhere you can run .Net Code**

* Heroku.&#x20;
  * Net Core using [Paketo](https://paketo.io/) Buildpacks
  * Connect to Heroku Postgres, Radis, Apache Kafka as all of them have C# drivers
  * For dealing with large-scale Salesforce data, use Heroku Connect
* Amazon AWS
* Microsoft Azure
  * [Azure Functions](https://azure.microsoft.com/en-us/products/functions/) : This event-driven serverless service is the easiest open with all most zero management.&#x20;
  * [App Service](https://azure.microsoft.com/en-us/products/app-service/)
  * [Azure Virtual Machines](https://azure.microsoft.com/en-us/products/virtual-machines/) and [Azure Virtual Machine Scale Sets](https://azure.microsoft.com/en-us/products/virtual-machine-scale-sets/)
  * [Azure Container Apps](https://azure.microsoft.com/en-us/products/container-apps)
  * [Azure Dedicated Host](https://azure.microsoft.com/en-us/pricing/details/virtual-machines/dedicated-host/) (_How about 400 CPU Cores and 10 TB of Ram on a single PC_)
  * GPU optimized virtual machine for your AI / ML needs (User C# based [ML.NET](https://learn.microsoft.com/en-us/dotnet/machine-learning/))
  * FPGA attestation for Azure, use [Hastlayer](https://hastlayer.com/project) for C# binding
  * [Azure provides well over 200 services](https://azure.microsoft.com/en-us/products/), all these services provide C# SDK thus you can extend your server side functions to use these services.&#x20;
* Google Cloud
* Kubernetes Container providers such as [Digitalocean](https://www.digitalocean.com/) and [Vultr ](https://www.vultr.com/)
* Raspberry Pi. Net Core is available on Pi and our server side code is 100% Net Core
* Your own Datacenter with Windows, Linux, Macs



**Advantages of ApexSharp Functions over Salesforce Functions** &#x20;

* Open source, which means its free.
* Using open standards allows it to work with any type of clients, not just Salesforce
* You have a large pool of C# developers Net Core is available on Windows, Linux and Mac
* C# has a very large 3rd party library support, over 350K packages on [NuGet](https://www.nuget.org/)
* The server SDK is Net Core thus languages such as F# and Visual Basic can use it, not just C#
* Visual Studio is the most advanced IDE in the market
* Allows Client and Server to be developed locally
  * Use[ Ngrok ](https://ngrok.com/product/secure-tunnels)or [Tailscale](https://tailscale.com/pricing/) if you are behind a firewall
  * Use [ApexSharp](https://github.com/apexsharp/) to develop Apex code locally
* No cloud vendor lock-in as code runs anywhere their is .NET support
* Ability to scale the cloud services (1 CPU Cores to [96 Cores](https://www.amd.com/en/products/cpu/amd-epyc-9654p)) or use Kubernetes for unlimited scalability.&#x20;
* Build AI applications using[ GPU Cloud](https://lambdalabs.com/service/gpu-cloud) and Microsoft [ML Framework](https://learn.microsoft.com/en-us/dotnet/machine-learning/)
* Integrate with applications written in other programming languages using [0MQ](https://zeromq.org/)
* Centralized logging, Salesforce and Apex Functions log combined on [Seq](https://datalust.co/seq)&#x20;
  * We are using [NebulaLogger ](https://github.com/jongpie/NebulaLogger)and [Serilog](https://serilog.net/)
* Server can be hosted on a environment that provides a Net Core runtime, any thing from a [3$/Month ARM ](https://www.vultr.com/pricing/#cloud-compute/) to a [$14,000/Month monster server with 8x NVIDIA A100, 1TB of Ram, 12 TB of SSD and 48 Cores. ](https://www.vultr.com/pricing/#bare-metal)



**Advantages of Salesforce Functions over ApexSharp Functions**

* ApexSharp Functions (ASF) is an open source project that is still in development. With Salesforce Function you get a backing of a 200 billion company.&#x20;
* You are dealing with one vendor and one team for your business and technical issues
* How Salesforce functions communicate to Heroku is internal to Salesforce. In ASF we use the public network. You can always host on Amazon AWS and use [Salesforce Private Connect](https://help.salesforce.com/s/articleView?id=sf.private\_connect\_overview.htm\&type=5) for a direct connection between your AWS services and SF.
* From a network view Salesforce and Heroku can be considered a single network thus providing lower latency and an extra security layer. Your data never leaves Salesforce owned and operated system.&#x20;





**High Level Architecture**

<figure><img src="../.gitbook/assets/image.png" alt=""><figcaption></figcaption></figure>

**How it all works**

ApexSharp contains two parts, client SDK and server SDK. The client SDK is Apex and it runs on your Salesforce org. You can call the client using Apex, LWC, Flow or Flow for Industry.&#x20;

The server SDK is a based on C# that is compatible with any .NET languages and runs on any environment where you can run .NET Core.&#x20;

**Apex Client SDK**

The Apex code runs in your Salesforce environment and implements all the [Salesforce functions API](https://developer.salesforce.com/docs/atlas.en-us.apexref.meta/apexref/apex\_namespace\_functions.htm). If you have existing Salesforce Apex code for functions, you will only need to make very small modification

Original Salesforce Code

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

As you see all we have to do is to remove the **"functions."** namespace from you existing code.&#x20;

For example

<mark style="color:red;">f</mark>_<mark style="color:red;">unctions.</mark>Function accountFunction = <mark style="color:red;">functions.</mark>Function.get('MyProject.AccountFunction');_

Turns to

_Function accountFunction = Function.get('MyProject.AccountFunction');_

A simple search and replace will do the trick.&#x20;



**LWC, Flow, Flow for Industry**

A UI-less LWC SDK is in the works, this can be used on Salesforce pages, Flow and Flow for Industry.&#x20;



**Server Side SDK**

The Server SDK is a .NET Nuget package and Open Source C# code and based on [ApexSharp ](https://github.com/apexsharp/), which has become a Salesforce approved open source project &#x20;

The primary services provided by the SDK are&#x20;

* Bi directional connection to Salesforce
* Logging&#x20;
* User defined Security
* Access to Salesforce objects through Metadata and Tooling API&#x20;
* 0MQ support for interoperability with other programing languages

**Web Assembly on Server**

A lot of work is going on in running applications on Web Assembly container that supports [C#](https://blog.jetbrains.com/dotnet/2022/12/15/the-future-of-net-with-wasm/) . Currently I working with [Fermyon. ](https://www.fermyon.com/blog/webassembly-for-dotnet-developers-spin-sdk-intro)The biggest advantage of Web Assembly are

* Applications developed using multiple languages can be compiled into a single image
* The small size allows it to be run on IoT devices
* All most all the edge based servelss vendors are supporting Web Assembly



