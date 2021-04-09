---
title: Azure Functions を使用する C# クラス ライブラリ関数を開発する
description: C# を使用して、クラス ライブラリとしてコードを開発して公開する方法を理解します。これは、Azure Functions ランタイムを使用してインプロセスで実行されます。
ms.topic: conceptual
ms.custom: devx-track-csharp
ms.date: 07/24/2020
ms.openlocfilehash: c7d14599ec1ebbcb94e0c0f3985a3b857f9353dc
ms.sourcegitcommit: e6de1702d3958a3bea275645eb46e4f2e0f011af
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "102563882"
---
# <a name="develop-c-class-library-functions-using-azure-functions"></a>Azure Functions を使用する C# クラス ライブラリ関数を開発する

<!-- When updating this article, make corresponding changes to any duplicate content in functions-reference-csharp.md -->

この記事では、.NET クラス ライブラリの C# を使用した Azure Functions 開発の概要を示します。

>[!IMPORTANT]
>この記事では、ランタイムを使用してインプロセスで実行される .NET クラス ライブラリ関数をサポートしています。 Functions は、ランタイムから分離された C# 関数をアウトプロセスで実行することで、.NET 5.x もサポートします。 詳細については、[.NET 分離プロセス関数](dotnet-isolated-process-guide.md)に関する記事を参照してください。

C# 開発者の方は、次の記事のいずれかに関心があるかもしれません。

