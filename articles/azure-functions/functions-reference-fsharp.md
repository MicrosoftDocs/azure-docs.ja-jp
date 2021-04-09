---
title: Azure Functions F# 開発者向けリファレンス
description: F# スクリプトを使用して Azure Functions を開発する方法について説明します。
author: sylvanc
ms.assetid: e60226e5-2630-41d7-9e5b-9f9e5acc8e50
ms.topic: conceptual
ms.date: 10/09/2018
ms.author: syclebsc
ms.openlocfilehash: f9b7b92fd21e12f1d86c5d5878e48c6ec6b0e748
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "87088021"
---
# <a name="azure-functions-f-developer-reference"></a>Azure Functions F# 開発者向けリファレンス

Azure Functions 用 F# は、小規模なコード ("関数") をクラウドで手軽に実行できるソリューションです。 データは関数の引数を通じて F# 関数に渡されます。 引数名は `function.json`で指定され、関数のロガーやキャンセル トークンなどにアクセスするための定義済みの名前があります。 

>[!IMPORTANT]
>F# スクリプト (.fsx) は、Azure Functions [バージョン 1.x](functions-versions.md#creating-1x-apps) の関数でのみサポートされています。 F# をバージョン 2.x 以降のランタイムで使用するには、プリコンパイル済み F# クラス ライブラリ プロジェクト (.fs) を使用する必要があります。 F# クラス ライブラリ プロジェクトを作成、管理、公開するには、[C# クラス ライブラリ プロジェクト](functions-dotnet-class-library.md)の場合と同じように Visual Studio を使用します。 Functions バージョンの詳細については、「[Azure Functions ランタイム バージョンの概要](functions-versions.md)」をご覧ください。

この記事では、「 [Azure Functions developer reference (Azure Functions 開発者向けリファレンス)](functions-reference.md)」を既に読んでいることを前提としています。

## <a name="how-fsx-works"></a>.fsx のしくみ
`.fsx` ファイルは、F# スクリプトです。 1 つのファイルに含まれている F# プロジェクトとして考えることができます。 ファイルには、プログラムのためのコード (この場合は、Azure 関数) と、依存関係を管理するためのディレクティブの両方が含まれています。

Azure 関数に対して `.fsx` を使用すると、一般に必要とされるアセンブリが自動的に含められるため、"定型" コードではなく、関数のコードに集中できます。

## <a name="folder-structure"></a>フォルダー構造

F# スクリプト プロジェクトのフォルダー構造は、次のようになります。

```
FunctionsProject
 | - MyFirstFunction
 | | - run.fsx
 | | - function.json
 | | - function.proj
 | - MySecondFunction
 | | - run.fsx
 | | - function.json
 | | - function.proj
 | - host.json
 | - extensions.csproj
 | - bin
```

関数アプリの構成に使用できる共有 [host.json](functions-host-json.md) ファイルがあります。 各関数には、独自のコード ファイル (.fsx) とバインディング構成ファイル (function.json) があります。

