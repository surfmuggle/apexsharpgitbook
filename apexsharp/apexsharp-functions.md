---
description: C# (Microsoft .NET) Server Side SDK for Salesforce Functions
---

# ApexSharp Functions

Please note that this project is not yet available to the public because

1. Currently, in alpha, this is just a proof-of-concept showing how Salesforce Functions can support C# on the server side and also various hosting option, not just Heroku
2. Although I work on these types of projects on my own time, Salesforce OSS legal must approve them before they can be made public, as Salesforce still employs me

If you have questions or comments, you can reach me on [LinkedIn ](https://www.linkedin.com/in/jayonsoftware/)



**Goal**

Provide a Cloud vendor agnostic C# based server-side SDK for Salesforce Functions that is compatible with the open [Salesforce Functions API](salesforce-functions-api.md)

**Open-source tools used in this project**

* [Net Core](https://dotnet.microsoft.com/en-us/)
* [Cloud Events](https://cloudevents.io/)
* [0MQ](https://zeromq.org/) / [gRPC](https://grpc.io/)
* [ApexSharp](https://github.com/apexsharp/apexsharp)

The client Apex code has a very simple open API. This allows Salesforce to call the client API from Apex, LWC, Flow, and Flow for Industry (Omniscript, Integration Procedure, Flex cards). Currently, the Apex API we provide allows 1:1 mapping with the current [Functions Apex](https://developer.salesforce.com/docs/atlas.en-us.242.0.apexref.meta/apexref/apex\_namespace\_functions.htm) implementation. &#x20;



**The C# SDK can be used with .Net supported provider. Following is a list of providers we are testing.**&#x20;

* Heroku.&#x20;
  * Net Core using [Paketo](https://paketo.io/) Buildpacks
  * Connect to Heroku Postgres, Radis, and Apache Kafka, as all of them have C# drivers
  * For dealing with large-scale Salesforce data, use Heroku Connect
* Amazon AWS
* Microsoft Azure
  * [Azure Functions](https://azure.microsoft.com/en-us/products/functions/): This event-driven serverless service is the easiest option with zero management and automatic scalability.&#x20;
  * [App Service](https://azure.microsoft.com/en-us/products/app-service/): Easy to use Web API hosting service.&#x20;
  * [Azure Virtual Machines](https://azure.microsoft.com/en-us/products/virtual-machines/) and [Azure Virtual Machine Scale Sets](https://azure.microsoft.com/en-us/products/virtual-machine-scale-sets/)
  * [Azure Container Apps](https://azure.microsoft.com/en-us/products/container-apps)
  * [Azure Dedicated Host](https://azure.microsoft.com/en-us/pricing/details/virtual-machines/dedicated-host/) (_How about 400 CPU Cores and 10 TB of Ram on a single PC_)
  * FPGA acceleration for Azure, using [Hastlayer](https://hastlayer.com/project) for C# binding
  * [Azure provides well over 200 services](https://azure.microsoft.com/en-us/products/); all these services provide C# SDK; thus, you can extend your server-side functions to use these services.&#x20;
* Google Cloud
* Any Kubernetes Container providers such as AWS, Azure, GCP, [Digitalocean](https://www.digitalocean.com/), and [Vultr ](https://www.vultr.com/)
* Raspberry Pi. [Net Core is available on Pi; this](https://medium.com/@cmendibl3/step-by-step-running-asp-net-core-on-raspberry-pi-9ef224f2e750) allows you to build IoT devices that Salesforce can communicate with
* Functions can be deployed to edge service providers such as [fly.io](https://fly.io/docs/reference/builders/#buildpacks) as they support Paketo buildpacks &#x20;
* GPU-optimized virtual machine for your AI / ML needs. Use services such as[ GPU Cloud](https://lambdalabs.com/service/gpu-cloud)&#x20;
  * [ILGPU ](https://github.com/m4rs-mt/ILGPU)for advanced math
  * [ML.NET ](https://dotnet.microsoft.com/en-us/apps/machinelearning-ai/ml-dotnet)for Machin Learning, including Open A
* Your Datacenter or Desktop with Windows, Linux, or Mac. Use[ Ngrok ](https://ngrok.com/product/secure-tunnels)if you are behind a firewall.

**Advantages of ApexSharp Functions C# SDK over Salesforce Functions** &#x20;

* Open source, which means it's free.
* The SDK can run anywhere. .NET is supported.&#x20;
* Use different cloud providers, from [3$Month](https://www.vultr.com/pricing/#cloud-compute/) ARM-based CPU to a [$14K/Month GPU monster. ](https://www.vultr.com/pricing/#bare-metal)
* The [limits of current Function implementation](https://developer.salesforce.com/docs/platform/functions/guide/limits#apex-limits-and-functions) can be overcome since you are in control.
* In addition to 1:1 request-reply and 1:1 callbacks, the SDK supports 1:M callbacks.&#x20;
* Open standards allow it to work with any type of client, not just Salesforce.
* The world has a large pool of C# developers.&#x20;
* C# has a very large 3rd party library support, over 350K packages on [NuGet](https://www.nuget.org/)
* The server SDK is Net Core; thus, languages such as F# and Visual Basic can be used.
* C# apps can call Node.Js code in-process using different [open-source tools. ](https://github.com/JeringTech/Javascript.NodeJS)
* Allows Client and Server to be developed locally using [ApexSharp](https://github.com/apexsharp/) to develop Apex code locally.
* Ability to scale the cloud services (1 CPU Cores to [96 Cores](https://www.amd.com/en/products/cpu/amd-epyc-9654p)) or use Kubernetes for unlimited scalability.&#x20;
* Integrate with applications written in other programming languages using [ZeroMQ.](https://zeromq.org/) ZeroMQ has support for all 30 different programming languages.  &#x20;
* Centralized logging, Salesforce, and Apex Functions log combined on [Seq](https://datalust.co/seq)&#x20;
  * We are using [NebulaLogger ](https://github.com/jongpie/NebulaLogger)and [Serilog](https://serilog.net/)
* Visual Studio is the most advanced IDE in the mark



**Advantages of Salesforce Functions on Heroku**

* Salesforce functions is a managed service product offering. Heroku takes care of everything from hardware to software to security and uptime.&#x20;
* Enterprise customers prefer to buy from Salesforce and not use open-source software.
* Enterprise customers like dealing with one vendor for Salesforce Functions from the business and technical side.
* How Salesforce functions communicate to Heroku is internal to Salesforce. From a network view, Salesforce and Heroku can be considered a single network, thus providing lower latency and an extra security layer.&#x20;
* Salesforce has the freedom to optimize the connectivity between SF and Heroku. For example, use tools such as [Riverbed SteelHead](https://www.riverbed.com/products/steelhead-interceptor/).
* While SF does not count outgoing API calls, they set limits on incoming REST calls. Function calls inbound REST API for CRUD and Callbacks. Salesforce can remove this limit for customers who are using Heroku. Note: This issue will go away with API user licenses being standard. I am testing more to see the overall limitations of REST calls inbound and outbound now.&#x20;



**How it all works**

<figure><img src="../.gitbook/assets/image.png" alt=""><figcaption></figcaption></figure>



ApexSharp contains two parts, client SDK and server SDK. The client SDK is Apex, and it runs on your Salesforce org. You can call the client using Apex, LWC, Flow, or Flow for Industry.&#x20;

The server SDK is developed C# that is compatible with any .NET languages and runs on any environment where you can run .NET Core.&#x20;

**Apex Client SDK**

The Apex code runs in your Salesforce environment and implements all the [Salesforce functions API](https://developer.salesforce.com/docs/atlas.en-us.apexref.meta/apexref/apex\_namespace\_functions.htm). If you have existing Salesforce Apex code for functions, you will only need to make very small modification

Original Salesforce Functions Code

```apex
public static string SalesforceFunctionGet() {
  functions.Function accountFunction = functions.Function
    .get('MyProject.AccountFunction');
  functions.FunctionInvocation invocation = accountFunction
    .invoke('{ "accountName" : "Acct", "contactName" : "MyContact"}');
  return invocation.getResponse();     
}
```

Modified Code to work with ApexSharp Functions

```apex
public static string ApexSharpFunctionGet() {
  Function accountFunction = Function.get('MyProject.AccountFunction');
  FunctionInvocation invocation = accountFunction
    .invoke('{ "accountName" : "Acct", "contactName" : "MyContact"}');
  return invocation.getResponse();     
}
```

As you see, all we have to do is to remove the **"functions."** namespace from your existing code.&#x20;

For example

<mark style="color:red;">f</mark>_<mark style="color:red;">unctions.</mark>Function accountFunction = <mark style="color:red;">functions.</mark>Function.get('MyProject.AccountFunction');_

Turns to

_Function accountFunction = Function.get('MyProject.AccountFunction');_

A simple search and replace can do it.&#x20;



**LWC, Flow, Flow for Industry**

A UI-less LWC SDK is in the works, which can be used on Salesforce pages, Flow, and Flow for Industry.&#x20;



**Server Side SDK**

The Server SDK is a .NET Nuget package and Open Source C# code based on [ApexSharp](https://github.com/apexsharp/), which has become a Salesforce-approved open-source project.&#x20;

The primary services provided by the SDK are&#x20;

* Support for [Salesforce Functions API specification.](broken-reference)&#x20;
* 1-1 Request Reply support.&#x20;
* 1-1 and 1-M callback support.&#x20;
* Integration with [Salesforce Pub / Sub ](https://developer.salesforce.com/docs/platform/pub-sub-api/guide/intro.html)using grpC&#x20;
* Detail Logging&#x20;
* User-defined Security
* Access to Salesforce objects through Rest API to perform CRUD
* Execute Apex on Salesforce using Tooling API
* 0MQ support for interoperability with other programming languages
* Realtime monitoring of functions that are executing

**Web Assembly on Server**

A lot of work is happening in running applications on a Web Assembly container that supports [C#](https://blog.jetbrains.com/dotnet/2022/12/15/the-future-of-net-with-wasm/). Currently, I am working with [Fermyon. ](https://www.fermyon.com/blog/webassembly-for-dotnet-developers-spin-sdk-intro)The biggest advantage of Web Assembly is

* Serverless Applications developed using multiple languages can be compiled into a single image.
* The small size allows it to be run on IoT devices
* All the edge-based serverless vendors support Web Assembly

**Demo and Current Status**

Currently, I have a few very simple demos running on Microsoft Azure and a Salesforce developer sandbox.&#x20;

**Demo 1: Request-Reply: PDF Creation**

In this demo, I pass some data from Salesforce to the C# Server function running on an Azure App Service instance. This server-side code takes the data and&#x20;

1. Call the server-side code using Apex Functions, and pass an Order ID from the Order object.
2. C# Server will make CRUD calls to Salesforce and read the rest of the details.&#x20;
3. Creates an Invoice PDF using [QuestPDF ](https://github.com/QuestPDF/QuestPDF)(An amazing open-source PDF creation tool)&#x20;
4. Uploads the PDF to a CDN&#x20;
5. Returns a secure URL link of the PDF to Salesforce.&#x20;
6. Salesforce shows the generated PDF in an LWC
7. With the click of a button, the PDF can be emailed to someone, again using server-side functions.&#x20;



**Demo 2: Call Back: FTP Download and Upload.**&#x20;

The primary purpose of this demo is to show 1-M callbacks. Here is how it works

1. Salesforce calls the Function with FTP server info and Amazon S3 server info. Also, the list of the filename to download
2. While the server does the download and uploads, every 10%, it will make a call back to Salesforce to inform the progress.
3. This detail can be real-time in realtime in LWC using Platform events.&#x20;



My primary concentration now is to show how the server code can be deployed on multiple environments. Here is the list I am working on and the status.&#x20;

1. [Azure App Service](https://azure.microsoft.com/en-us/products/app-service/) (Done)
2. Heroku using Net Core [Paketo](https://paketo.io/docs/howto/dotnet-core/) Buildpacks
3. [Azure Functions ](https://azure.microsoft.com/en-us/products/functions/)
4. Kubernetes Container providers such as [Digitalocean](https://www.digitalocean.com/)
5. Dedicated Server on [Tier.Net, ](https://www.tier.net/)an ultralow cost dedicated service provider. 20 Cores and 192 GB of ram for about 150$/Month.&#x20;





