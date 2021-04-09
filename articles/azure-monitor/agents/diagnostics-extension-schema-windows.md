---
title: Windows Diagnostics 拡張機能のスキーマ
description: Azure Monitor の Windows Diagnostics 拡張機能 (WAD) の構成スキーマ リファレンス。
ms.topic: reference
author: bwren
ms.author: bwren
ms.date: 01/20/2020
ms.openlocfilehash: 70b27fec07d074dadb413d1debb098e23b4d33b3
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "102428730"
---
# <a name="windows-diagnostics-extension-schema"></a>Windows Diagnostics 拡張機能のスキーマ
Azure Diagnostics 拡張機能は Azure Monitor のエージェントで、ゲスト オペレーティング システムと Azure コンピューティング リソースのワークロードから監視データを収集します。 この記事では、Windows 仮想マシンおよびその他のコンピューティング リソースで Diagnostics 拡張機能を構成するために使用するスキーマについて詳細に説明します。

> [!NOTE]
> この記事で説明するスキーマは、バージョン 1.3 以降 (Azure SDK 2.4 以降) で有効です。 新しい構成セクションには、追加されたバージョンがコメントで示されています。 スキーマのバージョン 1.0 と 1.2 は、アーカイブされ、使用できなくなりました。 

## <a name="public-configuration-file-schema"></a>パブリック構成ファイルのスキーマ

次の PowerShell コマンドを実行して、パブリック構成ファイルのスキーマ定義をダウンロードします。  

```powershell  
(Get-AzureServiceAvailableExtension -ExtensionName 'PaaSDiagnostics' -ProviderNamespace 'Microsoft.Azure.Diagnostics').PublicConfigurationSchema | Out-File –Encoding utf8 -FilePath 'C:\temp\WadConfig.xsd'  
```  