| 作業の開始 | 概念| ガイド付き学習とサンプル |
|--| -- |--| 
| <ul><li>[Visual Studio の使用](functions-create-your-first-function-visual-studio.md)</li><li>[Visual Studio Code の使用](create-first-function-vs-code-csharp.md)</li><li>[コマンド ライン ツールの使用](create-first-function-cli-csharp.md)</li></ul> | <ul><li>[ホスティング オプション](functions-scale.md)</li><li>[パフォーマンスに関する考慮事項&nbsp;](functions-best-practices.md)</li><li>[Visual Studio 開発](functions-develop-vs.md)</li><li>[依存関係の挿入](functions-dotnet-dependency-injection.md)</li></ul> | <ul><li>[サーバーレス アプリケーションの作成](/learn/paths/create-serverless-applications/)</li><li>[C# のサンプル](/samples/browse/?products=azure-functions&languages=csharp)</li></ul> |

Azure Functions では、C# および C# スクリプト プログラミング言語をサポートします。 [Azure Portal での C# の使用](functions-create-function-app-portal.md)に関するガイダンスを探している場合は、[C# スクリプト (.csx) 開発者向けリファレンス](functions-reference-csharp.md)をご覧ください。

## <a name="supported-versions"></a>サポートされているバージョン

Functions ランタイムの各バージョンは、.NET の特定のバージョンと動作します。 Functions バージョンの詳細については、「[Azure Functions ランタイム バージョンの概要](functions-versions.md)」を参照してください。

次の表は、特定のバージョンの Functions と共に使用できる最上位レベルの .NET Core と .NET Framework を示しています。 

| Functions ランタイムのバージョン | 最大の .NET バージョン |
| ---- | ---- |
| Functions 3.x | .NET Core 3.1<br/>.NET 5.0<sup>1</sup> |
| Functions 2.x | .NET Core 2.2<sup>2</sup> |
| Functions 1.x | .NET Framework 4.7 |

<sup>1</sup> [アウトプロセス](dotnet-isolated-process-guide.md)で実行する必要があります。  
<sup>2</sup> 詳細については、「[Functions v2. x に関する考慮事項](#functions-v2x-considerations)」を参照してください。   

特定の古いマイナー バージョンの削除など、Azure Functions リリースに関する最新のニュースについては、[Azure App Service のお知らせ](https://github.com/Azure/app-service-announcements/issues)を閲覧してください。

### <a name="functions-v2x-considerations"></a>Functions v2. x に関する考慮事項

最新の 2.x バージョン (`~2`) を対象とする関数アプリは、.Net Core 3.1 で実行されるように自動的にアップグレードされます。 .Net Core バージョン間の破壊的変更のため、.NET Core 2.2 に対して開発およびコンパイルされたすべてのアプリが .NET Core 3.1 に安全にアップグレードできるとは限りません。 関数アプリを `~2.0` にピン留めすることで、このアップグレードをオプトアウトできます。 また、Functions は互換性のない API を検出し、.Net Core 3.1 での不適切な実行を防ぐために、お使いのアプリを `~2.0` にピン留めすることがあります。 

>[!NOTE]
>関数アプリが `~2.0` にピン留めされている状態で、このバージョンのターゲットを `~2` に変更した場合、関数アプリで中断が発生する可能性があります。 ARM テンプレートを使用してデプロイする場合は、テンプレートのバージョンを確認してください。 これが発生した場合は、バージョンをターゲット `~2.0` に戻し、互換性の問題を修正してください。 

`~2.0` を対象とする関数アプリは、.Net Core 2.2 で引き続き実行されます。 このバージョンの .NET Core では、セキュリティおよびその他のメンテナンス更新プログラムが受信されなくなりました。 詳細については、[こちらのお知らせページ](https://github.com/Azure/app-service-announcements/issues/266)を参照してください。 

できるだけ早く、お使いの関数を .NET Core 3.1 と互換性があるものにしてください。 これらの問題を解決した後は、バージョンを `~2` に戻すか、`~3` にアップグレードしてください。 Functions ランタイムのバージョンをターゲットにする方法の詳細については、「[Azure Functions ランタイム バージョンをターゲットにする方法](set-runtime-version.md)」を参照してください。

Premium または専用 (App Service) プランで Linux 上で実行している場合は、`linuxFxVersion` サイトの構成設定を `DOCKER|mcr.microsoft.com/azure-functions/dotnet:2.0.14786-appservice` に設定して、代わりに特定のイメージをターゲットにすることで、バージョンをピン留めします。`linuxFxVersion` の設定方法については、「[Linux 上でのバージョンの手動更新](set-runtime-version.md#manual-version-updates-on-linux)」を参照してください。

## <a name="functions-class-library-project"></a>関数クラス ライブラリ プロジェクト

Visual Studio では、**Azure Functions** プロジェクト テンプレートは、次のファイルを含む C# クラス ライブラリ プロジェクトを作成します。

* [host.json](functions-host-json.md) - ローカルまたは Azure 内で実行される場合に、プロジェクト内のすべての関数に影響を及ぼす構成設定を格納します。
* [local.settings.json](functions-run-local.md#local-settings-file) - ローカルで実行される場合に使用されるアプリ設定および接続文字列を格納します。 このファイルにはシークレットが含まれていて、Azure の関数アプリには公開されません。 代わりに、[関数アプリにアプリ設定を追加](functions-develop-vs.md#function-app-settings)します。

プロジェクトをビルドするときに、次の例のようなフォルダー構造がビルドの出力ディレクトリに作成されます。

```
<framework.version>
 | - bin
 | - MyFirstFunction
 | | - function.json
 | - MySecondFunction
 | | - function.json
 | - host.json
```

このディレクトリは、Azure 上で関数アプリにデプロイされるディレクトリです。 Functions ランタイムの[バージョン 2.x](functions-versions.md) に必要なバインディング拡張機能は、[NuGet パッケージとしてプロジェクトに追加](./functions-bindings-register.md#vs)されます。

> [!IMPORTANT]
> ビルド処理では、関数ごとに *function.json* ファイルが作成されます。 この *function.json* ファイルに対しては、直接編集は行われません。 このファイルを編集して、バインド構成を変更したり、関数を無効にしたりすることはできません。 関数を無効にする方法については、[関数を無効にする方法](disable-function.md)に関するページをご覧ください。


## <a name="methods-recognized-as-functions"></a>関数として認識されるメソッド

クラス ライブラリでは、次の例に示すように 関数は `FunctionName` を使用した静的メソッドです。

```csharp
public static class SimpleExample
{
    [FunctionName("QueueTrigger")]
    public static void Run(
        [QueueTrigger("myqueue-items")] string myQueueItem, 
        ILogger log)
    {
        log.LogInformation($"C# function processed: {myQueueItem}");
    }
} 
```

`FunctionName` 属性は、関数のエントリ ポイントとしてメソッドをマークします。 名前はプロジェクト内で一意であり、文字で始まり、英数字、`_`、`-` のみが含まれ、127 文字以下にする必要があります。 プロジェクト テンプレートでは、多くの場合、`Run` という名前のメソッドが作成されますが、有効な C# メソッド名であればメソッド名として使用できます。

トリガー属性は、トリガーの種類を指定し、メソッド パラメーターに入力データをバインドします。 例の関数は 1 つのクエリ メッセージによってトリガーされ、そのクエリ メッセージは `myQueueItem` パラメーターでメソッドに渡されます。

## <a name="method-signature-parameters"></a>メソッド シグネチャのパラメーター

メソッド シグネチャには、トリガー属性で使用されているもの以外のパラメーターが含まれる場合があります。 含めることができるその他のパラメーターの一部を以下に示します。

* 属性で修飾することによってそのようにマークした[入出力のバインド](functions-triggers-bindings.md)。  
* [ログ記録](#logging)のための `ILogger` または `TraceWriter` ([バージョン 1.x のみ](functions-versions.md#creating-1x-apps)) パラメーター。
* [グレースフル シャットダウン](#cancellation-tokens)のための `CancellationToken` パラメーター。
* トリガー メタデータを取得するための[バインド式](./functions-bindings-expressions-patterns.md)パラメーター。

関数シグネチャ内のパラメーターの順序は問題ではありません。 たとえば、トリガー パラメーターは、他のバインドの前後に配置できます。また、ロガー パラメーターは、トリガー パラメーターまたはバインド パラメーターの前後に配置できます。

### <a name="output-bindings"></a>出力バインディング

関数では、出力パラメーターを使用して、0 個または 1 個の出力バインドを定義できます。 

次の例では、`myQueueItemCopy` という名前の出力キュー バインドを追加することで、前のものを変更しています。 この関数は、関数をトリガーするメッセージの内容を、異なるキュー内の新しいメッセージに書き込みます。

```csharp
public static class SimpleExampleWithOutput
{
    [FunctionName("CopyQueueMessage")]
    public static void Run(
        [QueueTrigger("myqueue-items-source")] string myQueueItem, 
        [Queue("myqueue-items-destination")] out string myQueueItemCopy,
        ILogger log)
    {
        log.LogInformation($"CopyQueueMessage function processed: {myQueueItem}");
        myQueueItemCopy = myQueueItem;
    }
}
```

出力バインドに代入された値は、関数が終了したときに書き込まれます。 1 つの関数で複数の出力バインドを使用するには、複数の出力パラメーターに値を代入するだけです。 

バインドの参照についての記事 (たとえば「[Storage キュー](functions-bindings-storage-queue.md)」) では、トリガー、入力、または出力のバインド属性で、どのパラメーターの種類を使用できるかを説明しています。

### <a name="binding-expressions-example"></a>バインド式の例

次のコードでは、アプリ設定から監視するキューの名前を取得し、`insertionTime` パラメーターで、キュー メッセージの作成時刻を取得しています。

```csharp
public static class BindingExpressionsExample
{
    [FunctionName("LogQueueMessage")]
    public static void Run(
        [QueueTrigger("%queueappsetting%")] string myQueueItem,
        DateTimeOffset insertionTime,
        ILogger log)
    {
        log.LogInformation($"Message content: {myQueueItem}");
        log.LogInformation($"Created at: {insertionTime}");
    }
}
```

## <a name="autogenerated-functionjson"></a>自動生成される function.json

ビルド処理では、ビルド フォルダー内の関数フォルダーに *function.json* ファイルを作成します。 前述のとおり、このファイルに対しては直接編集が行われません。 このファイルを編集して、バインド構成を変更したり、関数を無効にしたりすることはできません。 

このファイルの目的は、[従量課金プランでのスケーリングの判断](event-driven-scaling.md)に使用するスケール コントローラーに情報を提供することです。 このため、ファイルにはトリガー情報のみが含まれ、入力および出力バインドは含まれません。

生成された *function.json* ファイルには、*function.json* 構成ではなく、バインドの .NET 属性を使用するようにランタイムに指示する `configurationSource` プロパティが含まれます。 次に例を示します。

```json
{
  "generatedBy": "Microsoft.NET.Sdk.Functions-1.0.0.0",
  "configurationSource": "attributes",
  "bindings": [
    {
      "type": "queueTrigger",
      "queueName": "%input-queue-name%",
      "name": "myQueueItem"
    }
  ],
  "disabled": false,
  "scriptFile": "..\\bin\\FunctionApp1.dll",
  "entryPoint": "FunctionApp1.QueueTrigger.Run"
}
```

## <a name="microsoftnetsdkfunctions"></a>Microsoft.NET.Sdk.Functions

*function.json* ファイルの生成は、NuGet パッケージ ([Microsoft\.NET\.Sdk\.Functions](https://www.nuget.org/packages/Microsoft.NET.Sdk.Functions)) によって実行されます。 

Functions ランタイムのバージョン 1.x と 2.x では同じパッケージが使用されます。 1\.x プロジェクトと 2.x プロジェクトの違いはターゲット フレームワークです。 以下は *.csproj* ファイルの関連する部分で、同じ `Sdk` パッケージで異なるターゲット フレームワークを示しています。

# <a name="v2x"></a>[v2.x+](#tab/v2)

```xml
<PropertyGroup>
  <TargetFramework>netcoreapp2.1</TargetFramework>
  <AzureFunctionsVersion>v2</AzureFunctionsVersion>
</PropertyGroup>
<ItemGroup>
  <PackageReference Include="Microsoft.NET.Sdk.Functions" Version="1.0.8" />
</ItemGroup>
```

# <a name="v1x"></a>[v1.x](#tab/v1)

```xml
<PropertyGroup>
  <TargetFramework>net461</TargetFramework>
</PropertyGroup>
<ItemGroup>
  <PackageReference Include="Microsoft.NET.Sdk.Functions" Version="1.0.8" />
</ItemGroup>
```
---


`Sdk` パッケージ間の依存関係はトリガーとバインドです。 1\.x のトリガーとバインドの対象は .NET Framework であるため、1.x プロジェクトは 1.x のトリガーとバインドを参照します。一方、2.x のトリガーとバインドの対象は .NET Core です。

`Sdk` パッケージも、[Newtonsoft.Json](https://www.nuget.org/packages/Newtonsoft.Json) に依存しており、間接的に [WindowsAzure.Storage](https://www.nuget.org/packages/WindowsAzure.Storage) に依存します。 これらの依存関係により、ユーザーのプロジェクトでは、必ずそのプロジェクト用の Functions ランタイム バージョンで動作するパッケージ バージョンが使用されます。 たとえば、`Newtonsoft.Json` のバージョンが .NET Framework 4.6.1 用のバージョン 11 だとします。ところが、.NET Framework 4.6.1 を対象とする Functions ランタイムは `Newtonsoft.Json` 9.0.1 としか互換性がありません。 この場合は、そのプロジェクトの関数コードも `Newtonsoft.Json` 9.0.1 を使用する必要があります。

`Microsoft.NET.Sdk.Functions` のソース コードは、GitHub リポジトリ ([azure\-functions\-vs\-build\-sdk](https://github.com/Azure/azure-functions-vs-build-sdk)) で利用できます。

## <a name="runtime-version"></a>ランタイム バージョン

Visual Studio では、[Azure Functions Core Tools](functions-run-local.md#install-the-azure-functions-core-tools) を使用して、Functions プロジェクトを実行します。 Core Tools は、Functions ランタイム用のコマンド ライン インターフェイスです。

npm を使用して Core Tools をインストールする場合、これは Visual Studio で使用される Core Tools バージョンには影響しません。 Functions ランタイム バージョン 1.x の場合、Visual Studio は Core Tools のバージョンを *%USERPROFILE%\AppData\Local\Azure.Functions.Cli* に格納し、そこに格納されている中で最も新しいバージョンを使用します。 Functions 2.x の場合、Core Tools は **Azure Functions と Web ジョブ ツール** の拡張機能に含まれます。 1\.x と 2.x の両方について、使用されているバージョンは、Functions プロジェクトを実行するときに、コンソール出力で確認できます。

```terminal
[3/1/2018 9:59:53 AM] Starting Host (HostId=contoso2-1518597420, Version=2.0.11353.0, ProcessId=22020, Debug=False, Attempt=0, FunctionsExtensionVersion=)
```

## <a name="readytorun"></a>ReadyToRun

関数アプリは [ReadyToRun バイナリ](/dotnet/core/whats-new/dotnet-core-3-0#readytorun-images)としてコンパイルできます。 ReadyToRun は、事前コンパイル形式であり、起動時のパフォーマンスを向上させることができます。これは、[従量課金プラン](consumption-plan.md)で実行される場合に[コールドスタート](event-driven-scaling.md#cold-start)の影響を軽減するのに役立ちます。

ReadyToRun は .NET 3.0 で使用でき、[Azure Functions ランタイムのバージョン 3.0](functions-versions.md) が必要です。

プロジェクトを ReadyToRun としてコンパイルするには、`<PublishReadyToRun>` および `<RuntimeIdentifier>` 要素を追加して、プロジェクト ファイルを更新します。 Windows 32 ビットの関数アプリに公開するための構成を以下に示します。

```xml
<PropertyGroup>
  <TargetFramework>netcoreapp3.1</TargetFramework>
  <AzureFunctionsVersion>v3</AzureFunctionsVersion>
  <PublishReadyToRun>true</PublishReadyToRun>
  <RuntimeIdentifier>win-x86</RuntimeIdentifier>
</PropertyGroup>
```

> [!IMPORTANT]
> ReadyToRun では現在、クロスコンパイルはサポートされていません。 デプロイ ターゲットと同じプラットフォームでアプリをビルドする必要があります。 また、関数アプリで構成されている "ビット" にご注意ください。 たとえば、Azure の関数アプリが Windows 64 ビットの場合、[ランタイム識別子](/dotnet/core/rid-catalog)として `win-x64` を使用して Windows でアプリをコンパイルする必要があります。

コマンド ラインから ReadyToRun を使用してアプリをビルドすることもできます。 詳細については、「[`dotnet publish`](/dotnet/core/tools/dotnet-publish)」の `-p:PublishReadyToRun=true` オプションの説明を参照してください。

## <a name="supported-types-for-bindings"></a>バインドでサポートされる型

各バインドには独自にサポートされる型があります。たとえば、BLOB トリガー属性は文字列パラメーター、POCO パラメーター、`CloudBlockBlob` パラメーター、またはサポートされるその他の複数の型のいずれかに適用できます。 [BLOB バインディングのバインド リファレンス](functions-bindings-storage-blob-trigger.md#usage)に関する記事に、サポートされるすべてのパラメーター型の一覧が示されています。 詳細については、[トリガーとバインド](functions-triggers-bindings.md)に関する記事と、[各バインドの種類に対応するバインド リファレンス ドキュメント](functions-triggers-bindings.md#next-steps)をご覧ください。

[!INCLUDE [HTTP client best practices](../../includes/functions-http-client-best-practices.md)]

## <a name="binding-to-method-return-value"></a>メソッドの戻り値へのバインド

出力バインドのメソッドの戻り値を使用するには、属性をメソッドの戻り値に適用します。 例については、[トリガーとバインディング](./functions-bindings-return-value.md)に関するページを参照してください。 

正常な関数の実行によって、常に戻り値が出力バインドに渡される場合のみ、戻り値を使用してください。 それ以外の場合は、次のセクションに示すように `ICollector` または `IAsyncCollector` を使用してください。

## <a name="writing-multiple-output-values"></a>複数の出力値の書き込み

1 つの出力バインドに複数の値を書き込むため、または正常な関数の呼び出しによって出力バインドに渡される値がない場合、[`ICollector`](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs/ICollector.cs) または [`IAsyncCollector`](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs/IAsyncCollector.cs) 型を使用してください。 これらの型は、メソッド完了時に出力バインドに書き込まれる、書き込み専用接続です。

この例では、`ICollector` を使用して複数のキュー メッセージを同じキューに書き込みます。

```csharp
public static class ICollectorExample
{
    [FunctionName("CopyQueueMessageICollector")]
    public static void Run(
        [QueueTrigger("myqueue-items-source-3")] string myQueueItem,
        [Queue("myqueue-items-destination")] ICollector<string> myDestinationQueue,
        ILogger log)
    {
        log.LogInformation($"C# function processed: {myQueueItem}");
        myDestinationQueue.Add($"Copy 1: {myQueueItem}");
        myDestinationQueue.Add($"Copy 2: {myQueueItem}");
    }
}
```

## <a name="async"></a>非同期

関数を[非同期](/dotnet/csharp/programming-guide/concepts/async/)にするには、`async` キーワードを使用して `Task` オブジェクトを返します。

```csharp
public static class AsyncExample
{
    [FunctionName("BlobCopy")]
    public static async Task RunAsync(
        [BlobTrigger("sample-images/{blobName}")] Stream blobInput,
        [Blob("sample-images-copies/{blobName}", FileAccess.Write)] Stream blobOutput,
        CancellationToken token,
        ILogger log)
    {
        log.LogInformation($"BlobCopy function processed.");
        await blobInput.CopyToAsync(blobOutput, 4096, token);
    }
}
```

非同期関数では `out` パラメーターを使用できません。 出力バインドには、代わりに[関数の戻り値](#binding-to-method-return-value)または[コレクター オブジェクト](#writing-multiple-output-values)を使用します。

## <a name="cancellation-tokens"></a>キャンセル トークン

関数は [CancellationToken](/dotnet/api/system.threading.cancellationtoken) パラメーターを受け付けることができます。これにより、オペレーティング システムは、その関数をいつ終了しようとしているかをコードに通知できます。 この通知を使用すれば、関数が予期せず終了してデータが不整合な状態になることを防止できます。

次の例は、関数の終了が迫っているかどうかを確認する方法を示しています。

```csharp
public static class CancellationTokenExample
{
    public static void Run(
        [QueueTrigger("inputqueue")] string inputText,
        TextWriter logger,
        CancellationToken token)
    {
        for (int i = 0; i < 100; i++)
        {
            if (token.IsCancellationRequested)
            {
                logger.WriteLine("Function was cancelled at iteration {0}", i);
                break;
            }
            Thread.Sleep(5000);
            logger.WriteLine("Normal processing for queue message={0}", inputText);
        }
    }
}
```

## <a name="logging"></a>ログ記録

関数のコードで、Application Insights でトレースとして表示されるログに出力を書き込むことができます。 ログ書き込みには、[ILogger](/dotnet/api/microsoft.extensions.logging.ilogger) という種類のパラメーターを含める方法をお勧めします。これは通常 `log` という名前です。 Functions バージョン 1.x のランタイムでは `TraceWriter` が使用されていました。ここでは Application Insights への書き込みは行われますが、構造化ログはサポートされていません。 このデータは Application Insights によってキャプチャされないため、ログの書き込みに `Console.Write` を使用しないでください。 

### <a name="ilogger"></a>ILogger

関数定義に、[構造化ログ](https://softwareengineering.stackexchange.com/questions/312197/benefits-of-structured-logging-vs-basic-logging)をサポートする [ILogger](/dotnet/api/microsoft.extensions.logging.ilogger) パラメーターを含めます。

`ILogger` オブジェクトで、`Log<level>` [拡張メソッド (ILogger 上)](/dotnet/api/microsoft.extensions.logging.loggerextensions#methods) を呼び出して、ログを作成します。 次のコードでは、カテゴリが `Function.<YOUR_FUNCTION_NAME>.User.` の `Information` ログが書き込まれます。

```cs
public static async Task<HttpResponseMessage> Run(HttpRequestMessage req, ILogger logger)
{
    logger.LogInformation("Request for item with key={itemKey}.", id);
```

関数がどのように `ILogger` を実装しているかについて詳しくは、「[利用統計情報の収集](functions-monitoring.md#collecting-telemetry-data)」を参照してください。 `Function` で始まるカテゴリは、`ILogger` インスタンスを使用することを前提としています。 代わりに `ILogger<T>` を使用する場合、カテゴリ名は代わりに `T` に基づいている場合があります。  

### <a name="structured-logging"></a>構造化ログ

ログ メッセージで使用するパラメーターは、プレースホルダーの名前ではなく順序によって決定されます。 次のコードがあるとします。

```csharp
string partitionKey = "partitionKey";
string rowKey = "rowKey";
logger.LogInformation("partitionKey={partitionKey}, rowKey={rowKey}", partitionKey, rowKey);
```

同じメッセージ文字列を維持しながらパラメーターの順序を逆にすると、結果のメッセージ テキストではそれらの値が誤った場所に配置されます。

プレースホルダーはこのように処理されるため、構造化ログを実行できます。 Application Insights では、パラメーターの名前と値のペア、およびメッセージ文字列を保存します。 そのため、メッセージ引数はクエリ可能なフィールドになります。

前の例のようなロガー メソッド呼び出しでは、フィールド `customDimensions.prop__rowKey` をクエリできます。 `prop__` プレフィックスを追加して、ランタイムが追加したフィールドと、ご使用の関数コードが追加したフィールドとの間に競合が起こらないようにします。

フィールド `customDimensions.prop__{OriginalFormat}` を参照することで、元のメッセージ文字列をクエリすることもできます。  

`customDimensions` データの JSON 表現の例を次に示します。

```json
{
  "customDimensions": {
    "prop__{OriginalFormat}":"C# Queue trigger function processed: {message}",
    "Category":"Function",
    "LogLevel":"Information",
    "prop__message":"c9519cbf-b1e6-4b9b-bf24-cb7d10b1bb89"
  }
}
```

### <a name="log-custom-telemetry"></a><a name="log-custom-telemetry-in-c-functions"></a>カスタム テレメトリをログに記録する

ご自身の関数からカスタム テレメトリ データを Application Insights に送信するときに使用できる、Application Insights SDK の Functions 固有のバージョンがあります。[Microsoft.Azure.WebJobs.Logging.ApplicationInsights](https://www.nuget.org/packages/Microsoft.Azure.WebJobs.Logging.ApplicationInsights). コマンド プロンプトから次のコマンドを使用して、このパッケージをインストールします。

# <a name="command"></a>[コマンド](#tab/cmd)

```cmd
dotnet add package Microsoft.Azure.WebJobs.Logging.ApplicationInsights --version <VERSION>
```

# <a name="powershell"></a>[PowerShell](#tab/powershell)

```powershell
Install-Package Microsoft.Azure.WebJobs.Logging.ApplicationInsights -Version <VERSION>
```

---

このコマンドでは、`<VERSION>` を、このパッケージのバージョンに置き換えます。このバージョンでは、インストールされている [Microsoft.Azure.WebJobs](https://www.nuget.org/packages/Microsoft.Azure.WebJobs/) バージョンがサポートされます。 

次の C# の例では、[カスタム テレメトリ API](../azure-monitor/app/api-custom-events-metrics.md) を使用します。 この例は .NET クラス ライブラリ用ですが、Application Insights のコードは C# スクリプト用と同じです。

# <a name="v2x"></a>[v2.x+](#tab/v2)

バージョン 2.x 以降のランタイム バージョンでは、テレメトリを現在の操作と自動的に関連付けるために、Application Insights 内のより新しい機能が使用されます。 `Id`、`ParentId`、または `Name` フィールドを手動で設定する操作は必要ありません。

```cs
using System;
using System.Threading.Tasks;
using Microsoft.AspNetCore.Mvc;
using Microsoft.Azure.WebJobs;
using Microsoft.Azure.WebJobs.Extensions.Http;
using Microsoft.AspNetCore.Http;
using Microsoft.Extensions.Logging;

using Microsoft.ApplicationInsights;
using Microsoft.ApplicationInsights.DataContracts;
using Microsoft.ApplicationInsights.Extensibility;
using System.Linq;

namespace functionapp0915
{
    public class HttpTrigger2
    {
        private readonly TelemetryClient telemetryClient;

        /// Using dependency injection will guarantee that you use the same configuration for telemetry collected automatically and manually.
        public HttpTrigger2(TelemetryConfiguration telemetryConfiguration)
        {
            this.telemetryClient = new TelemetryClient(telemetryConfiguration);
        }

        [FunctionName("HttpTrigger2")]
        public Task<IActionResult> Run(
            [HttpTrigger(AuthorizationLevel.Anonymous, "get", Route = null)]
            HttpRequest req, ExecutionContext context, ILogger log)
        {
            log.LogInformation("C# HTTP trigger function processed a request.");
            DateTime start = DateTime.UtcNow;

            // Parse query parameter
            string name = req.Query
                .FirstOrDefault(q => string.Compare(q.Key, "name", true) == 0)
                .Value;

            // Write an event to the customEvents table.
            var evt = new EventTelemetry("Function called");
            evt.Context.User.Id = name;
            this.telemetryClient.TrackEvent(evt);

            // Generate a custom metric, in this case let's use ContentLength.
            this.telemetryClient.GetMetric("contentLength").TrackValue(req.ContentLength);

            // Log a custom dependency in the dependencies table.
            var dependency = new DependencyTelemetry
            {
                Name = "GET api/planets/1/",
                Target = "swapi.co",
                Data = "https://swapi.co/api/planets/1/",
                Timestamp = start,
                Duration = DateTime.UtcNow - start,
                Success = true
            };
            dependency.Context.User.Id = name;
            this.telemetryClient.TrackDependency(dependency);

            return Task.FromResult<IActionResult>(new OkResult());
        }
    }
}
```

この例では、カスタム メトリック データは、customMetrics テーブルに送信される前にホストによって集計されます。 詳細については、Application Insights で [GetMetric](../azure-monitor/app/api-custom-events-metrics.md#getmetric) のドキュメントを参照してください。 

ローカルで実行する場合は、Application Insights キーを使用して `APPINSIGHTS_INSTRUMENTATIONKEY` 設定を [local.settings.json](functions-run-local.md#local-settings-file) ファイルに追加する必要があります。


# <a name="v1x"></a>[v1.x](#tab/v1)

```cs
using System;
using System.Net;
using Microsoft.ApplicationInsights;
using Microsoft.ApplicationInsights.DataContracts;
using Microsoft.ApplicationInsights.Extensibility;
using Microsoft.Azure.WebJobs;
using System.Net.Http;
using System.Threading.Tasks;
using Microsoft.Azure.WebJobs.Extensions.Http;
using Microsoft.Extensions.Logging;
using System.Linq;

namespace functionapp0915
{
    public static class HttpTrigger2
    {
        private static string key = TelemetryConfiguration.Active.InstrumentationKey = 
            System.Environment.GetEnvironmentVariable(
                "APPINSIGHTS_INSTRUMENTATIONKEY", EnvironmentVariableTarget.Process);

        private static TelemetryClient telemetryClient = 
            new TelemetryClient() { InstrumentationKey = key };

        [FunctionName("HttpTrigger2")]
        public static async Task<HttpResponseMessage> Run(
            [HttpTrigger(AuthorizationLevel.Anonymous, "get", "post", Route = null)]
            HttpRequestMessage req, ExecutionContext context, ILogger log)
        {
            log.LogInformation("C# HTTP trigger function processed a request.");
            DateTime start = DateTime.UtcNow;

            // Parse query parameter
            string name = req.GetQueryNameValuePairs()
                .FirstOrDefault(q => string.Compare(q.Key, "name", true) == 0)
                .Value;

            // Get request body
            dynamic data = await req.Content.ReadAsAsync<object>();

            // Set name to query string or body data
            name = name ?? data?.name;
         
            // Track an Event
            var evt = new EventTelemetry("Function called");
            UpdateTelemetryContext(evt.Context, context, name);
            telemetryClient.TrackEvent(evt);
            
            // Track a Metric
            var metric = new MetricTelemetry("Test Metric", DateTime.Now.Millisecond);
            UpdateTelemetryContext(metric.Context, context, name);
            telemetryClient.TrackMetric(metric);
            
            // Track a Dependency
            var dependency = new DependencyTelemetry
            {
                Name = "GET api/planets/1/",
                Target = "swapi.co",
                Data = "https://swapi.co/api/planets/1/",
                Timestamp = start,
                Duration = DateTime.UtcNow - start,
                Success = true
            };
            UpdateTelemetryContext(dependency.Context, context, name);
            telemetryClient.TrackDependency(dependency);
        }
        
        // Correlate all telemetry with the current Function invocation
        private static void UpdateTelemetryContext(TelemetryContext context, ExecutionContext functionContext, string userName)
        {
            context.Operation.Id = functionContext.InvocationId.ToString();
            context.Operation.ParentId = functionContext.InvocationId.ToString();
            context.Operation.Name = functionContext.FunctionName;
            context.User.Id = userName;
        }
    }    
}
```
---

関数呼び出しの要求が重複するため、`TrackRequest` や `StartOperation<RequestTelemetry>` を呼び出さないでください。  Functions ランタイムでは、要求が自動的に追跡されます。

`telemetryClient.Context.Operation.Id` は設定しないでください。 このグローバル設定により、多くの関数が同時に実行されていると、正しくない相関関係が発生します。 代わりに、新しいテレメトリ インスタンス (`DependencyTelemetry`、`EventTelemetry`) を作成し、その `Context` プロパティを変更してください。 その後、テレメトリ インスタンスを、`TelemetryClient` (`TrackDependency()`、`TrackEvent()`、`TrackMetric()`) の対応する `Track` メソッドに渡します。 このメソッドにより、現在の関数呼び出しについて、テレメトリが必ず正しい相関関係の詳細を持つようになります。


## <a name="environment-variables"></a>環境変数

環境変数またはアプリ設定値を取得するには、次のコード例のように、 `System.Environment.GetEnvironmentVariable`を使用します。

```csharp
public static class EnvironmentVariablesExample
{
    [FunctionName("GetEnvironmentVariables")]
    public static void Run([TimerTrigger("0 */5 * * * *")]TimerInfo myTimer, ILogger log)
    {
        log.LogInformation($"C# Timer trigger function executed at: {DateTime.Now}");
        log.LogInformation(GetEnvironmentVariable("AzureWebJobsStorage"));
        log.LogInformation(GetEnvironmentVariable("WEBSITE_SITE_NAME"));
    }

    private static string GetEnvironmentVariable(string name)
    {
        return name + ": " +
            System.Environment.GetEnvironmentVariable(name, EnvironmentVariableTarget.Process);
    }
}
```

アプリ設定は、ローカルでの開発中と Azure での実行中の両方で、環境変数から読み取ることができます。 ローカルでの開発時、アプリ設定は *local.settings.json* ファイルの `Values` コレクションから取得します。 ローカルと Azure の両方の環境において、`GetEnvironmentVariable("<app setting name>")` は名前付きアプリ設定の値を取得します。 たとえば、ローカルでの実行中、*local.settings.json* ファイルに `{ "Values": { "WEBSITE_SITE_NAME": "My Site Name" } }` が含まれている場合は、"My Site Name" が返されます。

[System.Configuration.ConfigurationManager.AppSettings](/dotnet/api/system.configuration.configurationmanager.appsettings) プロパティは、アプリ設定値を取得するための代替 API ですが、次に示すように `GetEnvironmentVariable` を使用することをお勧めします。

## <a name="binding-at-runtime"></a>実行時のバインド

C# および他の .NET 言語では、属性の [*宣言型*](https://en.wikipedia.org/wiki/Declarative_programming)のバインドではなく [命令型](https://en.wikipedia.org/wiki/Imperative_programming)のバインド パターンを使用できます。 命令型のバインドは、設計時ではなくランタイム時にバインド パラメーターを計算する必要がある場合に便利です。 このパターンを使用すると、サポートされている入力バインドと出力バインドに関数コード内でバインドできます。

次のように命令型のバインドを定義します。

- 必要な命令型のバインドの関数署名に属性を含め **ないでください。**
- 入力パラメーター [`Binder binder`](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs.Host/Bindings/Runtime/Binder.cs) または [`IBinder binder`](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs/IBinder.cs) を渡します。
- 次の C# パターンを使用してデータ バインドを実行します。

  ```cs
  using (var output = await binder.BindAsync<T>(new BindingTypeAttribute(...)))
  {
      ...
  }
  ```

  `BindingTypeAttribute` はバインドを定義する .NET 属性、`T` はそのバインドの種類でサポートされている入力または出力の型です。 `T` を `out` パラメーター型 (`out JObject` など) にすることはできません。 たとえば、Mobile Apps テーブルの出力バインドは [6 種類の出力](https://github.com/Azure/azure-webjobs-sdk-extensions/blob/master/src/WebJobs.Extensions.MobileApps/MobileTableAttribute.cs#L17-L22)をサポートしますが、強制バインドに使用できるのは [ICollector\<T>](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs/ICollector.cs) または [IAsyncCollector\<T>](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs/IAsyncCollector.cs) のみです。

### <a name="single-attribute-example"></a>単一属性の例

次のコード例は、実行時に BLOB パスが定義された [Storage Blob の出力バインド](functions-bindings-storage-blob-output.md)を作成し、この BLOB に文字列を書き込みます。

```cs
public static class IBinderExample
{
    [FunctionName("CreateBlobUsingBinder")]
    public static void Run(
        [QueueTrigger("myqueue-items-source-4")] string myQueueItem,
        IBinder binder,
        ILogger log)
    {
        log.LogInformation($"CreateBlobUsingBinder function processed: {myQueueItem}");
        using (var writer = binder.Bind<TextWriter>(new BlobAttribute(
                    $"samples-output/{myQueueItem}", FileAccess.Write)))
        {
            writer.Write("Hello World!");
        };
    }
}
```

[BlobAttribute](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs.Extensions.Storage/Blobs/BlobAttribute.cs) は [Storage Blob](functions-bindings-storage-blob.md) の入力バインドまたは出力バインドを定義します。[TextWriter](/dotnet/api/system.io.textwriter) はサポートされている出力バインドの種類です。

### <a name="multiple-attributes-example"></a>複数属性の例

前の例では、関数アプリのメイン ストレージ アカウント接続文字列 (`AzureWebJobsStorage`) のアプリ設定を取得します。 ストレージ アカウントに使用するカスタム アプリ設定を指定するには、[StorageAccountAttribute](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs/StorageAccountAttribute.cs) を追加し、属性の配列を `BindAsync<T>()` に渡します。 `IBinder`ではなく、`Binder` パラメーターを使用します。  次に例を示します。

```cs
public static class IBinderExampleMultipleAttributes
{
    [FunctionName("CreateBlobInDifferentStorageAccount")]
    public async static Task RunAsync(
            [QueueTrigger("myqueue-items-source-binder2")] string myQueueItem,
            Binder binder,
            ILogger log)
    {
        log.LogInformation($"CreateBlobInDifferentStorageAccount function processed: {myQueueItem}");
        var attributes = new Attribute[]
        {
        new BlobAttribute($"samples-output/{myQueueItem}", FileAccess.Write),
        new StorageAccountAttribute("MyStorageAccount")
        };
        using (var writer = await binder.BindAsync<TextWriter>(attributes))
        {
            await writer.WriteAsync("Hello World!!");
        }
    }
}
```

## <a name="triggers-and-bindings"></a>トリガーとバインド 

[!INCLUDE [Supported triggers and bindings](../../includes/functions-bindings.md)]

## <a name="next-steps"></a>次のステップ

> [!div class="nextstepaction"]
> [トリガーとバインドの詳細を確認する](functions-triggers-bindings.md)

> [!div class="nextstepaction"]
> [Azure Functions のベスト プラクティスを確認する](functions-best-practices.md)
