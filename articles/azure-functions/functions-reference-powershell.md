---
title: Azure Functions の PowerShell 開発者向けリファレンス
description: PowerShell を使用して関数を開発する方法について説明します。
author: eamonoreilly
ms.topic: conceptual
ms.custom: devx-track-dotnet, devx-track-azurepowershell
ms.date: 04/22/2019
ms.openlocfilehash: 61ed3ed274505101c65e251260bd759fe78f7b31
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "97936789"
---
# <a name="azure-functions-powershell-developer-guide"></a>Azure Functions の PowerShell 開発者向けガイド

この記事では、PowerShell を使用して Azure Functions を作成する方法について詳しく説明します。

PowerShell Azure Functions (関数) は、トリガーされた時点で実行される PowerShell スクリプトとして表されます。 それぞれの関数スクリプトには、関連する `function.json` ファイルが存在し、関数の動作 (トリガー方法や入力および出力パラメーターなど) が定義されています。 詳細については、[トリガーとバインディングに関する記事](functions-triggers-bindings.md)を参照してください。 

他の種類の関数と同様、PowerShell スクリプト関数も、`function.json` ファイルで定義されているすべての入力バインディングの名前に対応したパラメーターを受け入れます。 また、関数を開始したトリガーに関する追加情報を含む `TriggerMetadata` パラメーターも渡されます。

この記事では、「[Azure Functions の開発者向けガイド](functions-reference.md)」を既に読んでいることを前提としています。 また、[Functions の PowerShell 向けクイックスタート](./create-first-function-vs-code-powershell.md)で、初めての PowerShell 関数を作成しておく必要があります。

## <a name="folder-structure"></a>フォルダー構造

