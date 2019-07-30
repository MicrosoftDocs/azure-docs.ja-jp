---
title: Azure Functions のアプリケーション設定のリファレンス
description: Azure Functions のアプリケーション設定または環境変数の参照ドキュメントです。
services: functions
author: ggailey777
manager: jeconnoc
keywords: ''
ms.service: azure-functions
ms.devlang: multiple
ms.topic: conceptual
ms.date: 09/22/2018
ms.author: glenga
ms.openlocfilehash: 62d359494050b188869d51d1e3975c823b9c0a76
ms.sourcegitcommit: b7a44709a0f82974578126f25abee27399f0887f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/18/2019
ms.locfileid: "67204934"
---
# <a name="app-settings-reference-for-azure-functions"></a>Azure Functions のアプリケーション設定のリファレンス

関数アプリのアプリケーション設定には、その関数アプリのすべての関数に影響するグローバル構成オプションが含まれています。 ローカルで実行する場合、これらの設定は、ローカルの[環境変数](functions-run-local.md#local-settings-file)としてアクセスされます。 この記事では、関数アプリで使用できるアプリケーション設定の一覧を紹介します。

[!INCLUDE [Function app settings](../../includes/functions-app-settings.md)]

[host.json](functions-host-json.md) ファイルと [local.settings.json](functions-run-local.md#local-settings-file) ファイルには、他のグローバル構成オプションもあります。

## <a name="appinsightsinstrumentationkey"></a>APPINSIGHTS_INSTRUMENTATIONKEY

Application Insights を使用している場合の Application Insights インストルメンテーション キーです。 「[Azure Functions を監視する](functions-monitoring.md)」を参照してください。

|キー|値の例|
|---|------------|
|APPINSIGHTS_INSTRUMENTATIONKEY|5dbdd5e9-af77-484b-9032-64f83bb83bb|

## <a name="azurefunctionsenvironment"></a>AZURE_FUNCTIONS_ENVIRONMENT

Functions ランタイムのバージョン 2.x では、ランタイム環境に基づいてアプリの動作を構成します。 この値は、[初期化中に読み取られます](https://github.com/Azure/azure-functions-host/blob/dev/src/WebJobs.Script.WebHost/Program.cs#L43)。 `AZURE_FUNCTIONS_ENVIRONMENT` は任意の値に設定することができますが、次の [3 つの値](/dotnet/api/microsoft.aspnetcore.hosting.environmentname)がサポートされています。[Development](/dotnet/api/microsoft.aspnetcore.hosting.environmentname.development)、[Staging](/dotnet/api/microsoft.aspnetcore.hosting.environmentname.staging)、[Production](/dotnet/api/microsoft.aspnetcore.hosting.environmentname.production)。 `AZURE_FUNCTIONS_ENVIRONMENT` が設定されていない場合、既定値は `Production` です。 この設定は、ランタイム環境を設定するために、`ASPNETCORE_ENVIRONMENT` の代わりに使用する必要があります。 

## <a name="azurewebjobsdashboard"></a>AzureWebJobsDashboard

ログの保存と、それらをポータルの **[モニター]** タブに表示する、オプションのストレージ アカウントの接続文字列です。 このストレージ アカウントは、blob、キュー、およびテーブルをサポートする汎用的なものである必要があります。 「[ストレージ アカウント](functions-infrastructure-as-code.md#storage-account)」および「[ストレージ アカウントの要件](functions-create-function-app-portal.md#storage-account-requirements)」を参照してください。

|キー|値の例|
|---|------------|
|AzureWebJobsDashboard|DefaultEndpointsProtocol=https;AccountName=[name];AccountKey=[key]|

> [!TIP]
> パフォーマンスとエクスペリエンスのために、AzureWebJobsDashboard ではなく APPINSIGHTS_INSTRUMENTATIONKEY と App Insights を使用して監視することをお勧めします。

## <a name="azurewebjobsdisablehomepage"></a>AzureWebJobsDisableHomepage

`true` は、関数アプリのルート URL 用に表示される既定のランディング ページを無効にすることを意味します。 既定値は `false` です。

|キー|値の例|
|---|------------|
|AzureWebJobsDisableHomepage|true|

このアプリ設定を省略するか、`false` に設定した場合、URL `<functionappname>.azurewebsites.net` の応答に対し、次の例のようなものが表示されます。

![関数アプリのランディング ページ](media/functions-app-settings/function-app-landing-page.png)

## <a name="azurewebjobsdotnetreleasecompilation"></a>AzureWebJobsDotNetReleaseCompilation

`true` は、.NET コードのコンパイルにリリース モードを使用することを意味し、`false` は、デバッグ モードを使用することを意味します。 既定値は `true` です。

|キー|値の例|
|---|------------|
|AzureWebJobsDotNetReleaseCompilation|true|

## <a name="azurewebjobsfeatureflags"></a>AzureWebJobsFeatureFlags

有効にするベータ機能のコンマ区切りの一覧です。 これらのフラグで有効となるベータ機能は本番には適しませんが、公開前の実験的な使用には有効にすることができます。

|キー|値の例|
|---|------------|
|AzureWebJobsFeatureFlags|feature1,feature2|

## <a name="azurewebjobssecretstoragetype"></a>AzureWebJobsSecretStorageType

キーの保存に使用するリポジトリまたはプロバイダーを指定します。 現時点でサポートされているリポジトリは、BLOB ストレージ ("Blob") およびローカル ファイル システム ("Files") です。 既定では、バージョン 2 では BLOB、バージョン 1 ではファイル システムが使用されます。

|キー|値の例|
|---|------------|
|AzureWebJobsSecretStorageType|ファイル|

## <a name="azurewebjobsstorage"></a>AzureWebJobsStorage

Azure Functions ランタイムは、HTTP によってトリガーされる関数を除くすべての関数で、このストレージ アカウントの接続文字列を使用します。 このストレージ アカウントは、blob、キュー、およびテーブルをサポートする汎用的なものである必要があります。 「[ストレージ アカウント](functions-infrastructure-as-code.md#storage-account)」および「[ストレージ アカウントの要件](functions-create-function-app-portal.md#storage-account-requirements)」を参照してください。

|キー|値の例|
|---|------------|
|AzureWebJobsStorage|DefaultEndpointsProtocol=https;AccountName=[name];AccountKey=[key]|

## <a name="azurewebjobstypescriptpath"></a>AzureWebJobs_TypeScriptPath

Typescript で使用されるコンパイラへのパスです。 必要に応じて、既定値はオーバーライドできます。

|キー|値の例|
|---|------------|
|AzureWebJobs_TypeScriptPath|%HOME%\typescript|

## <a name="functionappeditmode"></a>FUNCTION\_APP\_EDIT\_MODE

Azure portal での編集が有効になっているかどうかを決定します。 有効な値は "readwrite" および "readonly" です。

|キー|値の例|
|---|------------|
|FUNCTION\_APP\_EDIT\_MODE|readonly|

## <a name="functionsextensionversion"></a>FUNCTIONS\_EXTENSION\_VERSION

この関数アプリで使用する Functions ランタイムのバージョンです。 メジャー バージョンのチルダ (例: "~2") は、そのメジャー バージョンの最新バージョンを使用することを意味します。 同じメジャー バージョンの新しいバージョンが使用できる場合、それらは関数アプリに自動的にインストールされています。 特定のバージョンにアプリを固定するには、完全なバージョン番号 (例: "2.0.12345") を使用します。 既定値は "~2" です。 `~1` の値は、アプリをバージョン 1.x のランタイムに固定します。

|キー|値の例|
|---|------------|
|FUNCTIONS\_EXTENSION\_VERSION|~2|

## <a name="functionsworkerruntime"></a>FUNCTIONS\_WORKER\_RUNTIME

ワーカー ランタイムが関数アプリに読み込む言語。  これは、アプリケーションで使用されている言語に対応します (たとえば、"dotnet")。 関数の言語が混在する場合、関数ごとに対応するワーカー ランタイム値を設定して、関数を複数のアプリに公開する必要があります。  有効な値は、`dotnet` (C#/F#)、`node` (JavaScript/TypeScript)、`java` (Java)、`powershell` (PowerShell)、`python` (Python) です。

|キー|値の例|
|---|------------|
|FUNCTIONS\_WORKER\_RUNTIME|dotnet|

## <a name="websitecontentazurefileconnectionstring"></a>WEBSITE_CONTENTAZUREFILECONNECTIONSTRING

従量課金プラン用のみです。 関数アプリのコードと構成が格納されているストレージ アカウントの接続文字列です。 「[Function App を作成する](functions-infrastructure-as-code.md#create-a-function-app)」を参照してください。

|キー|値の例|
|---|------------|
|WEBSITE_CONTENTAZUREFILECONNECTIONSTRING|DefaultEndpointsProtocol=https;AccountName=[name];AccountKey=[key]|

## <a name="websitecontentshare"></a>WEBSITE\_CONTENTSHARE

従量課金プラン用のみです。 関数アプリ コードと構成へのファイル パスです。 WEBSITE_CONTENTAZUREFILECONNECTIONSTRING と共に使用されます。 既定は、関数アプリ名で始まる一意文字列です。 「[Function App を作成する](functions-infrastructure-as-code.md#create-a-function-app)」を参照してください。

|キー|値の例|
|---|------------|
|WEBSITE_CONTENTSHARE|functionapp091999e2|

## <a name="websitemaxdynamicapplicationscaleout"></a>WEBSITE\_MAX\_DYNAMIC\_APPLICATION\_SCALE\_OUT

関数アプリがスケールアウトできる最大のインスタンス数です。 既定は無制限です。

> [!NOTE]
> この設定は、プレビュー機能です。5 以下の値を設定した場合にのみ、信頼して使用できます。

|キー|値の例|
|---|------------|
|WEBSITE\_MAX\_DYNAMIC\_APPLICATION\_SCALE\_OUT|5|

## <a name="websitenodedefaultversion"></a>WEBSITE\_NODE\_DEFAULT_VERSION

既定値は "8.11.1" です。

|キー|値の例|
|---|------------|
|WEBSITE\_NODE\_DEFAULT_VERSION|8.11.1|

## <a name="websiterunfrompackage"></a>WEBSITE\_RUN\_FROM\_PACKAGE

マウントされたパッケージ ファイルから関数アプリを実行できるようにします。

|キー|値の例|
|---|------------|
|WEBSITE\_RUN\_FROM\_PACKAGE|1|

有効な値は、展開パッケージ ファイルの場所に解決される URL、または `1` です。 `1` に設定した場合、パッケージは `d:\home\data\SitePackages` フォルダーに存在する必要があります。 この設定で zip デプロイを使用すると、パッケージは自動的にこの場所にアップロードされます。 プレビューでは、この設定は `WEBSITE_RUN_FROM_ZIP` という名前でした。 詳細については、[パッケージ ファイルからの関数の実行](run-functions-from-deployment-package.md)に関するページを参照してください。

## <a name="azurefunctionproxydisablelocalcall"></a>AZURE_FUNCTION_PROXY_DISABLE_LOCAL_CALL

既定では、Functions プロキシは新しい HTTP 要求を作成するのではなく、同じ Function App 内の関数にプロキシから直接 API 呼び出しを送信するためのショートカットを使用します。 この設定を使用すると、その動作を無効にすることができます。

|キー|値|説明|
|-|-|-|
|AZURE_FUNCTION_PROXY_DISABLE_LOCAL_CALL|true|ローカル Function App 内の関数を指すバックエンド URL を使用した呼び出しは、関数に直接送信されるのではなく、Function App の HTTP フロント エンドに戻されるようになります|
|AZURE_FUNCTION_PROXY_DISABLE_LOCAL_CALL|false|これが既定値です。 ローカル Function App 内の関数を指すバックエンド URL を使用した呼び出しは、その関数に直接転送されるようになります|


## <a name="azurefunctionproxybackendurldecodeslashes"></a>AZURE_FUNCTION_PROXY_BACKEND_URL_DECODE_SLASHES

この設定は、スラッシュがバックエンド URL に挿入されたときに、%2F をルート パラメーターでスラッシュとしてデコードするかどうかを制御します。 

|キー|値|説明|
|-|-|-|
|AZURE_FUNCTION_PROXY_BACKEND_URL_DECODE_SLASHES|true|エンコードされたスラッシュがルート パラメーターに含まれている場合、それらがデコードされます。 `example.com/api%2ftest` は `example.com/api/test` になります。|
|AZURE_FUNCTION_PROXY_BACKEND_URL_DECODE_SLASHES|false|これは既定の動作です。 すべてのルート パラメーターが、変更されることなく渡されます|

### <a name="example"></a>例

次に示すのは、URL myfunction.com にある関数アプリ内の proxies.json の例です

```JSON
{
    "$schema": "http://json.schemastore.org/proxies",
    "proxies": {
        "root": {
            "matchCondition": {
                "route": "/{*all}"
            },
            "backendUri": "example.com/{all}"
        }
    }
}
```
|URL のデコード|入力|Output|
|-|-|-|
|true|myfunction.com/test%2fapi|example.com/test/api
|false|myfunction.com/test%2fapi|example.com/test%2fapi|


## <a name="next-steps"></a>次の手順

[アプリケーション設定の更新方法](functions-how-to-use-azure-function-app-settings.md#settings)

[host.json ファイルのグローバル設定を参照する](functions-host-json.md)

[App Service アプリの他のアプリ設定を参照する](https://github.com/projectkudu/kudu/wiki/Configurable-settings)
