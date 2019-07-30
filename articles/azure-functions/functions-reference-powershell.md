---
title: Azure Functions の PowerShell 開発者向けリファレンス
description: PowerShell を使用して関数を開発する方法について説明します。
services: functions
documentationcenter: na
author: tylerleonhardt
manager: jeconnoc
ms.service: azure-functions
ms.devlang: powershell
ms.topic: conceptual
ms.date: 04/22/2019
ms.author: tyleonha
ms.reviewer: glenga
ms.openlocfilehash: a75bdaf0e26193a5b2792b52923c085eff89b83f
ms.sourcegitcommit: c105ccb7cfae6ee87f50f099a1c035623a2e239b
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/09/2019
ms.locfileid: "67706394"
---
# <a name="azure-functions-powershell-developer-guide"></a>Azure Functions の PowerShell 開発者向けガイド

この記事では、PowerShell を使用して Azure Functions を作成する方法について詳しく説明します。

[!INCLUDE [functions-powershell-preview-note](../../includes/functions-powershell-preview-note.md)]

PowerShell Azure 関数 (関数) は、トリガーされた時点で実行される PowerShell スクリプトとして表されます。 それぞれの関数スクリプトには、関連する `function.json` ファイルが存在し、関数の動作 (トリガー方法や入力および出力パラメーターなど) が定義されています。 詳細については、[トリガーとバインディングに関する記事](functions-triggers-bindings.md)を参照してください。 

他の種類の関数と同様、PowerShell スクリプト関数も、`function.json` ファイルで定義されているすべての入力バインディングの名前に対応したパラメーターを受け入れます。 また、関数を開始したトリガーに関する追加情報を含む `TriggerMetadata` パラメーターも渡されます。

この記事では、「[Azure Functions の開発者向けガイド](functions-reference.md)」を既に読んでいることを前提としています。 また、[Functions の PowerShell 向けクイックスタート](functions-create-first-function-powershell.md)で、初めての PowerShell 関数を作成しておく必要があります。

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

