---
title: Azure Functions のアプリケーション設定のリファレンス
description: Azure Functions のアプリケーション設定または環境変数の参照ドキュメントです。
ms.topic: conceptual
ms.date: 09/22/2018
ms.openlocfilehash: 6fa8e2d9fb2270d53d8c0419ac7b4d88d79f30fd
ms.sourcegitcommit: ba676927b1a8acd7c30708144e201f63ce89021d
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/07/2021
ms.locfileid: "102425704"
---
# <a name="app-settings-reference-for-azure-functions"></a>Azure Functions のアプリケーション設定のリファレンス

関数アプリのアプリケーション設定には、その関数アプリのすべての関数に影響するグローバル構成オプションが含まれています。 ローカルで実行する場合、これらの設定は、ローカルの[環境変数](functions-run-local.md#local-settings-file)としてアクセスされます。 この記事では、関数アプリで使用できるアプリケーション設定の一覧を紹介します。

[!INCLUDE [Function app settings](../../includes/functions-app-settings.md)]

[host.json](functions-host-json.md) ファイルと [local.settings.json](functions-run-local.md#local-settings-file) ファイルには、他のグローバル構成オプションもあります。

> [!NOTE]  
> アプリケーション設定を使用して、host.json ファイル自体を変更することなく、host.json 設定値をオーバーライドできます。 これは、特定の環境の特定の host.json 設定を構成または変更する必要がある場合に便利です。 これにより、プロジェクトを再発行しなくても、host.json 設定を変更できます。 詳細については、[host.json のリファレンスに関する記事](functions-host-json.md#override-hostjson-values)をご覧ください。 関数アプリの設定に変更を加えるためには、関数アプリを再起動する必要があります。

## <a name="appinsights_instrumentationkey"></a>APPINSIGHTS_INSTRUMENTATIONKEY

Application Insights のインストルメンテーション キー。 `APPINSIGHTS_INSTRUMENTATIONKEY` または `APPLICATIONINSIGHTS_CONNECTION_STRING` のいずれかのみを使用してください。 Application Insights がソブリン クラウドで実行されている場合は、`APPLICATIONINSIGHTS_CONNECTION_STRING` を使用します。 詳細については、[Azure Functions で監視を構成する](configure-monitoring.md)方法に関するページを参照してください。 

|Key|値の例|
|---|------------|
|APPINSIGHTS_INSTRUMENTATIONKEY|55555555-af77-484b-9032-64f83bb83bb|

## <a name="applicationinsights_connection_string"></a>APPLICATIONINSIGHTS_CONNECTION_STRING

Application Insights の接続文字列。 次の場合は、`APPINSIGHTS_INSTRUMENTATIONKEY` ではなく `APPLICATIONINSIGHTS_CONNECTION_STRING` を使用します。

+ お使いの関数アプリで接続文字列を使用した追加のカスタマイズ サポートが必要な場合。 
+ カスタム エンドポイントを必要とするソブリン クラウドで Application Insights インスタンスが実行されている場合。

詳細については、[接続文字列](../azure-monitor/app/sdk-connection-string.md)に関するページを参照してください。 

|Key|値の例|
|---|------------|
|APPLICATIONINSIGHTS_CONNECTION_STRING|InstrumentationKey=[key];IngestionEndpoint=[url];LiveEndpoint=[url];ProfilerEndpoint=[url];SnapshotEndpoint=[url];|

## <a name="azure_function_proxy_disable_local_call"></a>AZURE_FUNCTION_PROXY_DISABLE_LOCAL_CALL

既定では、[Functions プロキシ](functions-proxies.md)は、ショートカットを使用して、同じ関数アプリ内の関数にプロキシから直接 API 呼び出しを送信します。 このショートカットは、新しい HTTP 要求を作成する代わりに使用されます。 この設定を使用すると、そのショートカット動作を無効にすることができます。

|Key|[値]|説明|
|-|-|-|
|AZURE_FUNCTION_PROXY_DISABLE_LOCAL_CALL|true|ローカル関数アプリ内の関数を指すバックエンド URL を使用した呼び出しは、その関数に直接送信されません。 代わりに、要求は、関数アプリの HTTP フロントエンドに戻されます。|
|AZURE_FUNCTION_PROXY_DISABLE_LOCAL_CALL|false|ローカル関数アプリ内の関数を指すバックエンド URL を使用した呼び出しは、その関数に直接転送されます。 これが既定値です。 |

## <a name="azure_function_proxy_backend_url_decode_slashes"></a>AZURE_FUNCTION_PROXY_BACKEND_URL_DECODE_SLASHES

この設定は、文字 `%2F` がバックエンド URL に挿入されたときにこれをルート パラメーターでスラッシュとしてデコードするかどうかを制御します。 

|Key|[値]|説明|
|-|-|-|
|AZURE_FUNCTION_PROXY_BACKEND_URL_DECODE_SLASHES|true|エンコードされたスラッシュを含むルート パラメーターがデコードされます。 |
|AZURE_FUNCTION_PROXY_BACKEND_URL_DECODE_SLASHES|false|すべてのルート パラメーターは変更されずに渡されます。これは既定の動作です。 |

たとえば、`myfunction.com` ドメインの関数アプリ用の proxies.json ファイルを考えてみます。

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

`AZURE_FUNCTION_PROXY_BACKEND_URL_DECODE_SLASHES` が `true` に設定されている場合、URL `example.com/api%2ftest` は `example.com/api/test` に解決されます。 既定では、URL は `example.com/test%2fapi` のまま変更されません。 詳細については、[Functions プロキシ](functions-proxies.md)に関するページを参照してください。

## <a name="azure_functions_environment"></a>AZURE_FUNCTIONS_ENVIRONMENT

Functions ランタイムのバージョン 2.x 以降では、ランタイム環境に基づいてアプリの動作を構成します。 この値は初期化時に読み取られ、任意の値に設定できます。 ランタイムによって受け入れられるのは、`Development`、`Staging`、および `Production` の値のみです。 Azure での実行時にこのアプリケーション設定が存在しない場合、環境は `Production` と見なされます。 Azure のランタイム環境を `Production` 以外のものに変更する必要がある場合は、`ASPNETCORE_ENVIRONMENT` の代わりにこの設定を使用します。 ローカル コンピューターで実行している場合は、Azure Functions Core Tools により `AZURE_FUNCTIONS_ENVIRONMENT` が `Development` に設定されます。local.settings.json ファイルでこれをオーバーライドすることはできません。 詳細については、「[環境別の起動のクラスとメソッド](/aspnet/core/fundamentals/environments#environment-based-startup-class-and-methods)」を参照してください。

## <a name="azurefunctionsjobhost__"></a>AzureFunctionsJobHost__\*

Functions ランタイムのバージョン 2.x 以降では、現在の環境の [host.json](functions-host-json.md) 設定をアプリケーション設定でオーバーライドできます。 これらのオーバーライドは、`AzureFunctionsJobHost__path__to__setting` という名前のアプリケーション設定として表されます。 詳細については、「[host.json 値をオーバーライドする](functions-host-json.md#override-hostjson-values)」を参照してください。

## <a name="azurewebjobsdashboard"></a>AzureWebJobsDashboard

ログの保存と、それらをポータルの **[モニター]** タブに表示する、オプションのストレージ アカウントの接続文字列です。 この設定は、Azure Functions ランタイムのバージョン 1.x を対象とするアプリに対してのみ有効です。 このストレージ アカウントは、blob、キュー、およびテーブルをサポートする汎用的なものである必要があります。 詳しくは、「[ストレージ アカウントの要件](storage-considerations.md#storage-account-requirements)」をご覧ください。

|Key|値の例|
|---|------------|
|AzureWebJobsDashboard|DefaultEndpointsProtocol=https;AccountName=<name>;AccountKey=<key>|

> [!NOTE]
> より良いパフォーマンスとエクスペリエンスのために、ランタイムのバージョン 2.x 以降では、`AzureWebJobsDashboard` ではなく APPINSIGHTS_INSTRUMENTATIONKEY と App Insights を使用します。

## <a name="azurewebjobsdisablehomepage"></a>AzureWebJobsDisableHomepage

`true` は、関数アプリのルート URL 用に表示される既定のランディング ページを無効にすることを意味します。 既定値は `false` です。

|Key|値の例|
|---|------------|
|AzureWebJobsDisableHomepage|true|

このアプリ設定を省略するか、`false` に設定した場合、URL `<functionappname>.azurewebsites.net` の応答に対し、次の例のようなものが表示されます。

![関数アプリのランディング ページ](media/functions-app-settings/function-app-landing-page.png)

## <a name="azurewebjobsdotnetreleasecompilation"></a>AzureWebJobsDotNetReleaseCompilation

`true` は、.NET コードのコンパイルにリリース モードを使用することを意味し、`false` は、デバッグ モードを使用することを意味します。 既定値は `true` です。

|Key|値の例|
|---|------------|
|AzureWebJobsDotNetReleaseCompilation|true|

## <a name="azurewebjobsfeatureflags"></a>AzureWebJobsFeatureFlags

有効にするベータ機能のコンマ区切りの一覧です。 これらのフラグで有効となるベータ機能は本番には適しませんが、公開前の実験的な使用には有効にすることができます。

|Key|値の例|
|---|------------|
|AzureWebJobsFeatureFlags|feature1,feature2|

## <a name="azurewebjobssecretstoragetype"></a>AzureWebJobsSecretStorageType

キーの保存に使用するリポジトリまたはプロバイダーを指定します。 現時点でサポートされているリポジトリは、BLOB ストレージ ("Blob") およびローカル ファイル システム ("Files") です。 既定では、バージョン 2 では BLOB、バージョン 1 ではファイル システムが使用されます。

|Key|値の例|
|---|------------|
|AzureWebJobsSecretStorageType|ファイル|

## <a name="azurewebjobsstorage"></a>AzureWebJobsStorage

Azure Functions ランタイムでは、このストレージ アカウント接続文字列は通常の操作に使用されます。 このストレージ アカウントの使用方法としては、キー管理、タイマー トリガー管理、Event Hubs チェックポイントなどがあります。 このストレージ アカウントは、blob、キュー、およびテーブルをサポートする汎用的なものである必要があります。 「[ストレージ アカウント](functions-infrastructure-as-code.md#storage-account)」および「[ストレージ アカウントの要件](storage-considerations.md#storage-account-requirements)」を参照してください。

|Key|値の例|
|---|------------|
|AzureWebJobsStorage|DefaultEndpointsProtocol=https;AccountName=[name];AccountKey=[key]|

## <a name="azurewebjobs_typescriptpath"></a>AzureWebJobs_TypeScriptPath

Typescript で使用されるコンパイラへのパスです。 必要に応じて、既定値はオーバーライドできます。

|Key|値の例|
|---|------------|
|AzureWebJobs_TypeScriptPath|%HOME%\typescript|

## <a name="function_app_edit_mode"></a>FUNCTION\_APP\_EDIT\_MODE

Azure portal での編集が有効になっているかどうかを決定します。 有効な値は "readwrite" および "readonly" です。

|Key|値の例|
|---|------------|
|FUNCTION\_APP\_EDIT\_MODE|readonly|

## <a name="functions_extension_version"></a>FUNCTIONS\_EXTENSION\_VERSION

関数アプリをホストする Functions ランタイムのバージョンです。 メジャー バージョンのチルダ (`~`) は、そのメジャー バージョンの最新バージョンを使用することを意味します (例: "~3")。 同じメジャー バージョンの新しいバージョンが使用できる場合、それらは関数アプリに自動的にインストールされています。 特定のバージョンにアプリを固定するには、完全なバージョン番号 (例: "3.0.12345") を使用します。 既定値は "~3" です。 `~1` の値は、アプリをバージョン 1.x のランタイムに固定します。 詳細については、「[Azure Functions ランタイム バージョンをターゲットにする方法](functions-versions.md)」をご覧ください。

|Key|値の例|
|---|------------|
|FUNCTIONS\_EXTENSION\_VERSION|~3|

## <a name="functions_v2_compatibility_mode"></a>FUNCTIONS\_V2\_COMPATIBILITY\_MODE

この設定により、関数アプリはバージョン 3.x ランタイムでバージョン 2.x 互換モードで実行できるようになります。 この設定は、[関数アプリをランタイムのバージョン 2.x から 3.x にアップグレードする](functions-versions.md#migrating-from-2x-to-3x)ときに問題が発生した場合にのみ使用してください。 

>[!IMPORTANT]
> この設定は、アプリケーションをバージョン 3.x で正常に動作するように更新するときに、短期的な回避策としてのみ使用することを目的としています。 [2.x ランタイムがサポートされている](functions-versions.md)限り、この設定はサポートされます。 この設定を使用せずにバージョン 3.x でアプリを実行できない問題が発生した場合は、[問題を報告](https://github.com/Azure/azure-functions-host/issues/new?template=Bug_report.md)してください。

[FUNCTIONS\_EXTENSION\_VERSION](functions-app-settings.md#functions_extension_version) を `~3` に設定する必要があります。

|Key|値の例|
|---|------------|
|FUNCTIONS\_V2\_COMPATIBILITY\_MODE|true|

## <a name="functions_worker_process_count"></a>FUNCTIONS\_WORKER\_PROCESS\_COUNT

言語ワーカー プロセスの最大数を指定します。既定値は `1` です。 許容される最大値は `10` です。 関数呼び出しは、言語ワーカー プロセス間で均等に分散されます。 言語ワーカー プロセスは、FUNCTIONS\_WORKER\_PROCESS\_COUNT によって設定されたカウントに達するまで、10 秒ごとに生成されます。 複数の言語ワーカー プロセスの使用は、[スケーリング](functions-scale.md)と同じではありません。 CPU にバインドされた呼び出しと I/O にバインドされた呼び出しがワークロードに混在している場合は、この設定を使用することを検討してください。 この設定はすべての非 .NET 言語に適用されます。

|Key|値の例|
|---|------------|
|FUNCTIONS\_WORKER\_PROCESS\_COUNT|2|

## <a name="python_threadpool_thread_count"></a>PYTHON\_THREADPOOL\_THREAD\_COUNT

関数呼び出しを実行するために Python 言語ワーカーによって使用されるスレッドの最大数を指定します。Python バージョン `3.8` 以前では、既定値 `1` を使用します。 Python バージョン `3.9` 以降では、値は `None` に設定されます。 この設定は、実行中に設定されるスレッドの数を保証するものではないことに注意してください。 この設定により、Python では、スレッドの数を指定された値に増やすことができます。 この設定は、Python 関数アプリにのみ適用されます。 また、この設定は、コルーチンではなく、同期関数の呼び出しに適用されます。

|Key|値の例|最大値|
|---|------------|---------|
|PYTHON\_THREADPOOL\_THREAD\_COUNT|2|32|


## <a name="functions_worker_runtime"></a>FUNCTIONS\_WORKER\_RUNTIME

ワーカー ランタイムが関数アプリに読み込む言語。  これは、アプリケーションで使用されている言語に対応します (たとえば、"dotnet")。 関数の言語が混在する場合、関数ごとに対応するワーカー ランタイム値を設定して、関数を複数のアプリに公開する必要があります。  有効な値は、`dotnet` (C#/F#)、`node` (JavaScript/TypeScript)、`java` (Java)、`powershell` (PowerShell)、`python` (Python) です。

|Key|値の例|
|---|------------|
|FUNCTIONS\_WORKER\_RUNTIME|dotnet|

## <a name="pip_extra_index_url"></a>PIP\_EXTRA\_INDEX\_URL

この設定の値は、Python アプリのカスタム パッケージ インデックス URL を示します。 この設定は、追加のパッケージ インデックスにあるカスタム依存関係を使用してリモート ビルドを実行する必要がある場合に使用します。   

|Key|値の例|
|---|------------|
|PIP\_EXTRA\_INDEX\_URL|http://my.custom.package.repo/simple |

詳細については、Python 開発者リファレンスの「[カスタムの依存関係](functions-reference-python.md#remote-build-with-extra-index-url)」を参照してください。

## <a name="scale_controller_logging_enabled"></a>SCALE\_CONTROLLER\_LOGGING\_ENABLED

"_この設定は現在プレビューの段階です。_ "  

この設定は、Azure Functions スケール コントローラーからのログ記録を制御します。 詳細については、[スケール コントローラーのログ](functions-monitoring.md#scale-controller-logs)に関するセクションを参照してください。

|Key|値の例|
|-|-|
|SCALE_CONTROLLER_LOGGING_ENABLED|AppInsights:Verbose|

このキーの値は `<DESTINATION>:<VERBOSITY>` の形式で指定されます。これは次のように定義されます。

[!INCLUDE [functions-scale-controller-logging](../../includes/functions-scale-controller-logging.md)]

## <a name="website_contentazurefileconnectionstring"></a>WEBSITE\_CONTENTAZUREFILECONNECTIONSTRING

Windows 上で実行されているイベント ドリブン スケーリング プランに関数アプリのコードと構成が格納されているストレージ アカウントの接続文字列です。 詳細については、「[Function App を作成する](functions-infrastructure-as-code.md#windows)」を参照してください。

|Key|値の例|
|---|------------|
|WEBSITE_CONTENTAZUREFILECONNECTIONSTRING|DefaultEndpointsProtocol=https;AccountName=[name];AccountKey=[key]|

Windows 上で実行されている Premium プランまたは従量課金プランにデプロイする場合にのみ使用されます。 Linux を実行する従量課金プランではサポートされていません。 この設定を変更または削除すると、関数アプリが起動しなくなることがあります。 詳細については、[こちらのトラブルシューティング記事](functions-recover-storage-account.md#storage-account-application-settings-were-deleted)を参照してください。 

## <a name="website_contentovervnet"></a>WEBSITE\_CONTENTOVERVNET

Premium プランのみ。 `1` の値を指定すると、ストレージ アカウントを仮想ネットワークに制限している場合に、関数アプリをスケーリングできます。 ストレージ アカウントを仮想ネットワークに制限する場合は、この設定を有効にする必要があります。 詳細については、「[ストレージ アカウントを仮想ネットワークに制限する](functions-networking-options.md#restrict-your-storage-account-to-a-virtual-network)」を参照してください。

|Key|値の例|
|---|------------|
|WEBSITE_CONTENTOVERVNET|1|

## <a name="website_contentshare"></a>WEBSITE\_CONTENTSHARE

Windows 上のイベント ドリブン スケーリング プラン内の関数アプリ コードと構成へのファイル パス。 WEBSITE_CONTENTAZUREFILECONNECTIONSTRING と共に使用されます。 既定は、関数アプリ名で始まる一意文字列です。 「[Function App を作成する](functions-infrastructure-as-code.md#windows)」を参照してください。

|Key|値の例|
|---|------------|
|WEBSITE_CONTENTSHARE|functionapp091999e2|

Windows 上で実行されている Premium プランまたは従量課金プランにデプロイする場合にのみ使用されます。 Linux を実行する従量課金プランではサポートされていません。 この設定を変更または削除すると、関数アプリが起動しなくなることがあります。 詳細については、[こちらのトラブルシューティング記事](functions-recover-storage-account.md#storage-account-application-settings-were-deleted)を参照してください。

デプロイ中、Azure Resource Manager を使用して関数アプリを作成するとき、テンプレートに WEBSITE_CONTENTSHARE を含めないでください。 このアプリケーション設定はデプロイ中に生成されます。 詳細については、[関数アプリのリソース デプロイを自動化する](functions-infrastructure-as-code.md#windows)方法に関するページを参照してください。   

## <a name="website_max_dynamic_application_scale_out"></a>WEBSITE\_MAX\_DYNAMIC\_APPLICATION\_SCALE\_OUT

関数アプリがスケールアウトできる最大のインスタンス数です。 既定は無制限です。

> [!IMPORTANT]
> この設定は、プレビューの段階です。  スケールアウトの制限に推奨される、[関数で最大にスケールアウトするためのアプリ プロパティ](./event-driven-scaling.md#limit-scale-out)が追加されています。

|Key|値の例|
|---|------------|
|WEBSITE\_MAX\_DYNAMIC\_APPLICATION\_SCALE\_OUT|5|

## <a name="website_node_default_version"></a>WEBSITE\_NODE\_DEFAULT_VERSION

"_Windows のみ_"。  
Windows で関数アプリを実行するときに使用する Node.js のバージョンを設定します。 チルダ (~) を使用して、ランタイムがターゲット メジャー バージョンの利用可能な最新バージョンを使用するようにする必要があります。 たとえば、`~10` に設定すると、最新バージョンの Node.js 10 が使用されます。 メジャー バージョンがチルダ付きで対象になっている場合は、マイナー バージョンを手動で更新する必要はありません。 

|Key|値の例|
|---|------------|
|WEBSITE\_NODE\_DEFAULT_VERSION|~10|

## <a name="website_run_from_package"></a>WEBSITE\_RUN\_FROM\_PACKAGE

マウントされたパッケージ ファイルから関数アプリを実行できるようにします。

|Key|値の例|
|---|------------|
|WEBSITE\_RUN\_FROM\_PACKAGE|1|

有効な値は、展開パッケージ ファイルの場所に解決される URL、または `1` です。 `1` に設定した場合、パッケージは `d:\home\data\SitePackages` フォルダーに存在する必要があります。 この設定で zip デプロイを使用すると、パッケージは自動的にこの場所にアップロードされます。 プレビューでは、この設定は `WEBSITE_RUN_FROM_ZIP` という名前でした。 詳細については、[パッケージ ファイルからの関数の実行](run-functions-from-deployment-package.md)に関するページを参照してください。

## <a name="website_time_zone"></a>WEBSITE\_TIME\_ZONE

関数アプリのタイムゾーンを設定できます。 

|Key|OS|値の例|
|---|--|------------|
|WEBSITE\_TIME\_ZONE|Windows|東部標準時|
|WEBSITE\_TIME\_ZONE|Linux|America/New_York|

[!INCLUDE [functions-timezone](../../includes/functions-timezone.md)]

## <a name="next-steps"></a>次のステップ

[アプリケーション設定の更新方法](functions-how-to-use-azure-function-app-settings.md#settings)

[host.json ファイルのグローバル設定を参照する](functions-host-json.md)

[App Service アプリの他のアプリ設定を参照する](https://github.com/projectkudu/kudu/wiki/Configurable-settings)