PowerShell プロジェクトに必要なフォルダー構造は次のようになります。 この既定値は変更可能です。 詳しくは、後の [scriptFile](#configure-function-scriptfile) に関するセクションをご覧ください。

```
PSFunctionApp
 | - MyFirstFunction
 | | - run.ps1
 | | - function.json
 | - MySecondFunction
 | | - run.ps1
 | | - function.json
 | - Modules
 | | - myFirstHelperModule
 | | | - myFirstHelperModule.psd1
 | | | - myFirstHelperModule.psm1
 | | - mySecondHelperModule
 | | | - mySecondHelperModule.psd1
 | | | - mySecondHelperModule.psm1
 | - local.settings.json
 | - host.json
 | - requirements.psd1
 | - profile.ps1
 | - extensions.csproj
 | - bin
```

プロジェクトのルートには、関数アプリの構成に使用できる共有 [`host.json`](functions-host-json.md) ファイルがあります。 各関数には、独自のコード ファイル (.ps1) とバインディング構成ファイル (`function.json`) が含まれるフォルダーがあります。 function.json ファイルの親ディレクトリの名前は常に関数の名前です。

特定のバインディングには、`extensions.csproj` ファイルが必要になります。 Functions ランタイムの[バージョン 2.x 以降](functions-versions.md)に必要なバインド拡張機能は `extensions.csproj` ファイルで定義されており、実際のライブラリ ファイルは `bin` フォルダーにあります。 ローカルで開発する場合は、[バインド拡張機能を登録する](functions-bindings-register.md#extension-bundles)必要があります。 Azure portal 上で関数を開発するときに、この登録が実行されます。

PowerShell 関数アプリには、必要に応じて `profile.ps1` を含めることができます。これは、関数アプリの実行開始時 (" *[コールド スタート](#cold-start)* " とも呼ばれます) に実行されます。 詳細については、「[PowerShell プロファイル](#powershell-profile)」を参照してください。

## <a name="defining-a-powershell-script-as-a-function"></a>PowerShell スクリプトを関数として定義する

既定では、Functions ランタイムは `run.ps1` で関数を検索します。`run.ps1` は、対応する `function.json` と同じ親ディレクトリを共有します。

スクリプトは、実行時に多数の引数が渡されます。 それらのパラメーターを処理するには、次の例のように、スクリプトの先頭に `param` ブロックを追加します。

```powershell
# $TriggerMetadata is optional here. If you don't need it, you can safely remove it from the param block
param($MyFirstInputBinding, $MySecondInputBinding, $TriggerMetadata)
```

### <a name="triggermetadata-parameter"></a>TriggerMetadata パラメーター

`TriggerMetadata` パラメーターは、トリガーに関する追加情報を指定するために使用されます。 追加のメタデータはバインディングによって異なりますが、いずれにも、次のデータが格納される `sys` プロパティが含まれています。

```powershell
$TriggerMetadata.sys
```

| プロパティ   | 説明                                     | Type     |
|------------|-------------------------------------------------|----------|
| UtcNow     | 関数がトリガーされた日時 (UTC)        | DateTime |
| MethodName | トリガーされた関数の名前     | string   |
| RandGuid   | この関数の実行に対して一意の GUID | string   |

トリガーの種類ごとに、異なる一連のメタデータがあります。 たとえば、`QueueTrigger` の `$TriggerMetadata` には、数ある中でも `InsertionTime`、`Id`、`DequeueCount` が格納されます。 キュー トリガーのメタデータの詳細については、[キュー トリガーの公式ドキュメント](functions-bindings-storage-queue-trigger.md#message-metadata)を参照してください。 実際に使用している[トリガー](functions-triggers-bindings.md)のドキュメントを確認すると、そのトリガーのメタデータに含まれるものがわかります。

## <a name="bindings"></a>バインド

PowerShell では、[バインディング](functions-triggers-bindings.md)の構成と定義が関数の function.json に記述されます。 関数は、さまざまな方法でバインドを操作します。

### <a name="reading-trigger-and-input-data"></a>トリガーと入力データの読み取り

トリガーと入力バインディングは、関数に渡されたパラメーターとして読み取られます。 入力バインディングの `direction` は、function.json で `in` に設定されています。 `function.json` で定義された `name` プロパティは、`param` ブロックにおけるパラメーターの名前です。 PowerShell ではバインディングに名前付きパラメーターが使用されるため、パラメーターの順序は重要ではありません。 ただし、`function.json` で定義されているバインディングの順序に従うことをお勧めします。

```powershell
param($MyFirstInputBinding, $MySecondInputBinding)
```

### <a name="writing-output-data"></a>出力データの書き込み

Functions では、出力バインディングの `direction` が function.json で `out` に設定されています。 出力バインディングに書き込むには、`Push-OutputBinding` コマンドレットを使用します。このコマンドレットは、Functions ランタイムから利用できます。 どのような場合も、`function.json` で定義されたバインディングの `name` プロパティは、`Push-OutputBinding` コマンドレットの `Name` パラメーターに対応します。

関数スクリプトから `Push-OutputBinding` を呼び出す方法を次に示します。

```powershell
param($MyFirstInputBinding, $MySecondInputBinding)

Push-OutputBinding -Name myQueue -Value $myValue
```

特定のバインディングの値をパイプラインを介して渡すこともできます。

```powershell
param($MyFirstInputBinding, $MySecondInputBinding)

Produce-MyOutputValue | Push-OutputBinding -Name myQueue
```

`Push-OutputBinding` の動作は、`-Name` に指定された値に応じて異なります。

* 指定された名前を有効な出力バインディングに解決できない場合は、エラーがスローされます。

* 出力バインディングが値のコレクションを受け入れると、`Push-OutputBinding` を繰り返し呼び出して複数の値をプッシュすることができます。

* 出力バインディングがシングルトン値しか受け入れない場合は、2 回目に `Push-OutputBinding` を呼び出した時点でエラーが発生します。

#### <a name="push-outputbinding-syntax"></a>`Push-OutputBinding` の構文

`Push-OutputBinding` の呼び出しに使用できる有効なパラメーターを次に示します。

| 名前 | Type | [位置] | 説明 |
| ---- | ---- |  -------- | ----------- |
| **`-Name`** | String | 1 | 設定する出力バインディングの名前。 |
| **`-Value`** | Object | 2 | 設定する出力バインディングの値。パイプライン ByValue から受け取ります。 |
| **`-Clobber`** | SwitchParameter | named | (省略可能) 指定した場合は、指定した出力バインディングに対して値が強制的に設定されます。 | 

以下の共通パラメーターもサポートされています。 
* `Verbose`
* `Debug`
* `ErrorAction`
* `ErrorVariable`
* `WarningAction`
* `WarningVariable`
* `OutBuffer`
* `PipelineVariable`
* `OutVariable` 

詳細については、「[About CommonParameters (共通パラメーターについて)](/powershell/module/microsoft.powershell.core/about/about_commonparameters)」を参照してください。

#### <a name="push-outputbinding-example-http-responses"></a>Push-OutputBinding の例: HTTP 応答

HTTP トリガーは、`response` という名前の出力バインディングを使用して応答を返します。 次の例では、出力バインディング `response` に値 "output #1" が設定されます。

```powershell
PS >Push-OutputBinding -Name response -Value ([HttpResponseContext]@{
    StatusCode = [System.Net.HttpStatusCode]::OK
    Body = "output #1"
})
```

出力先は HTTP ですが、シングルトン値しか受け入れないため、`Push-OutputBinding` が 2 回呼び出されると、エラーがスローされます。

```powershell
PS >Push-OutputBinding -Name response -Value ([HttpResponseContext]@{
    StatusCode = [System.Net.HttpStatusCode]::OK
    Body = "output #2"
})
```

出力がシングルトン値しか受け入れない場合は、`-Clobber` パラメーターを使用すると、コレクションへの追加を試みる代わりに古い値をオーバーライドすることができます。 次の例では、既に値が追加されていることを前提としています。 `-Clobber` を使用することで、次の例からの応答では、既存の値がオーバーライドされ、"output #3" という値が返されます。

```powershell
PS >Push-OutputBinding -Name response -Value ([HttpResponseContext]@{
    StatusCode = [System.Net.HttpStatusCode]::OK
    Body = "output #3"
}) -Clobber
```

#### <a name="push-outputbinding-example-queue-output-binding"></a>Push-OutputBinding の例: キュー出力バインディング

`Push-OutputBinding` は、出力バインディング ([Azure Queue storage 出力バインディング](functions-bindings-storage-queue-output.md)など) にデータを送信するために使用されます。 次の例では、キューに書き込まれるメッセージの値は "output #1" です。

```powershell
PS >Push-OutputBinding -Name outQueue -Value "output #1"
```

ストレージ キューの出力バインディングは、複数の出力値を受け入れます。 この場合、次の例を 1 つ目の例の後に呼び出すと、"output #1" と "output #2" の 2 つの項目を含むリストがキューに書き込まれます。

```powershell
PS >Push-OutputBinding -Name outQueue -Value "output #2"
```

次の例は、前述の 2 つを呼び出した後に呼び出すと、さらに 2 つの値が出力コレクションに追加されます。

```powershell
PS >Push-OutputBinding -Name outQueue -Value @("output #3", "output #4")
```

キューへの書き込み時には、メッセージに "output #1"、"output #2"、"output #3"、"output #4" という 4 つの値が格納されます。

#### <a name="get-outputbinding-cmdlet"></a>`Get-OutputBinding` コマンドレット

出力バインディングに現在設定されている値を取得するには、`Get-OutputBinding` コマンドレットを使用できます。 このコマンドレットは、出力バインディングの名前とそれぞれに対応する値を含んだハッシュテーブルを取得します。 

`Get-OutputBinding` を使用して現在のバインディング値を返す例を次に示します。

```powershell
Get-OutputBinding
```

```Output
Name                           Value
----                           -----
MyQueue                        myData
MyOtherQueue                   myData
```

`Get-OutputBinding` にも `-Name` というパラメーターがあります。これを使用すると、次の例に示すように、返されたバインディングをフィルター処理することができます。

```powershell
Get-OutputBinding -Name MyQ*
```

```Output
Name                           Value
----                           -----
MyQueue                        myData
```

`Get-OutputBinding` では、ワイルドカード (*) がサポートされます。

## <a name="logging"></a>ログ記録

PowerShell 関数におけるログは、通常の PowerShell のログと同様に機能します。 各出力ストリームへの書き込みには、ログ コマンドレットを使用できます。 各コマンドレットは、Functions で使用されるログ レベルに対応します。

| Functions のログ レベル | ログ コマンドレット |
| ------------- | -------------- |
| エラー | **`Write-Error`** |
| 警告 | **`Write-Warning`**  | 
| Information | **`Write-Information`** <br/> **`Write-Host`** <br /> **`Write-Output`**      | Information | "_情報_" レベル ログへの書き込み |
| デバッグ | **`Write-Debug`** |
| Trace | **`Write-Progress`** <br /> **`Write-Verbose`** |

これらのコマンドレットに加え、パイプラインに書き込まれた内容はすべて `Information` ログ レベルにリダイレクトされ、PowerShell の既定の書式で表示されます。

> [!IMPORTANT]
> `Write-Verbose` または `Write-Debug` コマンドレットを使用するだけでは、詳細およびデバッグ レベルのログを表示するには不十分です。 ログ レベルのしきい値も構成して、実際に注目するログのレベルを宣言する必要があります。 詳細については、「[関数アプリのログ レベルの構成](#configure-the-function-app-log-level)」を参照してください。

### <a name="configure-the-function-app-log-level"></a>関数アプリのログ レベルの構成

Azure Functions では、しきい値レベルを定義することで、関数によるログへの書き込み方法を簡単に制御することができます。 コンソールに書き込まれるすべてのトレースのしきい値を設定するには、[`host.json` ファイル][host.json referenceの  プロパティを使用します。 この設定は、関数アプリのすべての関数に適用されます。

次の例では、すべての関数の詳細ログが有効になるようしきい値を設定しますが、`MyFunction` という名前の関数についてはデバッグ ログが有効になるようしきい値を設定しています。

```json
{
    "logging": {
        "logLevel": {
            "Function.MyFunction": "Debug",
            "default": "Trace"
        }
    }
}  
```

詳細については、[host.json] のリファレンスを参照してください。

### <a name="viewing-the-logs"></a>ログの表示

Azure 内で実行されている関数アプリは、Application Insights を使用して監視することができます。 関数のログの表示とクエリについて詳しくは、「[Azure Functions を監視する](functions-monitoring.md)」をご覧ください。

開発のために関数アプリをローカルで実行している場合、ログの既定の出力先はファイル システムとなります。 コンソールでログを確認するには、`AZURE_FUNCTIONS_ENVIRONMENT` 環境変数を `Development` に設定してから関数アプリを開始してください。

## <a name="triggers-and-bindings-types"></a>トリガーとバインディングの種類

関数アプリで使用できるトリガーとバインディングは多数あります。 トリガーとバインディングの全一覧は、[こちらを参照してください](functions-triggers-bindings.md#supported-bindings)。

コード内では、すべてのトリガーとバインディングが、以下に示した少数の実数データ型として表現されます。

* Hashtable
* string
* byte[]
* INT
* double
* HttpRequestContext
* HttpResponseContext

この一覧の最初の 5 つの型は、標準 .NET 型です。 最後の 2 つは、[HttpTrigger トリガー](#http-triggers-and-bindings)のみで使用されます。

関数に含まれる各バインディング パラメーターは、これらの型のいずれかにする必要があります。

### <a name="http-triggers-and-bindings"></a>HTTP トリガーとバインディング

HTTP、webhook トリガー、および HTTP 出力バインディングでは、要求オブジェクトと応答オブジェクトを使用して HTTP メッセージングを表します。

#### <a name="request-object"></a>要求オブジェクト

スクリプトに渡される要求オブジェクトは `HttpRequestContext` 型で、次のプロパティを持ちます。

| プロパティ  | 説明                                                    | Type                      |
|-----------|----------------------------------------------------------------|---------------------------|
| **`Body`**    | 要求の本文を格納するオブジェクト。 `Body` は、データに基づいて最適な型にシリアル化されます。 たとえば、データが JSON である場合はハッシュテーブルとして渡されます。 データが文字列の場合は文字列として渡されます。 | object |
| **`Headers`** | 要求ヘッダーを格納するディクショナリ。                | Dictionary<string,string><sup>*</sup> |
| **`Method`** | 要求の HTTP メソッド。                                | string                    |
| **`Params`**  | 要求のルーティング パラメーターを格納するオブジェクト。 | Dictionary<string,string><sup>*</sup> |
| **`Query`** | クエリ パラメーターを格納するオブジェクト。                  | Dictionary<string,string><sup>*</sup> |
| **`Url`** | 要求の URL。                                        | string                    |

<sup>*</sup>`Dictionary<string,string>` のキーはすべて、大文字と小文字が区別されません。

#### <a name="response-object"></a>応答オブジェクト

返すべき応答オブジェクトは `HttpResponseContext` 型で、次のプロパティを持ちます。

| プロパティ      | 説明                                                 | Type                      |
|---------------|-------------------------------------------------------------|---------------------------|
| **`Body`**  | 応答の本文を格納するオブジェクト。           | object                    |
| **`ContentType`** | 応答のコンテンツ タイプを設定するための省略表現。 | string                    |
| **`Headers`** | 応答ヘッダーを格納するオブジェクト。               | ディクショナリまたはハッシュテーブル   |
| **`StatusCode`**  | 応答の HTTP 状態コード。                       | 文字列または整数             |

#### <a name="accessing-the-request-and-response"></a>要求と応答へのアクセス

HTTP トリガーを使用する場合、他の入力バインディングと同じ方法で HTTP 要求にアクセスできます。 これは `param` ブロックにあります。

応答を返すには、次に示すように、`HttpResponseContext` オブジェクトを使用します。

`function.json`

```json
{
  "bindings": [
    {
      "type": "httpTrigger",
      "direction": "in",
      "authLevel": "anonymous"
    },
    {
      "type": "http",
      "direction": "out"
    }
  ]
}
```

`run.ps1`

```powershell
param($req, $TriggerMetadata)

$name = $req.Query.Name

Push-OutputBinding -Name res -Value ([HttpResponseContext]@{
    StatusCode = [System.Net.HttpStatusCode]::OK
    Body = "Hello $name!"
})
```

この関数を呼び出した結果は次のとおりです。

```
PS > irm http://localhost:5001?Name=Functions
Hello Functions!
```

### <a name="type-casting-for-triggers-and-bindings"></a>トリガーとバインドの型キャスト

BLOB バインディングなど、いくつかのバインディングでは、パラメーターの型を指定できます。

たとえば、Blob Storage のデータを文字列として指定するには、`param` ブロックに次の型キャストを追加します。

```powershell
param([string] $myBlob)
```

## <a name="powershell-profile"></a>PowerShell プロファイル

PowerShell には、PowerShell プロファイルという概念があります。 PowerShell プロファイルに詳しくない場合は、「[About profiles (プロファイルについて)](/powershell/module/microsoft.powershell.core/about/about_profiles)」を参照してください。

PowerShell 関数では、プロファイル スクリプトは、最初のデプロイ時とアイドル状態からの復帰 ([コールド スタート](#cold-start)) 後にアプリの PowerShell worker インスタンスごとに 1 回実行されます。 [PSWorkerInProcConcurrencyUpperBound](#concurrency) 値を設定してコンカレンシーを有効にすると、作成された実行空間ごとにプロファイルスクリプトが実行されます。

Visual Studio Code や Azure Functions Core Tools などのツールを使用して関数アプリを作成すると、既定の `profile.ps1` が自動的に作成されます。 既定のプロファイルは [Core Tools GitHub リポジトリ](https://github.com/Azure/azure-functions-core-tools/blob/dev/src/Azure.Functions.Cli/StaticResources/profile.ps1)で保持されており、次の機能を備えています。

* Azure に対する自動 MSI 認証。
* 必要に応じて Azure PowerShell `AzureRM` PowerShell エイリアスを有効にする機能。

## <a name="powershell-versions"></a>PowerShell のバージョン

次の表は、Functions ランタイムのメジャー バージョンと、それに必要な .NET バージョンごとに使用可能な PowerShell バージョンを示しています。

| Functions バージョン | PowerShell バージョン                               | .NET バージョン  | 
|-------------------|--------------------------------------------------|---------------|
| 3.x (推奨) | PowerShell 7 (推奨)<br/>PowerShell Core 6 | .NET Core 3.1<br/>.NET Core 2.1 |
| 2.x               | PowerShell Core 6                                | .NET Core 2.2 |

現在のバージョンを確認するには、任意の関数から `$PSVersionTable` を出力します。

### <a name="running-local-on-a-specific-version"></a>特定のバージョンでのローカルな実行

ローカルで実行する場合、Azure Functions ランタイムは既定で PowerShell Core 6 を使用します。 代わりに PowerShell 7 を使用してローカルで実行するには、プロジェクト ルートの local.setting.json ファイルの `Values` 配列に設定 `"FUNCTIONS_WORKER_RUNTIME_VERSION" : "~7"` を追加する必要があります。 PowerShell 7 でローカルに実行する場合、local.settings.json ファイルは次の例のようになります。 

```json
{
  "IsEncrypted": false,
  "Values": {
    "AzureWebJobsStorage": "",
    "FUNCTIONS_WORKER_RUNTIME": "powershell",
    "FUNCTIONS_WORKER_RUNTIME_VERSION" : "~7"
  }
}
```

### <a name="changing-the-powershell-version"></a>PowerShell のバージョンの変更

PowerShell Core 6 から PowerShell 7 にアップグレードできるようにするには、関数アプリがバージョン 3.x で実行されている必要があります。 これを行う方法については、「[現在のランタイム バージョンの表示と更新](set-runtime-version.md#view-and-update-the-current-runtime-version)」を参照してください。

関数アプリで使用される PowerShell のバージョンを変更するには、次のステップに従います。 これは、Azure portal または PowerShell を使用して行うことができます。

# <a name="portal"></a>[ポータル](#tab/portal)

1. [Azure portal](https://portal.azure.com) で、関数アプリに移動します。

1. **[設定]** で **[構成]** を選択します。 **[全般設定]** タブで、**PowerShell のバージョン** を探します。 

    :::image type="content" source="media/functions-reference-powershell/change-powershell-version-portal.png" alt-text="関数アプリで使用される PowerShell のバージョンを選択する"::: 

1. 目的の **PowerShell Core のバージョン** を選択し、 **[保存]** を選択します。 再起動の保留に関する警告が表示されたら、 **[続行]** を選択します。 関数アプリは、選択した PowerShell のバージョンで再起動します。 

# <a name="powershell"></a>[PowerShell](#tab/powershell)

PowerShell のバージョンを変更するには、次のスクリプトを実行します。 

```powershell
Set-AzResource -ResourceId "/subscriptions/<SUBSCRIPTION_ID>/resourceGroups/<RESOURCE_GROUP>/providers/Microsoft.Web/sites/<FUNCTION_APP>/config/web" -Properties @{  powerShellVersion  = '<VERSION>' } -Force -UsePatchSemantics

```

`<SUBSCRIPTION_ID>`、`<RESOURCE_GROUP>`、`<FUNCTION_APP>` を、Azure サブスクリプションの ID、リソース グループと関数アプリの名前にそれぞれ置き換えます。  `<VERSION>` を `~6` または `~7` のいずれかに置き換えます。 返されたハッシュ テーブルの `Properties` で `powerShellVersion` 設定の更新された値を確認できます。 

---

構成に変更が加えられると、関数アプリが再起動します。

## <a name="dependency-management"></a>依存関係管理

Functions では、依存関係の管理に [PowerShell ギャラリー](https://www.powershellgallery.com)を使用できます。 依存関係の管理を有効になっていると、.psd1 ファイルを使って、必要なモジュールが自動的にダウンロードされます。 この動作を有効にするには、次の例のように、[host.json ](functions-host-json.md) ファイルのルートで `managedDependency` プロパティを `true` に設定します。

```json
{
  "managedDependency": {
          "enabled": true
       }
}
```

新しい PowerShell 関数プロジェクトを作成すると、依存関係の管理が既定で有効になり、[`Az` モジュール](/powershell/azure/new-azureps-module-az)が組み込まれます。 現在サポートされているモジュールの最大数は 10 です。 サポートされている構文は、次の requirements.psd1 に示すように、 _`MajorNumber`_ `.*` または厳密なモジュール バージョンです。

```powershell
@{
    Az = '1.*'
    SqlServer = '21.1.18147'
}
```

requirements.psd1 ファイルを更新すると、更新されたモジュールは、再起動後にインストールされます。

> [!NOTE]
> 管理対象の依存関係では、モジュールをダウンロードするときに、 www.powershellgallery.com にアクセスする必要があります。 ローカルで実行する場合は、必要なファイアウォール規則を追加して、ランタイムがこの URL にアクセスできることを確認してください。

> [!NOTE]
> 現在、管理対象の依存関係では、対話形式、または `Install-Module` を呼び出すときに `-AcceptLicense` スイッチを指定する方法でライセンスを受け入れるモジュールをサポートしていません。

次のアプリケーション設定を使用して、管理対象の依存関係をダウンロードしてインストールする方法を変更できます。 アプリのアップグレードは `MDMaxBackgroundUpgradePeriod` 以内に開始され、アップグレード プロセスはほぼ `MDNewSnapshotCheckPeriod` 以内に完了します。

| 関数アプリ設定              | 既定値             | 説明                                         |
|   -----------------------------   |   -------------------     |  -----------------------------------------------    |
| **`MDMaxBackgroundUpgradePeriod`**      | `7.00:00:00` (7 日)     | 各 PowerShell ワーカー プロセスは、そのプロセスの開始時に PowerShell ギャラリーでモジュールのアップグレードのチェックを開始し、その後は `MDMaxBackgroundUpgradePeriod` ごとにチェックします。 PowerShell ギャラリーで利用可能になった新しいモジュール バージョンは、ファイル システムにインストールされ、PowerShell ワーカーが使用できるになります。 この値を小さくすると、関数アプリは新しいモジュール バージョンを早く取得できますが、アプリ リソースの使用量 (ネットワーク I/O、CPU、ストレージ) も増加します。 この値を大きくすると、アプリ リソースの使用量は減少しますが、アプリへの新しいモジュール バージョンの配信が遅れる可能性があります。 | 
| **`MDNewSnapshotCheckPeriod`**         | `01:00:00` (1 時間)       | 新しいモジュール バージョンがファイル システムにインストールされたら、すべての PowerShell ワーカー プロセスを再起動する必要があります。 PowerShell ワーカーを再起動すると、現在の関数の実行が中断される可能性があるため、アプリの可用性がその影響を受けます。 すべての PowerShell ワーカー プロセスが再起動されるまで、関数呼び出しでは、前のモジュール バージョンまたは新しいモジュール バージョンのいずれかが使用される可能性があります。 すべての PowerShell ワーカーの再起動は `MDNewSnapshotCheckPeriod` 以内に完了します。 この値を大きくすると、中断の頻度は低くなりますが、関数呼び出しで、前のモジュール バージョンまたは新しいモジュール バージョンのいずれかが非決定的に使用される期間が長くなることもあります。 |
| **`MDMinBackgroundUpgradePeriod`**      | `1.00:00:00` (1 日)     | ワーカーの頻繁な再起動によってモジュールのアップグレードが過剰にならないように、任意のワーカーで直近 `MDMinBackgroundUpgradePeriod` 以内にモジュールのアップグレード確認が開始されているときは、その確認は行われません。 |

独自のカスタム モジュールを利用する方法が、通常の方法とは若干異なります。

ローカル コンピューターでは、モジュールは、`$env:PSModulePath` にあるグローバルに利用できるフォルダーのいずれかにインストールされます。 Azure で実行されているときは、お使いのマシンにインストールされたモジュールにアクセスできません。 つまり、PowerShell 関数アプリの `$env:PSModulePath` は、通常の PowerShell スクリプトの `$env:PSModulePath` とは異なります。

Functions では、`PSModulePath` に次の 2 つのパスが存在します。

* 関数アプリのルートに存在する `Modules` フォルダー。
* PowerShell 言語ワーカーによって制御される `Modules` フォルダーのパス。


### <a name="function-app-level-modules-folder"></a>関数アプリレベルの `Modules` フォルダー

カスタム モジュールを使用するには、関数が依存しているモジュールを `Modules` フォルダーに置きます。 このフォルダーにあるモジュールは、自動的に Functions Runtime から利用できる状態になります。 関数アプリ内のどの関数でもこれらのモジュールを使用できます。 

> [!NOTE]
> requirements.psd1 ファイルで指定されているモジュールは、自動的にダウンロードされてパスに組み込まれるため、modules フォルダーに含める必要はありません。 これらは、ローカルでは `$env:LOCALAPPDATA/AzureFunctions` フォルダーに、クラウドで実行されるときは `/data/ManagedDependencies` フォルダーに格納されます。

カスタム モジュール機能を利用するには、関数アプリのルートに `Modules` フォルダーを作成します。 関数で使用したいモジュールをこの場所にコピーします。

```powershell
mkdir ./Modules
Copy-Item -Path /mymodules/mycustommodule -Destination ./Modules -Recurse
```

`Modules` フォルダーでは、関数アプリのフォルダー構造が次のようになっている必要があります。

```
PSFunctionApp
 | - MyFunction
 | | - run.ps1
 | | - function.json
 | - Modules
 | | - MyCustomModule
 | | - MyOtherCustomModule
 | | - MySpecialModule.psm1
 | - local.settings.json
 | - host.json
 | - requirements.psd1
```

関数アプリを起動すると、PowerShell 言語ワーカーによりこの `Modules` フォルダーが `$env:PSModulePath` に追加されるため、通常の PowerShell スクリプトと同様に、モジュールの自動読み込みを利用できます。

### <a name="language-worker-level-modules-folder"></a>言語ワーカー レベルの `Modules` フォルダー

いくつかのモジュールは、PowerShell 言語ワーカーによってよく使用されます。 これらのモジュールは、`PSModulePath` の最後の位置で定義されます。 

現在のモジュールの一覧は次のとおりです。

* [Microsoft.PowerShell.Archive](https://www.powershellgallery.com/packages/Microsoft.PowerShell.Archive): アーカイブ (`.zip`、`.nupkg` など) に使用されるモジュール。
* **ThreadJob**: PowerShell ジョブ API のスレッドベースの実装です。

既定では、Functions では、これらのモジュールの最新のバージョンが使用されます。 特定のバージョンを使用するには、関数アプリの `Modules` フォルダーにその特定のバージョンのモジュールを格納してください。

## <a name="environment-variables"></a>環境変数

Functions では、サービス接続文字列などの[アプリ設定](functions-app-settings.md)は、実行中に環境変数として公開されます。 次の例に示すように、これらの設定には `$env:NAME_OF_ENV_VAR` を使用してアクセスできます。

```powershell
param($myTimer)

Write-Host "PowerShell timer trigger function ran! $(Get-Date)"
Write-Host $env:AzureWebJobsStorage
Write-Host $env:WEBSITE_SITE_NAME
```

[!INCLUDE [Function app settings](../../includes/functions-app-settings.md)]

ローカルで実行する場合、アプリ設定は [local.settings.json](functions-run-local.md#local-settings-file) プロジェクト ファイルから読み取られます。

## <a name="concurrency"></a>コンカレンシー

既定では、Functions PowerShell ランタイムで一度に処理できる関数の呼び出しは 1 つだけです。 ただし、以下の状況では、このコンカレンシー レベルが十分でない場合があります。

* 多数の呼び出しを同時に処理しようとした場合。
* 他の関数を呼び出す関数が同じ関数アプリ内にある場合。

ワークロードの種類に応じて、いくつかのコンカレンシー モデルを調べることができます。

* ```FUNCTIONS_WORKER_PROCESS_COUNT``` を増やします。 これにより、同じインスタンス内の複数のプロセスで関数の呼び出しを処理できますが、特定の CPU とメモリでオーバーヘッドが発生します。 一般に、I/O バインド関数は、このオーバーヘッドの影響を受けません。 CPU バインド関数の場合、影響が大きくなる可能性があります。

* ```PSWorkerInProcConcurrencyUpperBound``` アプリの設定値を増やします。 これにより、同じプロセス内に複数の実行空間を作成できるため、CPU とメモリのオーバーヘッドが大幅に減少します。

この環境変数は、関数アプリの[アプリ設定](functions-app-settings.md)で設定します。

使い方によっては、Durable Functions によってスケーラビリティが大幅に向上する場合があります。 詳細については、[Durable Functions のアプリケーション パターン](./durable/durable-functions-overview.md?tabs=powershell#application-patterns)に関する記事を参照してください。

>[!NOTE]
> "使用可能な実行空間がないため、要求がキューに置かれています" という警告が表示される場合がありますが、これはエラーではありません。 このメッセージは、要求がキューに登録されていて、前の要求が完了したときに処理されることを通知するものです。

### <a name="considerations-for-using-concurrency"></a>コンカレンシーの使用に関する注意点

PowerShell は、既定では "_シングル スレッド_" のスクリプト言語です。 ただし、同じプロセス内で複数の PowerShell 実行空間を使用することで、コンカレンシーを追加できます。 作成される実行空間の量は、```PSWorkerInProcConcurrencyUpperBound``` のアプリケーション設定と一致します。 スループットは、選択したプランで使用可能な CPU とメモリの量によって影響を受けます。

開発者の入力の手間を軽減するために、Azure PowerShell では、"_プロセスレベル_" のコンテキストと状態が使用されています。 ただし、関数アプリでコンカレンシーを有効にし、状態の変更を伴うアクションを呼び出した場合は、最終的に競合状態に陥る可能性があります。 このような競合状態をデバッグするのは困難です。なぜなら、一方の呼び出しが特定の状態に依存しているのに、もう一方の呼び出しがその状態を変更したためです。

一部の操作にはかなりの時間がかかる可能性があるため、Azure PowerShell のコンカレンシーには非常に大きな価値があります。 しかし、実際に使用する場合には注意が必要です。 競合状態が発生していると思われる場合は、PSWorkerInProcConcurrencyUpperBound アプリ設定を `1` に設定し、代わりに、コンカレンシーに対して[言語ワーカー プロセス レベルの分離](functions-app-settings.md#functions_worker_process_count)を使用します。

## <a name="configure-function-scriptfile"></a>関数の `scriptFile` を構成する

既定では、PowerShell 関数は `run.ps1` から実行されます。これは、対応する `function.json` と同じ親ディレクトリを共有するファイルです。

`function.json` の `scriptFile` プロパティを使用すると、次の例のようなフォルダー構造を取得できます。

```
FunctionApp
 | - host.json
 | - myFunction
 | | - function.json
 | - lib
 | | - PSFunction.ps1
```

この場合、`myFunction` の `function.json` には、実行するにあたってエクスポートされた関数のファイルを参照する `scriptFile` プロパティが含まれています。

```json
{
  "scriptFile": "../lib/PSFunction.ps1",
  "bindings": [
    // ...
  ]
}
```

## <a name="use-powershell-modules-by-configuring-an-entrypoint"></a>entryPoint を構成して PowerShell モジュールを使用する

この記事では、テンプレートによって生成される既定の `run.ps1` スクリプト ファイル内の PowerShell 関数について説明してきました。
ただし、PowerShell モジュールには自作の関数を含めることもできます。 モジュールに含まれている特定の関数コードを参照するには、function.json 構成ファイル内の `scriptFile` フィールドと `entryPoint` フィールドを使用します。

この場合の `entryPoint` は、`scriptFile` で参照される PowerShell モジュール内の関数またはコマンドレットの名前です。

次のフォルダー構造を考えてみましょう。

```
FunctionApp
 | - host.json
 | - myFunction
 | | - function.json
 | - lib
 | | - PSFunction.psm1
```

この場合、`PSFunction.psm1` の内容は次のとおりです。

```powershell
function Invoke-PSTestFunc {
    param($InputBinding, $TriggerMetadata)

    Push-OutputBinding -Name OutputBinding -Value "output"
}

Export-ModuleMember -Function "Invoke-PSTestFunc"
```

この例の `myFunction` の構成には、`PSFunction.psm1` (別のフォルダーにある PowerShell モジュール) を参照する `scriptFile` プロパティが含まれています。  `entryPoint` プロパティは、このモジュールのエントリ ポイントである `Invoke-PSTestFunc` 関数を参照します。

```json
{
  "scriptFile": "../lib/PSFunction.psm1",
  "entryPoint": "Invoke-PSTestFunc",
  "bindings": [
    // ...
  ]
}
```

この構成では、`run.ps1` の場合とまったく同じように `Invoke-PSTestFunc` が実行されます。

## <a name="considerations-for-powershell-functions"></a>PowerShell 関数に関する注意点

PowerShell 関数を使用するときは、以下のセクションに記載されている事柄に注意してください。

### <a name="cold-start"></a>コールド スタート

[サーバーレス ホスティング モデル](consumption-plan.md)で Azure Functions を開発する際は、コールド スタートを避けて通ることはできません。 "*コールド スタート*" とは、関数アプリの実行が開始されて要求が処理されるまでにかかる時間のことを指します。 従量課金プランでは、非アクティブな期間中に関数アプリがシャットダウンされるため、コールド スタートの発生頻度が高くなります。

### <a name="bundle-modules-instead-of-using-install-module"></a>`Install-Module` を使用せずにモジュールをバンドルする

スクリプトは、呼び出しのたびに実行されます。 スクリプト内で `Install-Module` を使用することは避けてください。 その代わり、発行前に `Save-Module` を使用します。そうすれば、関数がモジュールをダウンロードする際に生じる無駄な時間をなくすことができます。 コールド スタートが関数に影響を及ぼす場合は、"*常にオン*" に設定された [App Service プラン](dedicated-plan.md)、または [Premium プラン](functions-premium-plan.md)に関数アプリをデプロイすることを検討してください。

## <a name="next-steps"></a>次のステップ

詳細については、次のリソースを参照してください。

* [Azure Functions のベスト プラクティス](functions-best-practices.md)
* [Azure Functions 開発者向けリファレンス](functions-reference.md)
* [Azure Functions triggers and bindings (Azure Functions のトリガーとバインド)](functions-triggers-bindings.md)

[host.json のリファレンス]: functions-host-json.md