---
title: "Azure Functions C# スクリプト開発者向けリファレンス | Microsoft Docs"
description: "C# を使用して Azure Functions を開発する方法について説明します。"
services: functions
documentationcenter: na
author: lindydonna
manager: cfowler
editor: 
tags: 
keywords: "Azure Functions, 機能, イベント処理, Webhook, 動的コンピューティング, サーバーなしのアーキテクチャ"
ms.assetid: f28cda01-15f3-4047-83f3-e89d5728301c
ms.service: functions
ms.devlang: dotnet
ms.topic: reference
ms.tgt_pltfrm: multiple
ms.workload: na
ms.date: 06/07/2017
ms.author: donnam
ms.translationtype: HT
ms.sourcegitcommit: 8ad98f7ef226fa94b75a8fc6b2885e7f0870483c
ms.openlocfilehash: c9dfd3e3b9c155255959f76fd9b58b6935888db2
ms.contentlocale: ja-jp
ms.lasthandoff: 09/29/2017

---
# <a name="azure-functions-c-script-developer-reference"></a>Azure Functions C# スクリプト開発者向けリファレンス
> [!div class="op_single_selector"]
> * [C# スクリプト](functions-reference-csharp.md)
> * [F# スクリプト](functions-reference-fsharp.md)
> * [Node.JS](functions-reference-node.md)
>
>

Azure Functions の C# スクリプト エクスペリエンスは、Azure WebJobs SDK に基づいています。 データは、メソッドの引数を使用して C# 関数に渡されます。 引数名は `function.json`で指定され、関数のロガーやキャンセル トークンなどにアクセスするための定義済みの名前があります。

この記事では、「 [Azure Functions developer reference (Azure Functions 開発者向けリファレンス)](functions-reference.md)」を既に読んでいることを前提としています。

C# クラス ライブラリの使用の詳細については、「[Using .NET class libraries with Azure Functions](functions-dotnet-class-library.md)」(Azure Functions での .NET クラス ライブラリの使用) を参照してください。

## <a name="how-csx-works"></a>.csx のしくみ
`.csx` の形式では、"定型" の記述が少なく、C# 関数のみの記述に重点が置かれています。 通常どおり、すべてのアセンブリ参照と名前空間をファイルの先頭に含めます。 名前空間およびクラスにすべてをラップするのではなく、`Run` メソッドを定義するだけです。 Plain Old CLR Object (POCO) オブジェクトを定義する場合など、クラスを含める必要がある場合は、同じファイル内にクラスを含めることができます。   

## <a name="binding-to-arguments"></a>引数へのバインド
*function.json* 構成の `name` プロパティを通じて、さまざまなバインドが C# 関数にバインドされます。 各バインドには、独自のサポートされている型があります。たとえば、blob のトリガーでは、文字列、POCO、または CloudBlockBlob がサポートされます。 サポートされる型は、各バインドの参照に記載されています。 POCO オブジェクトでは、各プロパティに getter と setter が定義されている必要があります。

```csharp
public static void Run(string myBlob, out MyClass myQueueItem)
{
    log.Verbose($"C# Blob trigger function processed: {myBlob}");
    myQueueItem = new MyClass() { Id = "myid" };
}

public class MyClass
{
    public string Id { get; set; }
}
```

[!INCLUDE [HTTP client best practices](../../includes/functions-http-client-best-practices.md)]

## <a name="using-method-return-value-for-output-binding"></a>出力バインドのメソッド戻り値の使用

出力バインドにメソッド戻り値を使用できます。そのためには、*function.json* の名前 `$return` を使用します。

```json
{
    "type": "queue",
    "direction": "out",
    "name": "$return",
    "queueName": "outqueue",
    "connection": "MyStorageConnectionString",
}
```

```csharp
public static string Run(string input, TraceWriter log)
{
    return input;
}
```

## <a name="writing-multiple-output-values"></a>複数の出力値の書き込み

出力バインドに複数の値を書き込むには、[`ICollector`](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs/ICollector.cs) 型または [`IAsyncCollector`](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs/IAsyncCollector.cs) 型を使用します。 これらの型は、メソッド完了時に出力バインドに書き込まれる、書き込み専用接続です。

この例では、`ICollector` を使用して複数のキュー メッセージを書き込みます。

```csharp
public static void Run(ICollector<string> myQueueItem, TraceWriter log)
{
    myQueueItem.Add("Hello");
    myQueueItem.Add("World!");
}
```

## <a name="logging"></a>ログの記録
出力を C# のストリーミング ログにログ記録するために、`TraceWriter` 型の引数を含めます。 これの名前を `log`にすることをお勧めします。 Azure Functions では `Console.Write` を使用しないでください。 

`TraceWriter` は [Azure Web ジョブ SDK](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs.Host/TraceWriter.cs) で定義されます。 `TraceWriter` のログ レベルは、[host.json](functions-host-json.md) で構成できます。

```csharp
public static void Run(string myBlob, TraceWriter log)
{
    log.Info($"C# Blob trigger function processed: {myBlob}");
}
```

## <a name="async"></a>非同期
関数を非同期にするには、`async` キーワードを使用して `Task` オブジェクトを返します。

```csharp
public async static Task ProcessQueueMessageAsync(
        string blobName,
        Stream blobInput,
        Stream blobOutput)
{
    await blobInput.CopyToAsync(blobOutput, 4096, token);
}
```

## <a name="cancellation-token"></a>キャンセル トークン
一部の操作では、グレースフル シャットダウンが必要です。 クラッシュに対応するコードを記述するのが最善の方法ですが、グレースフル シャットダウンの要求を処理する場合は、[`CancellationToken`](https://msdn.microsoft.com/library/system.threading.cancellationtoken.aspx) 型の引数を定義します。  `CancellationToken` は、ホストのシャット ダウンがトリガーされることを通知するために用意されています。

```csharp
public async static Task ProcessQueueMessageAsyncCancellationToken(
        string blobName,
        Stream blobInput,
        Stream blobOutput,
        CancellationToken token)
    {
        await blobInput.CopyToAsync(blobOutput, 4096, token);
    }
```

## <a name="importing-namespaces"></a>名前空間のインポート
名前空間をインポートする必要がある場合は、 `using` 句を使用して、通常どおりにインポートできます。

```csharp
using System.Net;
using System.Threading.Tasks;

public static Task<HttpResponseMessage> Run(HttpRequestMessage req, TraceWriter log)
```

次の名前空間は自動的にインポートされるため、オプションとなります。

* `System`
* `System.Collections.Generic`
* `System.IO`
* `System.Linq`
* `System.Net.Http`
* `System.Threading.Tasks`
* `Microsoft.Azure.WebJobs`
* `Microsoft.Azure.WebJobs.Host`

## <a name="referencing-external-assemblies"></a>外部アセンブリの参照
フレームワークのアセンブリには、 `#r "AssemblyName"` ディレクティブを使用して参照を追加します。

```csharp
#r "System.Web.Http"

using System.Net;
using System.Net.Http;
using System.Threading.Tasks;

public static Task<HttpResponseMessage> Run(HttpRequestMessage req, TraceWriter log)
```

次のアセンブリは、Azure Functions をホストしている環境によって自動的に追加されます。

* `mscorlib`
* `System`
* `System.Core`
* `System.Xml`
* `System.Net.Http`
* `Microsoft.Azure.WebJobs`
* `Microsoft.Azure.WebJobs.Host`
* `Microsoft.Azure.WebJobs.Extensions`
* `System.Web.Http`
* `System.Net.Http.Formatting`

次のアセンブリは、単純な名前で参照できます (たとえば、`#r "AssemblyName"`)。

* `Newtonsoft.Json`
* `Microsoft.WindowsAzure.Storage`
* `Microsoft.ServiceBus`
* `Microsoft.AspNet.WebHooks.Receivers`
* `Microsoft.AspNet.WebHooks.Common`
* `Microsoft.Azure.NotificationHubs`

## <a name="referencing-custom-assemblies"></a>カスタム アセンブリの参照

カスタム アセンブリを参照するために、*共有*アセンブリまたは*プライベート* アセンブリのいずれかを使用できます。
- 共有アセンブリは、関数アプリ内のすべての関数にわたって共有されます。 カスタム アセンブリを参照するには、そのアセンブリを関数アプリにアップロードします (たとえば、関数アプリのルートの `bin` フォルダーにアップロード)。 
- プライベート アセンブリは、特定の関数のコンテキストの一部であり、異なるバージョンのサイドローディングをサポートします。 プライベート アセンブリを関数ディレクトリ の `bin` フォルダーにアップロードする必要があります。 `#r "MyAssembly.dll"` などのファイル名を使用して参照します。 

関数フォルダーにファイルをアップロードする方法については、パッケージ管理の次のセクションを参照してください。

### <a name="watched-directories"></a>監視対象のディレクトリ

関数のスクリプト ファイルを含むディレクトリは、アセンブリの変更を自動的に監視されています。 その他のディレクトリでアセンブリの変更を監視するには、[host.json](functions-host-json.md) の `watchDirectories` の一覧にそのディレクトリを追加します。

## <a name="using-nuget-packages"></a>NuGet パッケージを使用する
NuGet パッケージを C# 関数で使用するには、*project.json* ファイルを関数アプリのファイル システムにある関数のフォルダーにアップロードします。 Microsoft.ProjectOxford.Face バージョン 1.1.0 への参照を追加する *project.json* ファイルの例を次に示します。

```json
{
  "frameworks": {
    "net46":{
      "dependencies": {
        "Microsoft.ProjectOxford.Face": "1.1.0"
      }
    }
   }
}
```

.NET Framework 4.6 のみがサポートされているので、次に示すように *project.json* ファイルが `net46` を指定していることを確認します。

*project.json* ファイルをアップロードすると、ランタイムによってパッケージが取得され、パッケージ アセンブリに参照が自動的に追加されます。 `#r "AssemblyName"` ディレクティブを追加する必要はありません。 NuGet パッケージで定義されている型を使用するには、必要な `using` ステートメントを *run.csx* ファイルに追加します。 

Functions ランタイムでは、NuGet は `project.json` と `project.lock.json` を比較して作業を復元します。 ファイルの日付と時刻のタイムスタンプが**一致しない**場合は、NuGet 復元が実行され、NuGet は更新されたパッケージをダウンロードします。 ただし、ファイルの日付と時刻のタイムスタンプが**一致した**場合、NuGet は復元を実行しません。 したがって、NuGet がパッケージの復元をスキップするため、`project.lock.json` はデプロイされません。 ロック ファイルのデプロイを回避するには、`project.lock.json` を `.gitignore` ファイルに追加します。

カスタムの NuGet フィードを使用するには、関数アプリのルートにある *Nuget.Config* ファイルでフィードを指定します。 詳しくは、「[NuGet の動作の構成](/nuget/consume-packages/configuring-nuget-behavior)」をご覧ください。

### <a name="using-a-projectjson-file"></a>Project.json ファイルの使用
1. Azure ポータルで関数を開きます。 [ログ] タブには、パッケージのインストールの出力が表示されます。
2. project.json ファイルをアップロードするには、「Azure Functions developer reference (Azure Functions 開発者向けリファレンス)」の「[関数アプリ ファイルを更新する方法](functions-reference.md#fileupdate)」にあるいずれかの方法を利用してください。
3. *project.json* ファイルがアップロードされた後、関数のストリーミング ログの出力は次の例のようになります。

```
2016-04-04T19:02:48.745 Restoring packages.
2016-04-04T19:02:48.745 Starting NuGet restore
2016-04-04T19:02:50.183 MSBuild auto-detection: using msbuild version '14.0' from 'D:\Program Files (x86)\MSBuild\14.0\bin'.
2016-04-04T19:02:50.261 Feeds used:
2016-04-04T19:02:50.261 C:\DWASFiles\Sites\facavalfunctest\LocalAppData\NuGet\Cache
2016-04-04T19:02:50.261 https://api.nuget.org/v3/index.json
2016-04-04T19:02:50.261
2016-04-04T19:02:50.511 Restoring packages for D:\home\site\wwwroot\HttpTriggerCSharp1\Project.json...
2016-04-04T19:02:52.800 Installing Newtonsoft.Json 6.0.8.
2016-04-04T19:02:52.800 Installing Microsoft.ProjectOxford.Face 1.1.0.
2016-04-04T19:02:57.095 All packages are compatible with .NETFramework,Version=v4.6.
2016-04-04T19:02:57.189
2016-04-04T19:02:57.189
2016-04-04T19:02:57.455 Packages restored.
```

## <a name="environment-variables"></a>環境変数
環境変数またはアプリ設定値を取得するには、次のコード例のように、 `System.Environment.GetEnvironmentVariable`を使用します。

```csharp
public static void Run(TimerInfo myTimer, TraceWriter log)
{
    log.Info($"C# Timer trigger function executed at: {DateTime.Now}");
    log.Info(GetEnvironmentVariable("AzureWebJobsStorage"));
    log.Info(GetEnvironmentVariable("WEBSITE_SITE_NAME"));
}

public static string GetEnvironmentVariable(string name)
{
    return name + ": " +
        System.Environment.GetEnvironmentVariable(name, EnvironmentVariableTarget.Process);
}
```

## <a name="reusing-csx-code"></a>.csx コードの再利用
他の *.csx* ファイルで定義されたクラスとメソッドを、*run.csx* ファイルで使用できます。 そのためには、*run.csx* ファイル内で `#load` ディレクティブを使用します。 次の例では、`MyLogger` という名前のログ記録ルーチンが *myLogger.csx* 内で共有され、`#load` ディレクティブを使用して *run.csx* に読み込まれます。

*run.csx*の例:

```csharp
#load "mylogger.csx"

public static void Run(TimerInfo myTimer, TraceWriter log)
{
    log.Verbose($"Log by run.csx: {DateTime.Now}");
    MyLogger(log, $"Log by MyLogger: {DateTime.Now}");
}
```

*mylogger.csx*の例:

```csharp
public static void MyLogger(TraceWriter log, string logtext)
{
    log.Verbose(logtext);
}
```

共有された *.csx* の使用は、POCO オブジェクトを使用して関数間の引数を厳密に型宣言する場合の一般的なパターンです。 次の簡略化された例では、HTTP トリガーとキュー トリガーが `Order` という名前の POCO オブジェクトを共有して、注文データを厳密に型宣言しています。

例: HTTP トリガーの *run.csx*

```cs
#load "..\shared\order.csx"

using System.Net;

public static async Task<HttpResponseMessage> Run(Order req, IAsyncCollector<Order> outputQueueItem, TraceWriter log)
{
    log.Info("C# HTTP trigger function received an order.");
    log.Info(req.ToString());
    log.Info("Submitting to processing queue.");

    if (req.orderId == null)
    {
        return new HttpResponseMessage(HttpStatusCode.BadRequest);
    }
    else
    {
        await outputQueueItem.AddAsync(req);
        return new HttpResponseMessage(HttpStatusCode.OK);
    }
}
```

例: キュー トリガーの *run.csx*

```cs
#load "..\shared\order.csx"

using System;

public static void Run(Order myQueueItem, out Order outputQueueItem,TraceWriter log)
{
    log.Info($"C# Queue trigger function processed order...");
    log.Info(myQueueItem.ToString());

    outputQueueItem = myQueueItem;
}
```

例: *order.csx*

```cs
public class Order
{
    public string orderId {get; set; }
    public string custName {get; set;}
    public string custAddress {get; set;}
    public string custEmail {get; set;}
    public string cartId {get; set; }

    public override String ToString()
    {
        return "\n{\n\torderId : " + orderId +
                  "\n\tcustName : " + custName +             
                  "\n\tcustAddress : " + custAddress +             
                  "\n\tcustEmail : " + custEmail +             
                  "\n\tcartId : " + cartId + "\n}";             
    }
}
```

`#load` ディレクティブで相対パスを使用できます。

* `#load "mylogger.csx"` によって、関数フォルダーにあるファイルが読み込まれます。
* `#load "loadedfiles\mylogger.csx"` によって、関数フォルダー内のフォルダーにあるファイルが読み込まれます。
* `#load "..\shared\mylogger.csx"` によって、関数フォルダーと同じレベル ( *wwwroot*の直下) にあるフォルダーのファイルが読み込まれます。

`#load` ディレクティブは、*.csx* (C# スクリプト) ファイルでのみ機能し、*.cs* ファイルでは機能しません。

<a name="imperative-bindings"></a> 

## <a name="binding-at-runtime-via-imperative-bindings"></a>命令型のバインドを利用した実行時のバインド

C# および他の .NET 言語では、*function.json* の[*宣言型*](https://en.wikipedia.org/wiki/Declarative_programming)のバインドではなく[命令型](https://en.wikipedia.org/wiki/Imperative_programming)のバインド パターンを使用できます。 命令型のバインドは、設計時ではなくランタイム時にバインド パラメーターを計算する必要がある場合に便利です。 このパターンを使用すると、サポートされている入力バインドと出力バインドに関数コード内でバインドできます。

次のように命令型のバインドを定義します。

- 必要な命令型のバインドの *function.json* にエントリを**含めないで**ください。
- 入力パラメーター [`Binder binder`](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs.Host/Bindings/Runtime/Binder.cs) または [`IBinder binder`](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs/IBinder.cs) を渡します。
- 次の C# パターンを使用してデータ バインドを実行します。

```cs
using (var output = await binder.BindAsync<T>(new BindingTypeAttribute(...)))
{
    ...
}
```

`BindingTypeAttribute` はバインドを定義する .NET 属性、`T` はそのバインドの種類でサポートされている入力または出力の型です。 `T` も `out` パラメーター型 (`out JObject` など) にすることはできません。 たとえば、Mobile Apps テーブルの出力バインドは [6 種類の出力](https://github.com/Azure/azure-webjobs-sdk-extensions/blob/master/src/WebJobs.Extensions.MobileApps/MobileTableAttribute.cs#L17-L22)をサポートしますが、`T` に使用できるのは [ICollector<T>](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs/ICollector.cs) または [IAsyncCollector<T>](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs/IAsyncCollector.cs) のみです。

次のコード例は、実行時に BLOB パスが定義された [Storage Blob の出力バインド](functions-bindings-storage-blob.md#using-a-blob-output-binding)を作成し、この BLOB に文字列を書き込みます。

```cs
using Microsoft.Azure.WebJobs;
using Microsoft.Azure.WebJobs.Host.Bindings.Runtime;

public static async Task Run(string input, Binder binder)
{
    using (var writer = await binder.BindAsync<TextWriter>(new BlobAttribute("samples-output/path")))
    {
        writer.Write("Hello World!!");
    }
}
```

[BlobAttribute](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs/BlobAttribute.cs) は [Storage Blob](functions-bindings-storage-blob.md) の入力バインドまたは出力バインドを定義します。[TextWriter](https://msdn.microsoft.com/library/system.io.textwriter.aspx) はサポートされている出力バインドの種類です。
このコードは、ストレージ アカウントの接続文字列 (`AzureWebJobsStorage`) の既定のアプリケーション設定を取得します。 [StorageAccountAttribute](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs/StorageAccountAttribute.cs) を追加し、属性の配列を `BindAsync<T>()` に渡すことで、使用するカスタムなアプリケーション設定を指定できます。 たとえば、次のように入力します。

```cs
using Microsoft.Azure.WebJobs;
using Microsoft.Azure.WebJobs.Host.Bindings.Runtime;

public static async Task Run(string input, Binder binder)
{
    var attributes = new Attribute[]
    {    
        new BlobAttribute("samples-output/path"),
        new StorageAccountAttribute("MyStorageAccount")
    };

    using (var writer = await binder.BindAsync<TextWriter>(attributes))
    {
        writer.Write("Hello World!");
    }
}
```

次の表に、各バインドの種類の .NET 属性と、それらが定義されているパッケージを示します。

> [!div class="mx-codeBreakAll"]
| バインド | Attribute | 参照の追加 |
|------|------|------|
| Cosmos DB | [`Microsoft.Azure.WebJobs.DocumentDBAttribute`](https://github.com/Azure/azure-webjobs-sdk-extensions/blob/master/src/WebJobs.Extensions.DocumentDB/DocumentDBAttribute.cs) | `#r "Microsoft.Azure.WebJobs.Extensions.DocumentDB"` |
| Event Hubs | [`Microsoft.Azure.WebJobs.ServiceBus.EventHubAttribute`](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs.ServiceBus/EventHubs/EventHubAttribute.cs)、[`Microsoft.Azure.WebJobs.ServiceBusAccountAttribute`](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs.ServiceBus/ServiceBusAccountAttribute.cs) | `#r "Microsoft.Azure.Jobs.ServiceBus"` |
| Mobile Apps | [`Microsoft.Azure.WebJobs.MobileTableAttribute`](https://github.com/Azure/azure-webjobs-sdk-extensions/blob/master/src/WebJobs.Extensions.MobileApps/MobileTableAttribute.cs) | `#r "Microsoft.Azure.WebJobs.Extensions.MobileApps"` |
| Notification Hubs | [`Microsoft.Azure.WebJobs.NotificationHubAttribute`](https://github.com/Azure/azure-webjobs-sdk-extensions/blob/master/src/WebJobs.Extensions.NotificationHubs/NotificationHubAttribute.cs) | `#r "Microsoft.Azure.WebJobs.Extensions.NotificationHubs"` |
| Service Bus | [`Microsoft.Azure.WebJobs.ServiceBusAttribute`](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs.ServiceBus/ServiceBusAttribute.cs)、[`Microsoft.Azure.WebJobs.ServiceBusAccountAttribute`](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs.ServiceBus/ServiceBusAccountAttribute.cs) | `#r "Microsoft.Azure.WebJobs.ServiceBus"` |
| ストレージ キュー | [`Microsoft.Azure.WebJobs.QueueAttribute`](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs/QueueAttribute.cs)、[`Microsoft.Azure.WebJobs.StorageAccountAttribute`](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs/StorageAccountAttribute.cs) | |
| Storage Blob | [`Microsoft.Azure.WebJobs.BlobAttribute`](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs/BlobAttribute.cs)、[`Microsoft.Azure.WebJobs.StorageAccountAttribute`](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs/StorageAccountAttribute.cs) | |
| ストレージ テーブル | [`Microsoft.Azure.WebJobs.TableAttribute`](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs/TableAttribute.cs)、[`Microsoft.Azure.WebJobs.StorageAccountAttribute`](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs/StorageAccountAttribute.cs) | |
| Twilio | [`Microsoft.Azure.WebJobs.TwilioSmsAttribute`](https://github.com/Azure/azure-webjobs-sdk-extensions/blob/master/src/WebJobs.Extensions.Twilio/TwilioSMSAttribute.cs) | `#r "Microsoft.Azure.WebJobs.Extensions.Twilio"` |



## <a name="next-steps"></a>次のステップ
詳細については、次のリソースを参照してください。

* [Azure Functions のベスト プラクティス](functions-best-practices.md)
* [Azure Functions 開発者向けリファレンス](functions-reference.md)
* [Azure Functions F# 開発者向けリファレンス](functions-reference-fsharp.md)
* [Azure Functions NodeJS 開発者向けリファレンス](functions-reference-node.md)
* [Azure Functions のトリガーとバインドに関する記事](functions-triggers-bindings.md)