特定のバインディングには、`extensions.csproj` ファイルが必要になります。 Functions ランタイムの[バージョン 2.x](functions-versions.md) に必要なバインディング拡張機能は `extensions.csproj` ファイルで定義されており、実際のライブラリ ファイルは `bin` フォルダーにあります。 ローカルで開発する場合は、[バインド拡張機能を登録する](functions-bindings-register.md#extension-bundles)必要があります。 Azure portal 上で関数を開発するときに、この登録が実行されます。

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

| プロパティ   | Description                                     | Type     |
|------------|-------------------------------------------------|----------|
| UtcNow     | 関数がトリガーされた日時 (UTC)        | DateTime |
| MethodName | トリガーされた関数の名前     | string   |
| RandGuid   | この関数の実行に対して一意の GUID | string   |

トリガーの種類ごとに、異なる一連のメタデータがあります。 たとえば、`QueueTrigger` の `$TriggerMetadata` には、数ある中でも `InsertionTime`、`Id`、`DequeueCount` が格納されます。 キュー トリガーのメタデータの詳細については、[キュー トリガーの公式ドキュメント](functions-bindings-storage-queue.md#trigger---message-metadata)を参照してください。 実際に使用している[トリガー](functions-triggers-bindings.md)のドキュメントを確認すると、そのトリガーのメタデータに含まれるものがわかります。

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

| EnableAdfsAuthentication | Type | 位置 | 説明 |
| ---- | ---- |  -------- | ----------- |
| **`-Name`** | string | 1 | 設定する出力バインディングの名前。 |
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

詳細については、「[About CommonParameters (共通パラメーターについて)](https://go.microsoft.com/fwlink/?LinkID=113216)」を参照してください。

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

`Push-OutputBinding` は、出力バインディング ([Azure Queue storage 出力バインディング](functions-bindings-storage-queue.md#output)など) にデータを送信するために使用されます。 次の例では、キューに書き込まれるメッセージの値は "output #1" です。

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

## <a name="logging"></a>ログの記録

PowerShell 関数におけるログは、通常の PowerShell のログと同様に機能します。 各出力ストリームへの書き込みには、ログ コマンドレットを使用できます。 各コマンドレットは、Functions で使用されるログ レベルに対応します。

| Functions のログ レベル | ログ コマンドレット |
| ------------- | -------------- |
| Error | **`Write-Error`** |
| 警告 | **`Write-Warning`**  | 
| Information | **`Write-Information`** <br/> **`Write-Host`** <br /> **`Write-Output`**      | Information | "_情報_" レベル ログへの書き込み |
| デバッグ | **`Write-Debug`** |
| Trace | **`Write-Progress`** <br /> **`Write-Verbose`** |

これらのコマンドレットに加え、パイプラインに書き込まれた内容はすべて `Information` ログ レベルにリダイレクトされ、PowerShell の既定の書式で表示されます。

> [!IMPORTANT]
> `Write-Verbose` または `Write-Debug` コマンドレットを使用するだけでは、詳細およびデバッグ レベルのログを表示するには不十分です。 ログ レベルのしきい値も構成して、実際に注目するログのレベルを宣言する必要があります。 詳細については、「[関数アプリのログ レベルの構成](#configure-the-function-app-log-level)」を参照してください。

### <a name="configure-the-function-app-log-level"></a>関数アプリのログ レベルの構成

Azure Functions では、しきい値レベルを定義することで、関数によるログへの書き込み方法を簡単に制御することができます。 コンソールに書き込まれるすべてのトレースのしきい値を設定するには、[`host.json` ファイル][host.json]の `logging.logLevel.default` プロパティを使用します。 この設定は、関数アプリのすべての関数に適用されます。

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
* int
* double
* HttpRequestContext
* HttpResponseContext

この一覧の最初の 5 つの型は、標準 .NET 型です。 最後の 2 つは、[HttpTrigger トリガー](#http-triggers-and-bindings)のみで使用されます。

関数に含まれる各バインディング パラメーターは、これらの型のいずれかにする必要があります。

### <a name="http-triggers-and-bindings"></a>HTTP トリガーとバインディング

HTTP、webhook トリガー、および HTTP 出力バインディングでは、要求オブジェクトと応答オブジェクトを使用して HTTP メッセージングを表します。

#### <a name="request-object"></a>要求オブジェクト

スクリプトに渡される要求オブジェクトは `HttpRequestContext` 型で、次のプロパティを持ちます。

| プロパティ  | Description                                                    | Type                      |
|-----------|----------------------------------------------------------------|---------------------------|
| **`Body`**    | 要求の本文を格納するオブジェクト。 `Body` は、データに基づいて最適な型にシリアル化されます。 たとえば、データが JSON である場合はハッシュテーブルとして渡されます。 データが文字列の場合は文字列として渡されます。 | object |
| **`Headers`** | 要求ヘッダーを格納するディクショナリ。                | Dictionary<string,string><sup>*</sup> |
| **`Method`** | 要求の HTTP メソッド。                                | string                    |
| **`Params`**  | 要求のルーティング パラメーターを格納するオブジェクト。 | Dictionary<string,string><sup>*</sup> |
| **`Query`** | クエリ パラメーターを格納するオブジェクト。                  | Dictionary<string,string><sup>*</sup> |
| **`Url`** | 要求の URL。                                        | string                    |

<sup>*</sup> `Dictionary<string,string>` のキーはすべて、大文字と小文字が区別されません。

#### <a name="response-object"></a>応答オブジェクト

返すべき応答オブジェクトは `HttpResponseContext` 型で、次のプロパティを持ちます。

| プロパティ      | Description                                                 | Type                      |
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

PowerShell 関数では、関数アプリの起動時にプロファイル スクリプトが実行されます。 関数アプリが起動するのは、最初にデプロイされたときとアイドル状態になった後です ([コールド スタート](#cold-start))。

Visual Studio Code や Azure Functions Core Tools などのツールを使用して関数アプリを作成すると、既定の `profile.ps1` が自動的に作成されます。 既定のプロファイルは [Core Tools GitHub リポジトリ](https://github.com/Azure/azure-functions-core-tools/blob/dev/src/Azure.Functions.Cli/StaticResources/profile.ps1)で保持されており、次の機能を備えています。

* Azure に対する自動 MSI 認証。
* 必要に応じて Azure PowerShell `AzureRM` PowerShell エイリアスを有効にする機能。

## <a name="powershell-version"></a>PowerShell バージョン

次の表では、Functions Runtime のメジャー バージョンごとに使用される PowerShell バージョンを示しています。

| Functions バージョン | PowerShell バージョン                             |
|-------------------|------------------------------------------------|
| 1.x               | Windows PowerShell 5.1 (ランタイムによりロック) |
| 2.x               | PowerShell Core 6                              |

現在のバージョンを確認するには、任意の関数から `$PSVersionTable` を出力します。

## <a name="dependency-management"></a>依存関係の管理

PowerShell 関数では、サービスによる Azure モジュールの管理をサポートしています。 host.json を変更し、managedDependency の enabled プロパティを true に設定すると、requirements.psd1 ファイルが処理されるようになります。 最新の Azure モジュールが自動的にダウンロードされて、関数で使用できるようになります。

host.json
```json
{
    "managedDependency": {
        "enabled": true
    }
}
```

requirements.psd1

```powershell
@{
    Az = '1.*'
}
```

独自のカスタム モジュールまたは [PowerShell ギャラリー](https://powershellgallery.com)のモジュールを活用する方法が通常の方法とは若干異なります。

ローカル コンピューターにモジュールをインストールすると、`$env:PSModulePath` にあるグローバルに利用できるフォルダーのいずれかにそのモジュールが格納されます。 関数は Azure で実行されるため、ローカル コンピューターにインストールされたモジュールにアクセスできなくなります。 そのため、PowerShell 関数アプリの `$env:PSModulePath` は、通常の PowerShell スクリプトの `$env:PSModulePath` とは異なるようにする必要があります。

Functions では、`PSModulePath` に次の 2 つのパスが存在します。

* 関数アプリのルートに存在する `Modules` フォルダー。
* PowerShell 言語ワーカー内に存在する `Modules` フォルダーのパス。

### <a name="function-app-level-modules-folder"></a>関数アプリレベルの `Modules` フォルダー

カスタム モジュールまたは PowerShell ギャラリーの PowerShell モジュールを使用するために、関数の依存モジュールを `Modules` フォルダーに配置できます。 このフォルダーにあるモジュールは、自動的に Functions Runtime から利用できる状態になります。 関数アプリ内のどの関数でもこれらのモジュールを使用できます。

この機能を活用するには、関数アプリのルートに `Modules` フォルダーを作成します。 自分の関数で使用したいモジュールをこの場所に保存します。

```powershell
mkdir ./Modules
Save-Module MyGalleryModule -Path ./Modules
```

関数で使用するすべてのモジュールを `Save-Module` を使用して保存するか、独自のカスタム モジュールを `Modules` フォルダーにコピーします。 Modules フォルダーでは、関数アプリのフォルダー構造が次のようになっている必要があります。

```
PSFunctionApp
 | - MyFunction
 | | - run.ps1
 | | - function.json
 | - Modules
 | | - MyGalleryModule
 | | - MyOtherGalleryModule
 | | - MyCustomModule.psm1
 | - local.settings.json
 | - host.json
```

関数アプリを起動すると、PowerShell 言語ワーカーによりこの `Modules` フォルダーが `$env:PSModulePath` に追加されるため、通常の PowerShell スクリプトと同様に、モジュールの自動読み込みを利用できます。

### <a name="language-worker-level-modules-folder"></a>言語ワーカー レベルの `Modules` フォルダー

いくつかのモジュールは、PowerShell 言語ワーカーによってよく使用されます。 これらのモジュールは、`PSModulePath` の最後の位置で定義されます。 

現在のモジュールの一覧は次のとおりです。

* [Microsoft.PowerShell.Archive](https://www.powershellgallery.com/packages/Microsoft.PowerShell.Archive): アーカイブ (`.zip`、`.nupkg` など) に使用されるモジュール。
* **ThreadJob**: PowerShell ジョブ API のスレッドベースの実装です。

Functions では、これらのモジュールの最新のバージョンが使用されます。 これらのモジュールの特定のバージョンを使用するには、関数アプリの `Modules` フォルダーにその特定のバージョンのモジュールを格納してください。

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

この動作を変更するには、次の環境変数を整数値に設定します。

```
PSWorkerInProcConcurrencyUpperBound
```

この環境変数は、関数アプリの[アプリ設定](functions-app-settings.md)で設定します。

### <a name="considerations-for-using-concurrency"></a>コンカレンシーの使用に関する注意点

PowerShell は、既定では "_シングル スレッド_" のスクリプト言語です。 ただし、同じプロセス内で複数の PowerShell 実行空間を使用することで、コンカレンシーを追加できます。 この機能は、Azure Functions PowerShell ランタイムの動作です。

このアプローチにはいくつかの欠点があります。

#### <a name="concurrency-is-only-as-good-as-the-machine-its-running-on"></a>コンカレンシーは、実行環境となるマシンに依存します。

シングル コアのみをサポートしている [App Service プラン](functions-scale.md#app-service-plan) で関数アプリが実行されている場合、コンカレンシーはあまり役立ちません。 なぜなら、負荷の分散に役立つ追加のコアが存在しないためです。 この場合、実行空間と実行空間との間のコンテキスト切り替えをシングル コアで行わなければならないとパフォーマンスが変動する可能性があります。

[従量課金プラン](functions-scale.md#consumption-plan)の運用に使用されるコアは 1 つだけであるため、コンカレンシーを活用することはできません。 コンカレンシーを十分に活用したい場合は、十分なコア数を備えた専用 App Service プランで実行されている関数アプリに関数をデプロイしてください。

#### <a name="azure-powershell-state"></a>Azure PowerShell の状態

開発者の入力の手間を軽減するために、Azure PowerShell では、"_プロセスレベル_" のコンテキストと状態が使用されています。 ただし、関数アプリでコンカレンシーを有効にし、状態の変更を伴うアクションを呼び出した場合は、最終的に競合状態に陥る可能性があります。 このような競合状態をデバッグするのは困難です。なぜなら、一方の呼び出しが特定の状態に依存しているのに、もう一方の呼び出しがその状態を変更したためです。

一部の操作にはかなりの時間がかかる可能性があるため、Azure PowerShell のコンカレンシーには非常に大きな価値があります。 しかし、実際に使用する場合には注意が必要です。 競合状態の発生が疑われる場合は、コンカレンシーの設定を `1` に戻してから、再度要求を試みてください。

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

[サーバーレス ホスティング モデル](functions-scale.md#consumption-plan)で Azure Functions を開発する際は、コールド スタートを避けて通ることはできません。 "*コールド スタート*" とは、関数アプリの実行が開始されて要求が処理されるまでにかかる時間のことを指します。 従量課金プランでは、非アクティブな期間中に関数アプリがシャットダウンされるため、コールド スタートの発生頻度が高くなります。

### <a name="bundle-modules-instead-of-using-install-module"></a>`Install-Module` を使用せずにモジュールをバンドルする

スクリプトは、呼び出しのたびに実行されます。 スクリプト内で `Install-Module` を使用することは避けてください。 その代わり、発行前に `Save-Module` を使用します。そうすれば、関数がモジュールをダウンロードする際に生じる無駄な時間をなくすことができます。 コールド スタートが関数に影響を及ぼす場合は、"*常にオン*" に設定された [App Service プラン](functions-scale.md#app-service-plan)、または [Premium プラン](functions-scale.md#premium-plan)に関数アプリをデプロイすることを検討してください。

## <a name="next-steps"></a>次の手順

詳細については、次のリソースを参照してください。

* [Azure Functions のベスト プラクティス](functions-best-practices.md)
* [Azure Functions 開発者向けリファレンス](functions-reference.md)
* [Azure Functions triggers and bindings (Azure Functions のトリガーとバインド)](functions-triggers-bindings.md)

[host.json]: functions-host-json.md
