---
title: Azure Functions での .NET 5.0 の .NET 分離プロセス ガイド
description: .NET 分離プロセスを使用して、Azure において C# 関数を .NET 5.0 でアウトプロセスで実行する方法について説明します。
ms.service: azure-functions
ms.topic: conceptual
ms.date: 03/01/2021
ms.custom: template-concept
ms.openlocfilehash: be11c32cf06b9873e10247d7ccc4a84133a6c688
ms.sourcegitcommit: 2c1b93301174fccea00798df08e08872f53f669c
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/22/2021
ms.locfileid: "104774934"
---
# <a name="guide-for-running-functions-on-net-50-in-azure"></a>Azure において関数を .NET 5.0 で実行するためのガイド

この記事では、C# を使用して、Azure Functions でアウトプロセスで実行する .NET 分離プロセス関数を開発する方法について説明します。 アウトプロセスで実行することで、関数コードを Azure Functions ランタイムから切り離すことができます。 これは、現在の .NET 5.0 リリースを対象とする関数を作成して実行する方法にもなります。 

| 作業の開始 | 概念| サンプル |
|--|--|--| 
| <ul><li>[Visual Studio Code の使用](dotnet-isolated-process-developer-howtos.md?pivots=development-environment-vscode)</li><li>[コマンド ライン ツールの使用](dotnet-isolated-process-developer-howtos.md?pivots=development-environment-cli)</li><li>[Visual Studio の使用](dotnet-isolated-process-developer-howtos.md?pivots=development-environment-vs)</li></ul> | <ul><li>[ホスティング オプション](functions-scale.md)</li><li>[Monitoring](functions-monitoring.md)</li> | <ul><li>[リファレンスのサンプル](https://github.com/Azure/azure-functions-dotnet-worker/tree/main/samples)</li></ul> |

.NET 5.0 をサポートしたり関数をアウトプロセスで実行したりする必要がない場合は、代わりに [C# クラス ライブラリ関数を開発](functions-dotnet-class-library.md)することをお勧めします。

## <a name="why-net-isolated-process"></a>なぜ .NET 分離プロセスなのか?

これまで Azure Functions で唯一サポートされていた .NET 関数の緊密統合モードは、ホストと同じプロセスで[クラス ライブラリとして](functions-dotnet-class-library.md)実行されました。 このモードにより、ホスト プロセスと関数の間の統合が深くなります。 たとえば、.NET クラス ライブラリ関数は、バインドの API と型を共有できます。 しかし、この統合では、ホスト プロセスと .NET 関数の間の結合がより緊密になっている必要もあります。 たとえば、インプロセスで実行中の .NET 関数が Functions ランタイムと同じ .NET のバージョンで実行される必要があります。 これらの制約がなく実行できるように、分離プロセスでの実行を選べるようになりました。 このプロセス分離を使用すれば、Functions ランタイムでネイティブにサポートされていない、現在の .NET リリース (.NET 5.0 など) を使用する関数を開発することもできます。

これらの関数は別個のプロセスで実行されるため、.NET 分離関数アプリと .NET クラス ライブラリ関数アプリの間には[特徴と機能の違い](#differences-with-net-class-library-functions)がいくつかあります。

### <a name="benefits-of-running-out-of-process"></a>アウトプロセスで実行する場合の利点

アウトプロセスで実行すると、.NET 関数には次のような利点があります。 

+ 競合が少ない: 関数は別個のプロセスで実行されるため、アプリで使用されるアセンブリは、ホスト プロセスにより使用される同じアセンブリの異なるバージョンと競合しません。  
+ プロセスの完全制御: アプリの起動を制御でき、使用する構成や起動するミドルウェアを制御できます。
+ 依存関係の挿入: プロセスを完全制御できるため、現在の .NET 動作を使用して、依存関係の挿入や関数アプリへのミドルウェアの組み込みを行えます。 

## <a name="supported-versions"></a>サポートされているバージョン

アウトプロセスでの実行が現在サポートされている .NET の唯一のバージョンは .NET 5.0 です。

## <a name="net-isolated-project"></a>.NET 分離プロジェクト

.NET 分離関数プロジェクトとは、基本的には .NET 5.0 を対象とする .NET コンソール アプリ プロジェクトです。 以下は、.NET 分離プロジェクトで必要となる基本的なファイルです。

+ [host.json](functions-host-json.md) ファイル
+ [local.settings.json](functions-run-local.md#local-settings-file) ファイル
+ プロジェクトと依存関係を定義する C# プロジェクト ファイル (.csproj)
+ アプリのエントリ ポイントである Program.cs ファイル。

## <a name="package-references"></a>パッケージ参照

アウトプロセスで実行する場合、.NET プロジェクトは、コア機能とバインド拡張機能の両方を実装している、一意のパッケージ セットを使用します。 

### <a name="core-packages"></a>コア パッケージ 

以下のパッケージは、.NET 関数を分離プロセスで実行するために必要です。

+ [Microsoft.Azure.Functions.Worker](https://www.nuget.org/packages/Microsoft.Azure.Functions.Worker/)
+ [Microsoft.Azure.Functions.Worker.Sdk](https://www.nuget.org/packages/Microsoft.Azure.Functions.Worker.Sdk/)

### <a name="extension-packages"></a>拡張機能パッケージ

.NET 分離プロセスで実行される関数はさまざまなバインドの種類を使用するため、バインド拡張機能パッケージの一意のセットが必要になります。 

これらの拡張機能パッケージは、[Microsoft.Azure.Functions.Worker.Extensions](https://www.nuget.org/packages?q=Microsoft.Azure.Functions.Worker.Extensions) にあります。

## <a name="start-up-and-configuration"></a>スタートアップと構成 

.NET 分離関数を使用する場合、通常は Program.cs にある関数アプリのスタートアップにアクセスできます。 各自のホスト インスタンスの作成と開始は、お客様が担当します。 そのため、自分のアプリの構成パイプラインに直接アクセスすることもできます。 アウトプロセスで実行すると、構成の追加、依存関係の挿入、および独自のミドルウェアの実行がはるかに簡単になります。 

次のコードは [HostBuilder] パイプラインの例を示します。

:::code language="csharp" source="~/azure-functions-dotnet-worker/samples/FunctionApp/Program.cs" id="docsnippet_startup":::

[HostBuilder] は、完全に初期化された [IHost] インスタンス (これを非同期で実行して関数アプリを起動します) をビルドして返すために使用します。 

:::code language="csharp" source="~/azure-functions-dotnet-worker/samples/FunctionApp/Program.cs" id="docsnippet_host_run":::

### <a name="configuration"></a>構成

[ConfigureFunctionsWorkerDefaults] メソッドは、関数アプリがアウトプロセスの実行に必要な設定を追加するために使用されます。これには、次の機能が含まれます。

+ コンバーターの既定のセット。
+ プロパティ名の大文字と小文字の区別を無視するための既定の [JsonSerializerOptions] への設定。
+ Azure Functions ログとの統合。
+ 出力バインディング ミドルウェアと機能。
+ 関数の実行ミドルウェア。
+ 既定の gRPC サポート。 

:::code language="csharp" source="~/azure-functions-dotnet-worker/samples/FunctionApp/Program.cs" id="docsnippet_configure_defaults" :::   

ホスト ビルダー パイプラインにアクセスできるということは、初期化中にあらゆるアプリ固有の構成を設定できることも意味します。 [ConfigureAppConfiguration] メソッドを [HostBuilder] で 1 回以上呼び出して、関数アプリに必要な構成を追加できます。 アプリ構成について詳しくは、「[ASP.NET Core の構成](/aspnet/core/fundamentals/configuration/?view=aspnetcore-5.0&preserve-view=true)」を参照してください。 

これらの構成は、別のプロセスで実行している関数アプリに適用されます。 Functions ホストまたはトリガー、およびバインド構成に変更を加えるには、この場合も [host.json ファイル](functions-host-json.md)を使用する必要があります。   

### <a name="dependency-injection"></a>依存関係の挿入

依存関係の挿入は、.NET クラス ライブラリと比べると簡単です。 サービスを登録するためのスタートアップ クラスを作成するのではなく、ホスト ビルダーで [ConfigureServices] を呼び出して、[IServiceCollection] で拡張メソッドを使用して特定のサービスを挿入するだけで済みます。 

次の例では、シングルトン サービスの依存関係を挿入します。  
 
:::code language="csharp" source="~/azure-functions-dotnet-worker/samples/FunctionApp/Program.cs" id="docsnippet_dependency_injection" :::

詳細については、「[ASP.NET Core での依存関係の挿入](/aspnet/core/fundamentals/dependency-injection?view=aspnetcore-5.0&preserve-view=true)」を参照してください。

### <a name="middleware"></a>ミドルウェア

.NET 分離では、ASP.NET にあるものと同様のモデルを使用して、ミドルウェア登録もサポートしています。 このモデルを使用すると、呼び出しパイプラインにロジックを挿入したり、関数の実行前と実行後にロジックを挿入したりすることができます。

[ConfigureFunctionsWorkerDefaults] 拡張メソッドには、次の例に示すように、独自のミドルウェアを登録できるオーバーロードがあります。  

:::code language="csharp" source="~/azure-functions-dotnet-worker/samples/CustomMiddleware/Program.cs" id="docsnippet_middleware_register" :::

関数アプリでカスタム ミドルウェアを使用する詳細な例については、[カスタム ミドルウェアのリファレンス サンプル](https://github.com/Azure/azure-functions-dotnet-worker/blob/main/samples/CustomMiddleware)を参照してください。

## <a name="execution-context"></a>実行コンテキスト

.NET 分離は [FunctionContext] オブジェクトを関数メソッドに渡します。 このオブジェクトを使用すれば、ログに書き込む [ILogger] インスタンスを取得できます。これは、[GetLogger] メソッドを呼び出して `categoryName` 文字列を指定することで実行します。 詳細については、「[ログ](#logging)」を参照してください。 

## <a name="bindings"></a>バインド 

バインドは、メソッド、パラメーター、および戻り値の型の属性を使用して定義します。 関数メソッドとは、次の例に示すように、`Function` 属性とトリガー属性が入力パラメーターに適用されたメソッドです。

:::code language="csharp" source="~/azure-functions-dotnet-worker/samples/Extensions/Queue/QueueFunction.cs" id="docsnippet_queue_trigger" :::

トリガー属性は、トリガーの種類を指定し、メソッド パラメーターに入力データをバインドします。 前の例の関数はキュー メッセージによってトリガーされ、そのキュー メッセージは `myQueueItem` パラメーターでメソッドに渡されます。

`Function` 属性は、関数のエントリ ポイントとしてメソッドをマークします。 名前はプロジェクト内で一意であり、文字で始まり、英数字、`_`、`-` のみが含まれ、127 文字以下にする必要があります。 プロジェクト テンプレートでは、多くの場合、`Run` という名前のメソッドが作成されますが、有効な C# メソッド名であればメソッド名として使用できます。

.NET 分離プロジェクトは別個のワーカー プロセスで実行されるため、バインドで `ICollector<T>`、`IAsyncCollector<T>`、`CloudBlockBlob` などの豊富なバインド クラスを利用できません。 また、[DocumentClient] や [BrokeredMessage] など、基になるサービス SDK から継承された型に対する直接のサポートもありません。 代わりに、バインドは文字列、配列、および単純な従来のクラス オブジェクト (POCO) のようなシリアル化可能型に依存します。 

HTTP トリガーの場合、要求と応答のデータにアクセスするには、[HttpRequestData] と [HttpResponseData] を使用する必要があります。 これは、アウトプロセスでの実行時には、元の HTTP 要求および応答オブジェクトにアクセスできないためです。

アウトプロセスの実行時にトリガーとバインドを使用するためのリファレンス サンプルの完全なセットについては、[バインド拡張機能のリファレンス サンプル](https://github.com/Azure/azure-functions-dotnet-worker/blob/main/samples/Extensions)を参照してください。 

### <a name="input-bindings"></a>入力バインディング

関数には、関数にデータを渡すことができる入力バインディングを 0 個以上含めることができます。 トリガーと同様、入力バインディングは、入力パラメーターにバインディング属性を適用することによって定義します。 関数を実行すると、ランタイムはバインディングで指定されたデータを取得しようとします。 要求されるデータは、多くの場合、バインディング パラメーターを使用してトリガーから提供される情報に依存します。  

### <a name="output-bindings"></a>出力バインディング

出力バインディングに書き込むには、関数メソッドに、バインドされたサービスへの書き込み方法を定義した出力バインディング属性を適用する必要があります。 メソッドによって返される値は、出力バインディングに書き込まれます。 たとえば、次の例では、出力バインディングを使用して、`functiontesting2` という名前のメッセージ キューに文字列値を書き込みます。

:::code language="csharp" source="~/azure-functions-dotnet-worker/samples/Extensions/Queue/QueueFunction.cs" id="docsnippet_queue_output_binding" :::

### <a name="multiple-output-bindings"></a>複数の出力バインディング

出力バインディングに書き込まれるデータは、常に関数の戻り値です。 複数の出力バインディングに書き込む必要がある場合は、カスタムの戻り値の型を作成する必要があります。 この戻り値の型では、出力バインディング属性はクラスの 1 つ以上のプロパティに適用されていなければなりません。 次の例では、HTTP 応答とキュー出力バインディングの両方に書き込みます。

:::code language="csharp" source="~/azure-functions-dotnet-worker/samples/Extensions/MultiOutput/MultiOutput.cs" id="docsnippet_multiple_outputs":::

### <a name="http-trigger"></a>HTTP トリガー

HTTP トリガーは、受信した HTTP 要求メッセージを、関数に渡される [HttpRequestData] オブジェクトに変換します。 このオブジェクトは、`Headers`、`Cookies`、`Identities`、`URL`、およびメッセージ `Body` (オプション) を含む、要求からのデータを提供します。 このオブジェクトは、HTTP 要求オブジェクトを表現したものであり、要求そのものではありません。 

同様に、関数が返す [HttpReponseData] オブジェクトも、メッセージ `StatusCode`、`Headers`、およびメッセージ `Body` (オプション) を含む、HTTP 応答を作成するために使用するデータを提供します。  

次のコードは、HTTP トリガーです 

:::code language="csharp" source="~/azure-functions-dotnet-worker/samples/Extensions/Http/HttpFunction.cs" id="docsnippet_http_trigger" :::

## <a name="logging"></a>ログ記録

.NET 分離では、[ILogger] インスタンスを使用してログに書き込むことができます。このインスタンスは、関数に渡される [FunctionContext] オブジェクトから取得します。 [GetLogger] メソッドを呼び出します。その際に、ログが書き込まれるカテゴリの名前を表す文字列値を渡します。 カテゴリは通常、ログの書き込み元となる特定の関数の名前です。 カテゴリについて詳しくは、[監視に関する記事](functions-monitoring.md#log-levels-and-categories)をご覧ください。 

次の例は、[ILogger] を取得してログを関数内で書き込む方法を示しています。

:::code language="csharp" source="~/azure-functions-dotnet-worker/samples/Extensions/Http/HttpFunction.cs" id="docsnippet_logging" ::: 

さまざまな [ILogger] のメソッドを使用して、`LogWarning` や `LogError` などの各種のログ レベルを記述します。 ログ レベルについて詳しくは、[監視に関する記事](functions-monitoring.md#log-levels-and-categories)をご覧ください。

[ILogger] は、[依存関係の挿入](#dependency-injection)を使用しているときにも提供されます。

## <a name="differences-with-net-class-library-functions"></a>.NET クラス ライブラリ関数との相違点

このセクションでは、インプロセスで実行される .NET クラス ライブラリ関数と比較した場合の、.NET 5.0 でアウトプロセスで実行される機能と動作の違いの現在の状態について説明します。

| 機能/動作 |  インプロセス (.NET Core 3.1) | アウトプロセス (.NET 5.0) |
| ---- | ---- | ---- |
| .NET のバージョン | LTS (.NET Core 3.1) | 現行 (.NET 5.0) |
| コア パッケージ | [Microsoft.NET.Sdk.Functions](https://www.nuget.org/packages/Microsoft.NET.Sdk.Functions/) | [Microsoft.Azure.Functions.Worker](https://www.nuget.org/packages/Microsoft.Azure.Functions.Worker/)<br/>[Microsoft.Azure.Functions.Worker.Sdk](https://www.nuget.org/packages/Microsoft.Azure.Functions.Worker.Sdk) | 
| バインディング拡張機能パッケージ | [Microsoft.Azure.WebJobs.Extensions.*](https://www.nuget.org/packages?q=Microsoft.Azure.WebJobs.Extensions)  | [Microsoft.Azure.Functions.Worker.Extensions.*](https://www.nuget.org/packages?q=Microsoft.Azure.Functions.Worker.Extensions) の下 | 
| ログ記録 | [ILogger] が関数に渡される | [ILogger] は [FunctionContext] から取得される |
| キャンセル トークン | [サポートされています](functions-dotnet-class-library.md#cancellation-tokens) | サポートされていません |
| 出力バインディング | out パラメーター | 戻り値 |
| 出力バインディングの種類 |  `IAsyncCollector`、[DocumentClient]、[BrokeredMessage]、および他のクライアント固有の型 | 単純型、JSON シリアル化可能型、および配列。 |
| 複数の出力バインディング | サポートされています | [サポート状況](#multiple-output-bindings) |
| HTTP トリガー | [HttpRequest]/[ObjectResult] | [HttpRequestData]/[HttpResponseData] |
| Durable Functions | [サポートされています](durable/durable-functions-overview.md) | サポートされていません | 
| 命令型バインディング | [サポートされています](functions-dotnet-class-library.md#binding-at-runtime) | サポートされていません |
| function.json アーティファクト | 自動生成 | 生成されない |
| 構成 | [host.json](functions-host-json.md) | [host.json](functions-host-json.md) とカスタム初期化 |
| 依存関係の挿入 | [サポートされています](functions-dotnet-dependency-injection.md)  | [サポートされています](#dependency-injection) |
| ミドルウェア | サポートされていません | サポートされています |
| コールド スタート時間 | 通常 | 長くなります。これは、Just-In-Time スタートアップのためです。 遅延の可能性を減らすために、Windows ではなく Linux で実行してください。 |
| ReadyToRun | [サポート状況](functions-dotnet-class-library.md#readytorun) | _TBD_ |

## <a name="known-issues"></a>既知の問題

.NET 分離プロセス関数を実行するときの既知の問題の回避策については、[こちらの既知の問題に関するページ](https://aka.ms/AAbh18e)を参照してください。 問題を報告するには、[こちらの GitHub リポジトリで問題を作成](https://github.com/Azure/azure-functions-dotnet-worker/issues/new/choose)してください。  

## <a name="next-steps"></a>次のステップ

+ [トリガーとバインドの詳細を確認する](functions-triggers-bindings.md)
+ [Azure Functions のベスト プラクティスを確認する](functions-best-practices.md)


[HostBuilder]: /dotnet/api/microsoft.extensions.hosting.hostbuilder?view=dotnet-plat-ext-5.0&preserve-view=true
[IHost]: /dotnet/api/microsoft.extensions.hosting.ihost?view=dotnet-plat-ext-5.0&preserve-view=true
[ConfigureFunctionsWorkerDefaults]: /dotnet/api/microsoft.extensions.hosting.workerhostbuilderextensions.configurefunctionsworkerdefaults?view=azure-dotnet&preserve-view=true#Microsoft_Extensions_Hosting_WorkerHostBuilderExtensions_ConfigureFunctionsWorkerDefaults_Microsoft_Extensions_Hosting_IHostBuilder_
[ConfigureAppConfiguration]: /dotnet/api/microsoft.extensions.hosting.hostbuilder.configureappconfiguration?view=dotnet-plat-ext-5.0&preserve-view=true
[IServiceCollection]: /dotnet/api/microsoft.extensions.dependencyinjection.iservicecollection?view=dotnet-plat-ext-5.0&preserve-view=true
[ConfigureServices]: /dotnet/api/microsoft.extensions.hosting.hostbuilder.configureservices?view=dotnet-plat-ext-5.0&preserve-view=true
[FunctionContext]: /dotnet/api/microsoft.azure.functions.worker.functioncontext?view=azure-dotnet&preserve-view=true
[ILogger]: /dotnet/api/microsoft.extensions.logging.ilogger?view=dotnet-plat-ext-5.0&preserve-view=true
[GetLogger]: /dotnet/api/microsoft.azure.functions.worker.functioncontextloggerextensions.getlogger?view=azure-dotnet&preserve-view=true
[DocumentClient]: /dotnet/api/microsoft.azure.documents.client.documentclient
[BrokeredMessage]: /dotnet/api/microsoft.servicebus.messaging.brokeredmessage
[HttpRequestData]: /dotnet/api/microsoft.azure.functions.worker.http.httprequestdata?view=azure-dotnet&preserve-view=true
[HttpResponseData]: /dotnet/api/microsoft.azure.functions.worker.http.httpresponsedata?view=azure-dotnet&preserve-view=true
[HttpRequest]: /dotnet/api/microsoft.aspnetcore.http.httprequest?view=aspnetcore-5.0&preserve-view=true
[ObjectResult]: /dotnet/api/microsoft.aspnetcore.mvc.objectresult?view=aspnetcore-5.0&preserve-view=true
[JsonSerializerOptions]: /api/system.text.json.jsonserializeroptions?view=net-5.0&preserve-view=true