## <a name="common-attribute-types"></a>一般的な属性の型  
 **scheduledTransferPeriod** 属性は、複数の要素に表示されます。 ストレージへのスケジュールされている転送の間隔です。最も近い分単位に切り上げられます。 値は [XML "Duration Data Type"](https://www.w3schools.com/xml/schema_dtypes_date.asp) です。


## <a name="diagnosticsconfiguration-element"></a>DiagnosticsConfiguration 要素  
 *ツリー: ルート - DiagnosticsConfiguration*

バージョン 1.3 で追加。  

診断構成ファイルの最上位要素。  

**属性**  xmlns - 診断構成ファイルの XML 名前空間は次のとおりです。  
`http://schemas.microsoft.com/ServiceHosting/2010/10/DiagnosticsConfiguration`


|子要素|説明|  
|--------------------|-----------------|  
|**PublicConfig**|必須。 このページの他の場所の説明を参照してください。|  
|**PrivateConfig**|省略可能。 このページの他の場所の説明を参照してください。|  
|**IsEnabled**|Boolean です。 このページの他の場所の説明を参照してください。|  

## <a name="publicconfig-element"></a>PublicConfig 要素  
 *ツリー: ルート - DiagnosticsConfiguration - PublicConfig*

 パブリック診断構成について説明します。  

|子要素|説明|  
|--------------------|-----------------|  
|**WadCfg**|必須。 このページの他の場所の説明を参照してください。|  
|**StorageAccount**|データを格納する Azure ストレージ アカウントの名前。 Set-AzureServiceDiagnosticsExtension コマンドレットを実行するときに、パラメーターとして指定することもできます。|  
|**StorageType**|*Table*、*Blob*、または *TableAndBlob* を指定できます。 既定値は Table です。 TableAndBlob を選択すると、種類ごとに 1 回、つまり合計 2 回、診断データが書き込まれます。|  
|**LocalResourceDirectory**|監視エージェントがイベント データを保存する仮想マシンのディレクトリ。 設定しない場合は、既定のディレクトリが使用されます。<br /><br /> worker/Web ロールの場合: `C:\Resources\<guid>\directory\<guid>.<RoleName.DiagnosticStore\`<br /><br /> 仮想マシンの場合: `C:\WindowsAzure\Logs\Plugins\Microsoft.Azure.Diagnostics.IaaSDiagnostics\<WADVersion>\WAD<WADVersion>`<br /><br /> 必須属性は次のとおりです。<br /><br /> - **path** - Azure Diagnostics で使用するシステム上のディレクトリ。<br /><br /> - **expandEnvironment** - 環境変数をパス名で展開するかどうかを制御します。|  

## <a name="wadcfg-element"></a>WadCFG 要素  
 *ツリー: ルート - DiagnosticsConfiguration - PublicConfig - WadCFG*

 収集するテレメトリ データを特定して構成します。  


## <a name="diagnosticmonitorconfiguration-element"></a>DiagnosticMonitorConfiguration 要素
 *ツリー: ルート - DiagnosticsConfiguration - PublicConfig - WadCFG - DiagnosticMonitorConfiguration*

 必須

|属性|説明|  
|----------------|-----------------|  
| **overallQuotaInMB** | Azure Diagnostics によって収集された、さまざまな種類の診断データで使用できるローカル ディスク領域の最大量。 既定の設定は 4096 MB です。<br />
|**useProxyServer** | IE 設定で設定したプロキシ サーバー設定を使用するように Azure Diagnostics を構成します。|
|**sinks** | 1\.5 で追加されました。 省略可能。 シンクの場所を指定すると共に、シンクをサポートするすべての子要素の診断データを送信します。 シンクの例に、Application Insights または Event Hubs があります。 なお、Event Hubs にアップロードするイベントにリソース ID を含めるには、*Metrics* 要素の下に *resourceId* プロパティを追加します。 |  


<br /> <br />

|子要素|説明|  
|--------------------|-----------------|  
|**CrashDumps**|このページの他の場所の説明を参照してください。|  
|**DiagnosticInfrastructureLogs**|Azure Diagnostics によって生成されたログの収集を有効にします。 診断インフラストラクチャ ログは、診断システム自体のトラブルシューティングに役に立ちます。 オプションの属性は次のとおりです。<br /><br /> - **scheduledTransferLogLevelFilter** - 収集されたログの最小重大度レベルを構成します。<br /><br /> - **scheduledTransferPeriod** - ストレージへのスケジュールされている転送の間隔。最も近い分単位に切り上げられます。 値は [XML "Duration Data Type"](https://www.w3schools.com/xml/schema_dtypes_date.asp) です。 |  
|**Directories**|このページの他の場所の説明を参照してください。|  
|**EtwProviders**|このページの他の場所の説明を参照してください。|  
|**Metrics**|このページの他の場所の説明を参照してください。|  
|**PerformanceCounters**|このページの他の場所の説明を参照してください。|  
|**WindowsEventLog**|このページの他の場所の説明を参照してください。|
|**DockerSources**|このページの他の場所の説明を参照してください。 |



## <a name="crashdumps-element"></a>CrashDumps 要素  
 *ツリー: ルート - DiagnosticsConfiguration - PublicConfig - WadCFG - DiagnosticMonitorConfiguration - CrashDumps*

 クラッシュ ダンプの収集を有効にします。  

|属性|説明|  
|----------------|-----------------|  
|**containerName**|省略可能。 クラッシュ ダンプの保存に使用する Azure ストレージ アカウント内の BLOB コンテナーの名前。|  
|**crashDumpType**|省略可能。  Azure Diagnostics を、小さいクラッシュ ダンプまたはフル クラッシュ ダンプを収集するように構成します。|  
|**directoryQuotaPercentage**|省略可能。  VM でのクラッシュ ダンプ用に予約する **overallQuotaInMB** の割合を構成します。|  

|子要素|説明|  
|--------------------|-----------------|  
|**CrashDumpConfiguration**|必須。 各プロセスの構成値を定義します。<br /><br /> 次の属性も必須です。<br /><br /> **processName** - Azure Diagnostics でクラッシュ ダンプを収集するプロセスの名前。|  

## <a name="directories-element"></a>Directories 要素
 *ツリー: ルート - DiagnosticsConfiguration - PublicConfig - WadCFG - DiagnosticMonitorConfiguration - Directories*

 ディレクトリ、IIS 失敗アクセス要求ログ、IIS ログのコンテンツの収集を有効にします。  

 オプションの **scheduledTransferPeriod** 属性。 前の説明を参照してください。  

|子要素|説明|  
|--------------------|-----------------|  
|**IISLogs**|この要素を構成に含めることで、IIS ログの収集を有効にします。<br /><br /> **containerName** - IIS ログの保存に使用する Azure ストレージ アカウント内の BLOB コンテナーの名前。|   
|**FailedRequestLogs**|この要素を構成に含めることで、IIS サイトまたはアプリケーションへの失敗要求に関するログの収集を有効にします。 また、**Web.config** の **system.WebServer** でトレース オプションを有効にする必要もあります。|  
|**DataSources**|監視するディレクトリの一覧。|




## <a name="datasources-element"></a>DataSources 要素  
 *ツリー: ルート - DiagnosticsConfiguration - PublicConfig - WadCFG - DiagnosticMonitorConfiguration - Directories - DataSources*

 監視するディレクトリの一覧。  

|子要素|説明|  
|--------------------|-----------------|  
|**DirectoryConfiguration**|必須。 必須属性:<br /><br /> **containerName** - ログ ファイルの保存に使用する Azure ストレージ アカウント内の BLOB コンテナーの名前。|  





## <a name="directoryconfiguration-element"></a>DirectoryConfiguration 要素  
 *ツリー: ルート - DiagnosticsConfiguration - PublicConfig - WadCFG - DiagnosticMonitorConfiguration - Directories - DataSources - DirectoryConfiguration*

 **Absolute** 要素または **LocalResource** 要素のいずれかを含めることができます。ただし、両方を含めることができません。  

|子要素|説明|  
|--------------------|-----------------|  
|**Absolute**|監視するディレクトリの絶対パス。 次の属性は必須です。<br /><br /> - **Path** - 監視するディレクトリの絶対パス。<br /><br /> - **expandEnvironment** - Path で環境変数を展開するかどうかを構成します。|  
|**LocalResource**|監視するローカル リソースの相対パス。 必須属性は次のとおりです。<br /><br /> - **Name** - 監視するディレクトリを含むローカル リソース<br /><br /> - **relativePath** - 監視するディレクトリを含む名前の相対パス|  



## <a name="etwproviders-element"></a>EtwProviders 要素  
 *ツリー: ルート - DiagnosticsConfiguration - PublicConfig - WadCFG - DiagnosticMonitorConfiguration - EtwProviders*

 EventSource や ETW マニフェスト ベースのプロバイダーからの ETW イベントの収集を構成します。  

|子要素|説明|  
|--------------------|-----------------|  
|**EtwEventSourceProviderConfiguration**|[EventSource クラス](/dotnet/api/system.diagnostics.tracing.eventsource)から生成されたイベントの収集を構成します。 必須属性:<br /><br /> **provider** - EventSource イベントのクラス名。<br /><br /> オプションの属性は次のとおりです。<br /><br /> - **scheduledTransferLogLevelFilter** - ストレージ アカウントへの転送の最小重大度レベル。<br /><br /> - **scheduledTransferPeriod** - ストレージへのスケジュールされている転送の間隔。最も近い分単位に切り上げられます。 値は [XML "Duration Data Type"](https://www.w3schools.com/xml/schema_dtypes_date.asp) です。 |  
|**EtwManifestProviderConfiguration**|必須属性:<br /><br /> **provider** - イベント プロバイダーの GUID<br /><br /> オプションの属性は次のとおりです。<br /><br /> - **scheduledTransferLogLevelFilter** - ストレージ アカウントへの転送の最小重大度レベル。<br /><br /> - **scheduledTransferPeriod** - ストレージへのスケジュールされている転送の間隔。最も近い分単位に切り上げられます。 値は [XML "Duration Data Type"](https://www.w3schools.com/xml/schema_dtypes_date.asp) です。 |  



## <a name="etweventsourceproviderconfiguration-element"></a>EtwEventSourceProviderConfiguration 要素  
 *ツリー: ルート - DiagnosticsConfiguration - PublicConfig - WadCFG - DiagnosticMonitorConfiguration - EtwProviders- EtwEventSourceProviderConfiguration*

 [EventSource クラス](/dotnet/api/system.diagnostics.tracing.eventsource)から生成されたイベントの収集を構成します。  

|子要素|説明|  
|--------------------|-----------------|  
|**DefaultEvents**|オプションの属性:<br/><br/> **eventDestination** - イベントを保存するテーブルの名前|  
|**Event**|必須属性:<br /><br /> **id** - イベントの ID。<br /><br /> オプションの属性:<br /><br /> **eventDestination** - イベントを保存するテーブルの名前|  



## <a name="etwmanifestproviderconfiguration-element"></a>EtwManifestProviderConfiguration 要素  
 *ツリー: ルート - DiagnosticsConfiguration - PublicConfig - WadCFG - DiagnosticMonitorConfiguration - EtwProviders - EtwManifestProviderConfiguration*

|子要素|説明|  
|--------------------|-----------------|  
|**DefaultEvents**|オプションの属性:<br /><br /> **eventDestination** - イベントを保存するテーブルの名前|  
|**Event**|必須属性:<br /><br /> **id** - イベントの ID。<br /><br /> オプションの属性:<br /><br /> **eventDestination** - イベントを保存するテーブルの名前|  



## <a name="metrics-element"></a>Metrics 要素  
 *ツリー: ルート - DiagnosticsConfiguration - PublicConfig - WadCFG - DiagnosticMonitorConfiguration - Metrics*

 高速クエリ用に最適化されたパフォーマンス カウンター テーブルを生成できます。 **PerformanceCounters** 要素で定義された各パフォーマンス カウンターが、パフォーマンス カウンター テーブルだけでなくメトリック テーブルにも保存されます。  

 **resourceId** 属性は必須です。  Azure Diagnostics のデプロイ先仮想マシンまたは仮想マシン スケール セットのリソース ID です。 [Azure Portal](https://portal.azure.com) から **resourceID** を取得します。 **[参照]**  ->  **[リソース グループ]**  ->  **<名前\>** の順に選択します。 **[プロパティ]** タイルをクリックし、 **[ID]** フィールドの値をコピーします。  この resourceID プロパティは、カスタム メトリックの送信と、Event Hubs に送信するデータへの resourceID プロパティの追加の両方に使用します。 なお、Event Hubs にアップロードするイベントにリソース ID を含めるには、*Metrics* 要素の下に *resourceId* プロパティを追加します。

|子要素|説明|  
|--------------------|-----------------|  
|**MetricAggregation**|必須属性:<br /><br /> **scheduledTransferPeriod** - ストレージへのスケジュールされている転送の間隔。最も近い分単位に切り上げられます。 値は [XML "Duration Data Type"](https://www.w3schools.com/xml/schema_dtypes_date.asp) です。 |  



## <a name="performancecounters-element"></a>PerformanceCounters 要素  
 *ツリー: ルート - DiagnosticsConfiguration - PublicConfig - WadCFG - DiagnosticMonitorConfiguration - PerformanceCounters*

 パフォーマンス カウンターの収集を有効にします。  

 オプションの属性:  

 オプションの **scheduledTransferPeriod** 属性。 前の説明を参照してください。

|子要素|説明|  
|-------------------|-----------------|  
|**PerformanceCounterConfiguration**|次の属性は必須です。<br /><br /> - **counterSpecifier** - パフォーマンス カウンターの名前。 たとえば、「 `\Processor(_Total)\% Processor Time` 」のように入力します。 ホストでカウンター パフォーマンスの一覧を取得するには、`typeperf` コマンドを実行します。<br /><br /> - **sampleRate** - カウンターをサンプリングする頻度。<br /><br /> オプションの属性:<br /><br /> **unit** - カウンターの測定単位。 値は、[UnitType クラス](/dotnet/api/microsoft.azure.management.sql.models.unittype)で使用できます。 |
|**sinks** | 1\.5 で追加されました。 省略可能。 sink の場所を指定して、診断データも送信します。 たとえば、Azure Monitor や Event Hubs です。 なお、Event Hubs にアップロードするイベントにリソース ID を含めるには、*Metrics* 要素の下に *resourceId* プロパティを追加します。|    




## <a name="windowseventlog-element"></a>WindowsEventLog 要素
 *ツリー: ルート - DiagnosticsConfiguration - PublicConfig - WadCFG - DiagnosticMonitorConfiguration - WindowsEventLog*

 Windows イベント ログの収集を有効にします。  

 オプションの **scheduledTransferPeriod** 属性。 前の説明を参照してください。  

|子要素|説明|  
|-------------------|-----------------|  
|**DataSource**|収集する Windows イベント ログ。 必須属性:<br /><br /> **name** - 収集する Windows イベントについて説明する XPath クエリ。 次に例を示します。<br /><br /> `Application!*[System[(Level <=3)]], System!*[System[(Level <=3)]], System!*[System[Provider[@Name='Microsoft Antimalware']]], Security!*[System[(Level <= 3)]`<br /><br /> すべてのイベントを収集するには、"*" を指定します。 |
|**sinks** | 1\.5 で追加されました。 省略可能。 シンクの場所を指定すると共に、シンクをサポートするすべての子要素の診断データを送信します。 シンクの例に、Application Insights または Event Hubs があります。|  


## <a name="logs-element"></a>Logs 要素  
 *ツリー: ルート - DiagnosticsConfiguration - PublicConfig - WadCFG - DiagnosticMonitorConfiguration - Logs*

 バージョン 1.0 および 1.1 に存在します。 1\.2 にはありません。 1\.3 で再び追加されています。  

 基本的な Azure ログのバッファー構成を定義します。  

|属性|Type|説明|  
|---------------|----------|-----------------|  
|**bufferQuotaInMB**|**unsignedInt**|省略可能。 指定されたデータに使用できるファイル システム ストレージの最大量を指定します。<br /><br /> 既定値は 0 です。|  
|**scheduledTransferLogLevelFilter**|**string**|省略可能。 転送されるログ エントリの最小重大度レベルを指定します。 既定値は **Undefined** で、すべてのログを転送します。 他の有効値は、(情報量が多いものから順に) **Verbose**、**Information**、**Warning**、**Error**、**Critical** となります。|  
|**scheduledTransferPeriod**|**duration**|省略可能。 最も近い分単位の値に丸められた、スケジュールされているデータ転送の間隔を指定します。<br /><br /> 既定値は PT0S です。|  
|**sinks** |**string**| 1\.5 で追加されました。 省略可能。 sink の場所を指定して、診断データも送信します。 たとえば、Application Insights または Event Hubs があります。 なお、Event Hubs にアップロードするイベントにリソース ID を含めるには、*Metrics* 要素の下に *resourceId* プロパティを追加します。|  

## <a name="dockersources"></a>DockerSources
 *ツリー: ルート - DiagnosticsConfiguration - PublicConfig - WadCFG - DiagnosticMonitorConfiguration - DockerSources*

 1\.9 で追加。

|要素名|説明|  
|------------------|-----------------|  
|**Stats**|Docker コンテナーの統計情報を収集するようにシステムに通知します。|  

## <a name="sinksconfig-element"></a>SinksConfig 要素  
 *ツリー: ルート - DiagnosticsConfiguration - PublicConfig - WadCFG - SinksConfig*

 診断データの送信先の一覧と、その場所に関連付けられている構成。  

|要素名|説明|  
|------------------|-----------------|  
|**Sink**|このページの他の場所の説明を参照してください。|  

## <a name="sink-element"></a>Sink 要素
 *ツリー: ルート - DiagnosticsConfiguration - PublicConfig - WadCFG - SinksConfig - Sink*

 バージョン 1.5 で追加。  

 診断データの送信先を定義します。 Application Insights サービスなど。  

|属性|Type|説明|  
|---------------|----------|-----------------|  
|**name**|string|シンク名を特定する文字列。|  

|要素|Type|説明|  
|-------------|----------|-----------------|  
|**Application Insights**|string|データを Application Insights に送信するときにのみ使用されます。 アクセス先のアクティブな Application Insights アカウントのインストルメンテーション キーが含まれます。|  
|**Channels**|string|追加フィルタリングごとに 1 つ|  

## <a name="channels-element"></a>Channels 要素  
 *ツリー: ルート - DiagnosticsConfiguration - PublicConfig - WadCFG - SinksConfig - Sink - Channels*

 バージョン 1.5 で追加。  

 シンクを通過するログ データのストリームのフィルターを定義します。  

|要素|Type|説明|  
|-------------|----------|-----------------|  
|**Channel**|string|このページの他の場所の説明を参照してください。|  

## <a name="channel-element"></a>Channel 要素
 *ツリー: ルート - DiagnosticsConfiguration - PublicConfig - WadCFG - SinksConfig - Sink - Channels - Channel*

 バージョン 1.5 で追加。  

 診断データの送信先を定義します。 Application Insights サービスなど。  

|属性|Type|説明|  
|----------------|----------|-----------------|  
|**logLevel**|**string**|転送されるログ エントリの最小重大度レベルを指定します。 既定値は **Undefined** で、すべてのログを転送します。 他の有効値は、(情報量が多いものから順に) **Verbose**、**Information**、**Warning**、**Error**、**Critical** となります。|  
|**name**|**string**|参照するチャネルの一意の名前|  


## <a name="privateconfig-element"></a>PrivateConfig 要素
 *ツリー: ルート - DiagnosticsConfiguration - PrivateConfig*

 バージョン 1.3 で追加。  

 省略可能  

 ストレージ アカウントのプライベート詳細 (名前、キー、およびエンドポイント) を保存します。 この情報は仮想マシンに送信されますが、その仮想マシンから取得することはできません。  

|子要素|説明|  
|--------------------|-----------------|  
|**StorageAccount**|使用するストレージ アカウント。 次の属性は必須です<br /><br /> - **name** - ストレージ アカウントの名前。<br /><br /> - **key** - ストレージ アカウントへのキー。<br /><br /> - **endpoint** - ストレージ アカウントにアクセスするためのエンドポイント。 <br /><br /> -**sasToken** (1.8.1 で追加) - ストレージ アカウント キーの代わりに SAS トークンをプライベート構成に指定できます。指定した場合、ストレージ アカウント キーは無視されます。 <br />SAS トークンの要件: <br />- アカウントの SAS トークンのみをサポートします。 <br />- *b* *t* のサービスの種類が必要です。 <br /> - *a* *c* *u* *w* のアクセス許可が必要です。 <br /> - *c* *o* のリソースの種類が必要です。 <br /> - HTTPS プロトコルのみをサポートします。 <br /> - 開始時刻と有効期限を有効にする必要があります。|  


## <a name="isenabled-element"></a>IsEnabled 要素  
 *ツリー: ルート - DiagnosticsConfiguration - IsEnabled*

 Boolean です。 `true` を使用して診断を有効にするか、`false` を使用して診断を無効にします。

## <a name="example-configuration"></a>構成例
 JSON と XML の両方の場合について Windows 用 Diagnostics 拡張機能の詳細な構成サンプルを次に示します。

 
### <a name="json"></a>JSON

JSON のユース ケースでは、ほとんどの場合、*PublicConfig* と *PrivateConfig* は異なる変数として渡されるため、これらを分けて示しています。 このようなケースとしては、Resource Manager テンプレート、PowerShell、Visual Studio があります。

> [!NOTE]
> パブリック構成の Azure Monitor シンクの定義には、*resourceId* と *region* の 2 つのプロパティがあります。 これらは、クラシック VM と従来の Cloud Services のみに必要です。 *region* プロパティは、他のリソースには使用できません。ARM VM には、*resourceId* プロパティを使用して、Event Hubs にアップロードされるログの resourceID フィールドを入力します。

```json
"PublicConfig" {
    "WadCfg": {
        "DiagnosticMonitorConfiguration": {
            "overallQuotaInMB": 10000,
            "DiagnosticInfrastructureLogs": {
                "scheduledTransferLogLevelFilter": "Error"
            },
            "PerformanceCounters": {
                "scheduledTransferPeriod": "PT1M",
                "sinks": "AzureMonitorSink",
                "PerformanceCounterConfiguration": [
                    {
                        "counterSpecifier": "\\Processor(_Total)\\% Processor Time",
                        "sampleRate": "PT1M",
                        "unit": "percent"
                    }
                ]
            },
            "Directories": {
                "scheduledTransferPeriod": "PT5M",
                "IISLogs": {
                    "containerName": "iislogs"
                },
                "FailedRequestLogs": {
                    "containerName": "iisfailed"
                },
                "DataSources": [
                    {
                        "containerName": "mynewprocess",
                        "Absolute": {
                            "path": "C:\\MyNewProcess",
                            "expandEnvironment": false
                        }
                    },
                    {
                        "containerName": "badapp",
                        "Absolute": {
                            "path": "%SYSTEMDRIVE%\\BadApp",
                            "expandEnvironment": true
                        }
                    },
                    {
                        "containerName": "goodapp",
                        "LocalResource": {
                            "relativePath": "..\\PeanutButter",
                            "name": "Skippy"
                        }
                    }
                ]
            },
            "EtwProviders": {
                "sinks": "",
                "EtwEventSourceProviderConfiguration": [
                    {
                        "scheduledTransferPeriod": "PT5M",
                        "provider": "MyProviderClass",
                        "Event": [
                            {
                                "id": 0
                            },
                            {
                                "id": 1,
                                "eventDestination": "errorTable"
                            }
                        ],
                        "DefaultEvents": {
                        }
                    }
                ],
                "EtwManifestProviderConfiguration": [
                    {
                        "scheduledTransferPeriod": "PT2M",
                        "scheduledTransferLogLevelFilter": "Information",
                        "provider": "5974b00b-84c2-44bc-9e58-3a2451b4e3ad",
                        "Event": [
                            {
                                "id": 0
                            }
                        ],
                        "DefaultEvents": {
                        }
                    }
                ]
            },
            "WindowsEventLog": {
                "scheduledTransferPeriod": "PT5M",
                "DataSource": [
                    {
                        "name": "System!*[System[Provider[@Name='Microsoft Antimalware']]]"
                    },
                    {
                        "name": "System!*[System[Provider[@Name='NTFS'] and (EventID=55)]]"
                    },
                    {
                        "name": "System!*[System[Provider[@Name='disk'] and (EventID=7 or EventID=52 or EventID=55)]]"
                    }
                ]
            },
            "Logs": {
                "scheduledTransferPeriod": "PT1M",
                "scheduledTransferLogLevelFilter": "Verbose",
                "sinks": "ApplicationInsights.AppLogs"
            },
            "CrashDumps": {
                "directoryQuotaPercentage": 30,
                "dumpType": "Mini",
                "containerName": "wad-crashdumps",
                "CrashDumpConfiguration": [
                    {
                        "processName": "mynewprocess.exe"
                    },
                    {
                        "processName": "badapp.exe"
                    }
                ]
            }
        },
        "SinksConfig": {
            "Sink": [
                {
                    "name": "AzureMonitorSink",
                    "AzureMonitor":
                    {
                        "ResourceId": "{insert resourceId if a classic VM or cloud service, else property not needed}",
                        "Region": "{insert Azure region of resource if a classic VM or cloud service, else property not needed}"
                    }
                },
                {
                    "name": "ApplicationInsights",
                    "ApplicationInsights": "{Insert InstrumentationKey}",
                    "Channels": {
                        "Channel": [
                            {
                                "logLevel": "Error",
                                "name": "Errors"
                            },
                            {
                                "logLevel": "Verbose",
                                "name": "AppLogs"
                            }
                        ]
                    }
                },
                {
                    "name": "EventHub",
                    "EventHub": {
                        "Url": "https://myeventhub-ns.servicebus.windows.net/diageventhub",
                        "SharedAccessKeyName": "SendRule",
                        "usePublisherId": false
                    }
                },
                {
                    "name": "secondaryEventHub",
                    "EventHub": {
                        "Url": "https://myeventhub-ns.servicebus.windows.net/secondarydiageventhub",
                        "SharedAccessKeyName": "SendRule",
                        "usePublisherId": false
                    }
                },
                {
                    "name": "secondaryStorageAccount",
                    "StorageAccount": {
                        "name": "secondarydiagstorageaccount",
                        "endpoint": "https://core.windows.net"
                    }
                }
            ]
        }
    },
    "StorageAccount": "diagstorageaccount",
    "StorageType": "TableAndBlob"
}
```

> [!NOTE]
> プライベート構成の Azure Monitor シンクの定義には、*PrincipalId* と *Secret* の 2 つのプロパティがあります。 これらは、クラシック VM と従来の Cloud Services のみに必要です。 これらのプロパティを他のリソースに使用することはできません。


```json
"PrivateConfig" {
    "storageAccountName": "diagstorageaccount",
    "storageAccountKey": "{base64 encoded key}",
    "storageAccountEndPoint": "https://core.windows.net",
    "storageAccountSasToken": "{sas token}",
    "EventHub": {
        "Url": "https://myeventhub-ns.servicebus.windows.net/diageventhub",
        "SharedAccessKeyName": "SendRule",
        "SharedAccessKey": "{base64 encoded key}"
    },
    "AzureMonitorAccount": {
        "ServicePrincipalMeta": {
            "PrincipalId": "{Insert service principal client Id}",
            "Secret": "{Insert service principal client secret}"
        }
    },
    "SecondaryStorageAccounts": {
        "StorageAccount": [
            {
                "name": "secondarydiagstorageaccount",
                "key": "{base64 encoded key}",
                "endpoint": "https://core.windows.net",
                "sasToken": "{sas token}"
            }
        ]
    },
    "SecondaryEventHubs": {
        "EventHub": [
            {
                "Url": "https://myeventhub-ns.servicebus.windows.net/secondarydiageventhub",
                "SharedAccessKeyName": "SendRule",
                "SharedAccessKey": "{base64 encoded key}"
            }
        ]
    }
}

```

### <a name="xml"></a>XML

```xml  
<?xml version="1.0" encoding="utf-8"?>  
<DiagnosticsConfiguration  xmlns="http://schemas.microsoft.com/ServiceHosting/2010/10/DiagnosticsConfiguration">   
  <PublicConfig>  
    <WadCfg>  
      <DiagnosticMonitorConfiguration overallQuotaInMB="10000">  

        <PerformanceCounters scheduledTransferPeriod="PT1M" sinks="AzureMonitorSink">  
          <PerformanceCounterConfiguration counterSpecifier="\Processor(_Total)\% Processor Time" sampleRate="PT1M" unit="percent" />  
        </PerformanceCounters>  

        <Directories scheduledTransferPeriod="PT5M">  
          <IISLogs containerName="iislogs" />  
          <FailedRequestLogs containerName="iisfailed" />  

          <DataSources>  
            <DirectoryConfiguration containerName="mynewprocess">  
              <Absolute path="C:\MyNewProcess" expandEnvironment="false" />  
            </DirectoryConfiguration>  
            <DirectoryConfiguration containerName="badapp">  
              <Absolute path="%SYSTEMDRIVE%\BadApp" expandEnvironment="true" />  
            </DirectoryConfiguration>  
            <DirectoryConfiguration containerName="goodapp">  
              <LocalResource name="Skippy" relativePath="..\PeanutButter"/>  
            </DirectoryConfiguration>  
          </DataSources>  

        </Directories>  

        <EtwProviders>  
          <EtwEventSourceProviderConfiguration   
                       provider="MyProviderClass"   
                       scheduledTransferPeriod="PT5M">  
            <Event id="0"/>  
            <Event id="1" eventDestination="errorTable"/>  
            <DefaultEvents />  
          </EtwEventSourceProviderConfiguration>  
          <EtwManifestProviderConfiguration provider="5974b00b-84c2-44bc-9e58-3a2451b4e3ad" scheduledTransferLogLevelFilter="Information" scheduledTransferPeriod="PT2M">  
            <Event id="0"/>  
            <DefaultEvents eventDestination="defaultTable"/>  
          </EtwManifestProviderConfiguration>  
        </EtwProviders>  

        <WindowsEventLog scheduledTransferPeriod="PT5M">  
          <DataSource name="System!*[System[Provider[@Name='Microsoft Antimalware']]]"/>  
          <DataSource name="System!*[System[Provider[@Name='NTFS'] and (EventID=55)]]" />  
          <DataSource name="System!*[System[Provider[@Name='disk'] and (EventID=7 or EventID=52 or EventID=55)]]" />  
        </WindowsEventLog>  

        <Logs  bufferQuotaInMB="1024"   
             scheduledTransferPeriod="PT1M"   
             scheduledTransferLogLevelFilter="Verbose"   
             sinks="ApplicationInsights.AppLogs"/>  <!-- sinks attribute added in 1.5 -->  

        <CrashDumps containerName="wad-crashdumps" directoryQuotaPercentage="30" dumpType="Mini">  
          <CrashDumpConfiguration processName="mynewprocess.exe" />  
          <CrashDumpConfiguration processName="badapp.exe"/>  
        </CrashDumps>  

        <DockerSources> <!-- Added in 1.9 -->
          <Stats enabled="true" sampleRate="PT1M" scheduledTransferPeriod="PT1M" />
        </DockerSources>

      </DiagnosticMonitorConfiguration>  

      <SinksConfig>   <!-- Added in 1.5 -->  
        <Sink name="AzureMonitorSink">
            <AzureMonitor> <!-- Added in 1.11 -->
                <resourceId>{insert resourceId}</ResourceId> <!-- Parameter only needed for classic VMs and Classic Cloud Services, exclude VMSS and Resource Manager VMs-->
                <Region>{insert Azure region of resource}</Region> <!-- Parameter only needed for classic VMs and Classic Cloud Services, exclude VMSS and Resource Manager VMs -->
            </AzureMonitor>
        </Sink>
        <Sink name="ApplicationInsights">   
          <ApplicationInsights>{Insert InstrumentationKey}</ApplicationInsights>   
          <Channels>   
            <Channel logLevel="Error" name="Errors"  />   
            <Channel logLevel="Verbose" name="AppLogs"  />   
          </Channels>   
        </Sink>   
        <Sink name="EventHub"> <!-- Added in 1.7 -->
          <EventHub Url="https://myeventhub-ns.servicebus.windows.net/diageventhub" SharedAccessKeyName="SendRule" usePublisherId="false" />
        </Sink>
        <Sink name="secondaryEventHub"> <!-- Added in 1.7 -->
          <EventHub Url="https://myeventhub-ns.servicebus.windows.net/secondarydiageventhub" SharedAccessKeyName="SendRule" usePublisherId="false" />
        </Sink>
        <Sink name="secondaryStorageAccount"> <!-- Added in 1.7 -->
          <StorageAccount name="secondarydiagstorageaccount" endpoint="https://core.windows.net" />
        </Sink>
   </SinksConfig>

  </WadCfg>  

  <StorageAccount>diagstorageaccount</StorageAccount>
  <StorageType>TableAndBlob</StorageType> <!-- Added in 1.8 -->  
  </PublicConfig>  

  <PrivateConfig>  <!-- Added in 1.3 -->  
    <StorageAccount name="" key="" endpoint="" sasToken="{sas token}"  />  <!-- sasToken in Private config added in 1.8.1 -->  
    <EventHub Url="https://myeventhub-ns.servicebus.windows.net/diageventhub" SharedAccessKeyName="SendRule" SharedAccessKey="{base64 encoded key}" />

    <AzureMonitorAccount>
        <ServicePrincipalMeta> <!-- Added in 1.11; only needed for classic VMs and Classic cloud services -->
            <PrincipalId>{Insert service principal clientId}</PrincipalId>
            <Secret>{Insert service principal client secret}</Secret>
        </ServicePrincipalMeta>
    </AzureMonitorAccount>

    <SecondaryStorageAccounts>
       <StorageAccount name="secondarydiagstorageaccount" key="{base64 encoded key}" endpoint="https://core.windows.net" sasToken="{sas token}" />
    </SecondaryStorageAccounts>

    <SecondaryEventHubs>
       <EventHub Url="https://myeventhub-ns.servicebus.windows.net/secondarydiageventhub" SharedAccessKeyName="SendRule" SharedAccessKey="{base64 encoded key}" />
    </SecondaryEventHubs>

  </PrivateConfig>  
  <IsEnabled>true</IsEnabled>  
</DiagnosticsConfiguration>  

```  
> [!NOTE]
> パブリック構成の Azure Monitor シンクの定義には、resourceId と region の 2 つのプロパティがあります。 これらは、クラシック VM と従来の Cloud Services のみに必要です。 これらのプロパティは、Resource Manager 仮想マシンまたは Virtual Machine Scale Sets に使用しないでください。
> また、プリンシパル ID とシークレットを渡す、Azure Monitor シンクの追加のプライベート構成要素もあります。 これは、クラシック VM と従来の Cloud Services のみに必要です。 Resource Manager VM と VMSSでは、プライベート構成要素の Azure Monitor 定義を除外できます。
>