Functions ランタイムの[バージョン 2.x およびそれ以降](functions-versions.md) で必要なバインディング拡張機能は、`bin` フォルダー内の実際のライブラリファイルと共に、`extensions.csproj`ファイルで定義されます。 ローカルで開発する場合は、[バインド拡張機能を登録する](./functions-bindings-register.md#extension-bundles)必要があります。 Azure portal 上で関数を開発するときに、この登録が実行されます。

## <a name="binding-to-arguments"></a>引数へのバインド
「[Azure Functions のトリガーとバインドの開発者用リファレンス](functions-triggers-bindings.md)」で詳しく説明されているように、各バインドはいくつかの引数のセットをサポートしています。 たとえば、BLOB トリガーでサポートされている引数バインドの 1 つは、POCO です。これは、F# レコードを使用して表すことができます。 次に例を示します。

```fsharp
type Item = { Id: string }

let Run(blob: string, output: byref<Item>) =
    let item = { Id = "Some ID" }
    output <- item
```

F# Azure 関数は、1 つまたは複数の引数を受け取ります。 Azure Functions の引数についての説明では、"*入力*" 引数と "*出力*" 引数が出てきます。 入力引数は、名前が示すとおり、F# Azure 関数への入力です。 "*出力*" 引数は、関数から "*出力*" データを返す方法として機能する、変更可能なデータまたは `byref<>` 引数です。

上の例では、`blob` が入力引数で、`output` が出力引数です。 `output` に `byref<>` を使用したことに注意してください (`[<Out>]` という注釈を付ける必要はありません)。 `byref<>` 型を使用すると、関数は引数が参照するレコードまたはオブジェクトを変更できます。

F# レコードを入力型として使用する場合は、Azure Functions フレームワークがレコードを関数に渡す前にフィールドを適切に設定できるように、レコード定義を `[<CLIMutable>]` でマークする必要があります。 内部的には、 `[<CLIMutable>]` によってレコードのプロパティのセッターが生成されます。 次に例を示します。

```fsharp
[<CLIMutable>]
type TestObject =
    { SenderName : string
      Greeting : string }

let Run(req: TestObject, log: ILogger) =
    { req with Greeting = sprintf "Hello, %s" req.SenderName }
```

F# クラスも、入力と出力の両方の引数で使用できます。 クラスの場合、プロパティには通常、ゲッターとセッターが必要です。 次に例を示します。

```fsharp
type Item() =
    member val Id = "" with get,set
    member val Text = "" with get,set

let Run(input: string, item: byref<Item>) =
    let result = Item(Id = input, Text = "Hello from F#!")
    item <- result
```

## <a name="logging"></a>ログ記録
出力を F# の[ストリーミング ログ](../app-service/troubleshoot-diagnostic-logs.md)に記録するには、関数が [ILogger](/dotnet/api/microsoft.extensions.logging.ilogger) 型の引数を受け取る必要があります。 一貫性のために、この引数は `log`という名前にすることをお勧めします。 次に例を示します。

```fsharp
let Run(blob: string, output: byref<string>, log: ILogger) =
    log.LogInformation(sprintf "F# Azure Function processed a blob: %s" blob)
    output <- input
```

## <a name="async"></a>非同期
`async` ワークフローを使用できますが、結果は `Task` を返す必要があります。 そのためには、たとえば `Async.StartAsTask`を使用します。

```fsharp
let Run(req: HttpRequestMessage) =
    async {
        return new HttpResponseMessage(HttpStatusCode.OK)
    } |> Async.StartAsTask
```

## <a name="cancellation-token"></a>キャンセル トークン
関数が正常なシャットダウンを処理する必要がある場合は、 [`CancellationToken`](/dotnet/api/system.threading.cancellationtoken) 引数を設定できます。 これは、たとえば `async`と結合される場合があります。

```fsharp
let Run(req: HttpRequestMessage, token: CancellationToken)
    let f = async {
        do! Async.Sleep(10)
        return new HttpResponseMessage(HttpStatusCode.OK)
    }
    Async.StartAsTask(f, token)
```

## <a name="importing-namespaces"></a>名前空間のインポート
名前空間は、通常の方法で開くことができます。

```fsharp
open System.Net
open System.Threading.Tasks
open Microsoft.Extensions.Logging

let Run(req: HttpRequestMessage, log: ILogger) =
    ...
```

以下の名前空間は、自動的に開かれます。

* `System`
* `System.Collections.Generic`
* `System.IO`
* `System.Linq`
* `System.Net.Http`
* `System.Threading.Tasks`
* `Microsoft.Azure.WebJobs`
* `Microsoft.Azure.WebJobs.Host`.

## <a name="referencing-external-assemblies"></a>外部アセンブリの参照
同様に、フレームワーク アセンブリ参照を、`#r "AssemblyName"` ディレクティブと共に追加できます。

```fsharp
#r "System.Web.Http"

open System.Net
open System.Net.Http
open System.Threading.Tasks
open Microsoft.Extensions.Logging

let Run(req: HttpRequestMessage, log: ILogger) =
    ...
```

次のアセンブリは、Azure Functions をホストしている環境によって自動的に追加されます。

* `mscorlib`,
* `System`
* `System.Core`
* `System.Xml`
* `System.Net.Http`
* `Microsoft.Azure.WebJobs`
* `Microsoft.Azure.WebJobs.Host`
* `Microsoft.Azure.WebJobs.Extensions`
* `System.Web.Http`
* `System.Net.Http.Formatting`.

さらに、次のアセンブリは特別扱いされ、simplename によって参照される場合があります (例: `#r "AssemblyName"`)。

* `Newtonsoft.Json`
* `Microsoft.WindowsAzure.Storage`
* `Microsoft.ServiceBus`
* `Microsoft.AspNet.WebHooks.Receivers`
* `Microsoft.AspNEt.WebHooks.Common`.

プライベート アセンブリを参照する必要がある場合は、アセンブリ ファイルを関数に関連する `bin` フォルダーにアップロードし、ファイル名 (例: `#r "MyAssembly.dll"`) を使用して参照できます。 関数フォルダーにファイルをアップロードする方法については、パッケージ管理の次のセクションを参照してください。

## <a name="editor-prelude"></a>エディター準備
F# Compiler Services をサポートしているエディターは、Azure Functions が自動的に含める名前空間とアセンブリを認識しません。 そのため、使用されているアセンブリを検索するエディターを支援するための準備を行い、明示的に名前空間を開くと、便利な場合があります。 次に例を示します。

```fsharp
#if !COMPILED
#I "../../bin/Binaries/WebJobs.Script.Host"
#r "Microsoft.Azure.WebJobs.Host.dll"
#endif

open System
open Microsoft.Azure.WebJobs.Host
open Microsoft.Extensions.Logging

let Run(blob: string, output: byref<string>, log: ILogger) =
    ...
```

Azure Functions は、コードを実行するとき、 `COMPILED` が定義されているソースを処理するため、エディター準備は無視されます。

<a name="package"></a>

## <a name="package-management"></a>パッケージの管理
NuGet パッケージを F# 関数で使用するには、`project.json` ファイルを関数アプリのファイル システムにある関数のフォルダーに追加します。 次に示すのは、`Microsoft.ProjectOxford.Face` Version 1.1.0 への NuGet パッケージ参照を追加する、`project.json` ファイルの例です。

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

.NET Framework 4.6 のみがサポートされているので、次に示すように `project.json` ファイルが `net46` を指定していることを確認します。

`project.json` ファイルをアップロードすると、ランタイムによってパッケージが取得され、パッケージ アセンブリへの参照が自動的に追加されます。 `#r "AssemblyName"` ディレクティブを追加する必要はありません。 単に、必要な `open` ステートメントを `.fsx` ファイルに追加します。

エディターと F# Compile Services との相互作用を向上させるために、エディター準備に参照アセンブリを自動的に配置したい場合があるかもしれません。

### <a name="how-to-add-a-projectjson-file-to-your-azure-function"></a>`project.json` ファイルを Azure 関数に追加する方法
1. Azure ポータルで関数を開き、関数アプリが実行中であることを確認して開始します。 これにより、パッケージのインストール出力が表示されるストリーミング ログへのアクセス権が付与されます。
2. `project.json` ファイルをアップロードするには、「[関数アプリ ファイルを更新する方法](functions-reference.md#fileupdate)」で説明されているいずれかの方法を使用します。 [Azure Functions の継続的なデプロイ](functions-continuous-deployment.md)を使用している場合は、`project.json` ファイルをステージング環境の分岐に追加して、デプロイ分岐に追加する前に実験することができます。
3. `project.json` ファイルが追加された後、関数のストリーミング ログの出力は次の例のようになります。

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
環境変数またはアプリ設定値を取得するには、次の例のように、 `System.Environment.GetEnvironmentVariable`を使用します。

```fsharp
open System.Environment
open Microsoft.Extensions.Logging

let Run(timer: TimerInfo, log: ILogger) =
    log.LogInformation("Storage = " + GetEnvironmentVariable("AzureWebJobsStorage"))
    log.LogInformation("Site = " + GetEnvironmentVariable("WEBSITE_SITE_NAME"))
```

## <a name="reusing-fsx-code"></a>.fsx コードの再利用
他の `.fsx` ファイルのコードを利用するには、`#load` ディレクティブを使用します。 次に例を示します。

`run.fsx`

```fsharp
#load "logger.fsx"

let Run(timer: TimerInfo, log: ILogger) =
    mylog log (sprintf "Timer: %s" DateTime.Now.ToString())
```

`logger.fsx`

```fsharp
let mylog(log: ILogger, text: string) =
    log.LogInformation(text);
```

`#load` ディレクティブに指定するパスは、`.fsx` ファイルの場所からの相対パスです。

* `#load "logger.fsx"` によって、関数フォルダーにあるファイルが読み込まれます。
* `#load "package\logger.fsx"` によって、関数フォルダー内の `package` フォルダーにあるファイルが読み込まれます。
* `#load "..\shared\mylogger.fsx"` によって、関数フォルダーと同じレベル (`wwwroot` の直下) にある `shared` フォルダーのファイルが読み込まれます。

`#load` ディレクティブは、`.fsx` (F# スクリプト) ファイルだけで動作します。`.fs` ファイルでは動作しません。

## <a name="next-steps"></a>次のステップ
詳細については、次のリソースを参照してください。

* [F# のガイド](/dotnet/articles/fsharp/index)
* [Azure Functions のベスト プラクティス](functions-best-practices.md)
* [Azure Functions 開発者向けリファレンス](functions-reference.md)
* [Azure Functions triggers and bindings (Azure Functions のトリガーとバインド)](functions-triggers-bindings.md)
* [Azure Functions のテスト](functions-test-a-function.md)
* [Azure Functions のスケーリング方法](functions-scale.md)
