---
title: "Azure 診断 1.2 構成スキーマ | Microsoft Docs"
description: "この記事は、Azure SDK 2.5 を Azure Virtual Machines、Virtual Machine Scale Sets、Service Fabric、または Cloud Services と共に使用している場合にのみ該当します。"
services: monitoring-and-diagnostics
documentationcenter: .net
author: rboucher
manager: carmonm
editor: 
ms.assetid: 
ms.service: monitoring-and-diagnostics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: article
ms.date: 02/09/2017
ms.author: robb
translationtype: Human Translation
ms.sourcegitcommit: 66f733d7602a8d26627fcc205f357e7a4a266d11
ms.openlocfilehash: b76ef954d8a00e190817e3d7f8e2b064210d0357
ms.lasthandoff: 02/22/2017


---
# <a name="azure-diagnostics-12-configuration-schema"></a>Azure 診断 1.2 構成スキーマ
> [!NOTE]
> Azure 診断は、Azure Virtual Machines、Virtual Machine Scale Sets、Service Fabric、および Cloud Services からパフォーマンス カウンターやその他の統計情報を収集するために使用するコンポーネントです。  このページは、これらのサービスのいずれかを使用している場合にのみ該当します。
>

Azure 診断は、Azure Monitor、Application Insights、Log Analytics など、他の Microsoft 診断製品と共に使用します。

このスキーマは、診断モニターの開始時に、診断構成設定を初期化するために使用できる値を定義します。  


 次の PowerShell コマンドを実行して、パブリック構成ファイルのスキーマ定義をダウンロードします。  

```PowerShell  
(Get-AzureServiceAvailableExtension -ExtensionName 'PaaSDiagnostics' -ProviderNamespace 'Microsoft.Azure.Diagnostics').PublicConfigurationSchema | Out-File –Encoding utf8 -FilePath 'C:\temp\WadConfig.xsd'  
```  

 Azure の診断の詳細については、[Azure Cloud Services での診断の有効化](http://azure.microsoft.com/documentation/articles/cloud-services-dotnet-diagnostics/)に関するページをご覧ください。  

## <a name="example-of-the-diagnostics-configuration-file"></a>診断構成ファイルの例  
 一般的な診断構成ファイルの例を次に示します。  

```xml
<?xml version="1.0" encoding="utf-8"?>  
<PublicConfig xmlns="http://schemas.microsoft.com/ServiceHosting/2010/10/DiagnosticsConfiguration">  
  <WadCfg>  
    <DiagnosticMonitorConfiguration overallQuotaInMB="10000">  
      <PerformanceCounters scheduledTransferPeriod="PT1M">  
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
        <EtwEventSourceProviderConfiguration provider="MyProviderClass" scheduledTransferPeriod="PT5M">  
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
      <CrashDumps containerName="wad-crashdumps" directoryQuotaPercentage="30" dumpType="Mini">  
        <CrashDumpConfiguration processName="mynewprocess.exe" />  
        <CrashDumpConfiguration processName="badapp.exe"/>  
      </CrashDumps>  
    </DiagnosticMonitorConfiguration>  
  </WadCfg>  
</PublicConfig>  

```  

## <a name="diagnostics-configuration-namespace"></a>診断構成の名前空間  
 診断構成ファイルの XML 名前空間は、次のとおりです。  

```  
http://schemas.microsoft.com/ServiceHosting/2010/10/DiagnosticsConfiguration  
```  

## <a name="publicconfig-element"></a>PublicConfig 要素  
 診断構成ファイルの最上位要素。 構成ファイルの要素を次の表に示します。  

|要素名|Description|  
|------------------|-----------------|  
|**WadCfg**|必須。 収集するテレメトリ データの構成設定。|  
|**StorageAccount**|データを格納する Azure ストレージ アカウントの名前。 Set-AzureServiceDiagnosticsExtension コマンドレットを実行するときに、パラメーターとして指定することもできます。|  
|**LocalResourceDirectory**|監視エージェントがイベント データの格納に使用する仮想マシンのディレクトリ。 設定しない場合は、既定のディレクトリが使用されます。<br /><br /> worker/Web ロールの場合: `C:\Resources\<guid>\directory\<guid>.<RoleName.DiagnosticStore\`<br /><br /> 仮想マシンの場合: `C:\WindowsAzure\Logs\Plugins\Microsoft.Azure.Diagnostics.IaaSDiagnostics\<WADVersion>\WAD<WADVersion>`<br /><br /> 必須属性は次のとおりです。<br /><br /> -                      **path** - Azure 診断で使用するシステム上のディレクトリ。<br /><br /> -                      **expandEnvironment** - 環境変数をパス名で展開するかどうかを制御します。|  

## <a name="wadcfg-element"></a>WadCFG 要素  
収集するテレメトリ データの構成設定を定義します。 次の表で、子要素について説明します。  

|要素名|Description|  
|------------------|-----------------|  
|**DiagnosticMonitorConfiguration**|必須。 オプションの属性は次のとおりです。<br /><br /> -                     **overallQuotaInMB** - Azure 診断によって収集された、さまざまな種類の診断データで使用できるローカル ディスク領域の最大量。 既定の設定は 5120 MB です。<br /><br /> -                     **useProxyServer** - IE 設定で設定したプロキシ サーバー設定を使用するように Azure 診断を構成します。|  
|**CrashDumps**|クラッシュ ダンプの収集を有効にします。 オプションの属性は次のとおりです。<br /><br /> -                     **containerName** - クラッシュ ダンプの保存に使用する Azure ストレージ アカウント内の BLOB コンテナーの名前。<br /><br /> -                     **crashDumpType** - 小さいクラッシュ ダンプまたはフル クラッシュ ダンプを収集するように Azure 診断を構成します。<br /><br /> -                     **directoryQuotaPercentage** - VM でクラッシュ ダンプ用に予約する **overallQuotaInMB** の割合を構成します。|  
|**DiagnosticInfrastructureLogs**|Azure 診断によって生成されたログの収集を有効にします。 診断インフラストラクチャ ログは、診断システム自体のトラブルシューティングに役に立ちます。 オプションの属性は次のとおりです。<br /><br /> -                     **scheduledTransferLogLevelFilter** - 収集されたログの最小重大度レベルを構成します。<br /><br /> -                     **scheduledTransferPeriod** - ストレージへのスケジュールされている転送の間隔。最も近い分単位に切り上げられます。 値は [XML "Duration Data Type"](http://www.w3schools.com/schema/schema_dtypes_date.asp) です。|  
|**Directories**|ディレクトリ、IIS 失敗アクセス要求ログ、IIS ログのコンテンツの収集を有効にします。 オプションの属性:<br /><br /> **scheduledTransferPeriod** - ストレージへのスケジュールされている転送の間隔。最も近い分単位に切り上げられます。 値は [XML "Duration Data Type"](http://www.w3schools.com/schema/schema_dtypes_date.asp) です。|  
|**EtwProviders**|EventSource や ETW マニフェスト ベースのプロバイダーからの ETW イベントの収集を構成します。|  
|**メトリック**|この要素により、高速クエリ用に最適化されたパフォーマンス カウンター テーブルを生成できます。 **PerformanceCounters** 要素で定義された各パフォーマンス カウンターが、パフォーマンス カウンター テーブルだけでなくメトリック テーブルにも保存されます。 必須属性: <br /><br /> **resourceId** - これは、Azure 診断のデプロイ先とする仮想マシンのリソース ID です。 [Azure Portal](https://portal.azure.com) から **resourceID** を取得します。 **[参照]**  ->  **[リソース グループ]**  ->  **<名前\>** の順に選択します。 **[プロパティ]** タイルをクリックし、**[ID]** フィールドの値をコピーします。|  
|**PerformanceCounters**|パフォーマンス カウンターの収集を有効にします。 オプションの属性:<br /><br /> **scheduledTransferPeriod** - ストレージへのスケジュールされている転送の間隔。最も近い分単位に切り上げられます。 値は [XML "Duration Data Type"](http://www.w3schools.com/schema/schema_dtypes_date.asp) です。|  
|**WindowsEventLog**|Windows イベント ログの収集を有効にします。 オプションの属性:<br /><br /> **scheduledTransferPeriod** - ストレージへのスケジュールされている転送の間隔。最も近い分単位に切り上げられます。 値は [XML "Duration Data Type"](http://www.w3schools.com/schema/schema_dtypes_date.asp) です。|  

## <a name="crashdumps-element"></a>CrashDumps 要素  
 クラッシュ ダンプの収集を有効にします。 次の表で、子要素について説明します。  

|要素名|Description|  
|------------------|-----------------|  
|**CrashDumpConfiguration**|必須。 必須属性: <br /><br /> **processName** - Azure 診断でクラッシュ ダンプを収集するプロセスの名前。|  
|**crashDumpType**|Azure 診断を、小さいクラッシュ ダンプまたはフル クラッシュ ダンプを収集するように構成します。|  
|**directoryQuotaPercentage**|VM でのクラッシュ ダンプ用に予約する **overallQuotaInMB** の割合を構成します。|  

## <a name="directories-element"></a>Directories 要素  
 ディレクトリ、IIS 失敗アクセス要求ログ、IIS ログのコンテンツの収集を有効にします。 次の表で、子要素について説明します。  

|要素名|Description|  
|------------------|-----------------|  
|**DataSources**|監視するディレクトリの一覧。|  
|**FailedRequestLogs**|この要素を構成に含めることで、IIS サイトまたはアプリケーションへの失敗要求に関するログの収集を有効にします。 また、**Web.config** の **system.WebServer** でトレース オプションを有効にする必要もあります。|  
|**IISLogs**|この要素を構成に含めることで、IIS ログの収集を有効にします。<br /><br /> **containerName** - IIS ログの保存に使用する Azure ストレージ アカウント内の BLOB コンテナーの名前。|  

## <a name="datasources-element"></a>DataSources 要素  
 監視するディレクトリの一覧。 次の表で、子要素について説明します。  

|要素名|Description|  
|------------------|-----------------|  
|**DirectoryConfiguration**|必須。 必須属性: <br /><br /> **containerName** - ログ ファイルの保存に使用する Azure ストレージ アカウント内の BLOB コンテナーの名前。|  

## <a name="directoryconfiguration-element"></a>DirectoryConfiguration 要素  
 **DirectoryConfiguration** には、**Absolute** 要素または **LocalResource** 要素のいずれかを含めることができます。ただし、両方を含めることができません。 次の表で、子要素について説明します。  

|要素名|Description|  
|------------------|-----------------|  
|**Absolute**|監視するディレクトリの絶対パス。 次の属性は必須です。<br /><br /> -                     **Path** - 監視するディレクトリの絶対パス。<br /><br /> -                      **expandEnvironment** - Path で環境変数を展開するかどうかを構成します。|  
|**LocalResource**|監視するローカル リソースの相対パス。 必須属性は次のとおりです。<br /><br /> -                     **Name** - 監視するディレクトリを含むローカル リソース<br /><br /> -                     **relativePath** - 監視するディレクトリを含む名前の相対パス|  

## <a name="etwproviders-element"></a>EtwProviders 要素  
 EventSource や ETW マニフェスト ベースのプロバイダーからの ETW イベントの収集を構成します。 次の表で、子要素について説明します。  

|要素名|Description|  
|------------------|-----------------|  
|**EtwEventSourceProviderConfiguration**|[EventSource クラス](http://msdn.microsoft.com/library/system.diagnostics.tracing.eventsource\(v=vs.110\).aspx)から生成されたイベントの収集を構成します。 必須属性: <br /><br /> **provider** - EventSource イベントのクラス名。<br /><br /> オプションの属性は次のとおりです。<br /><br /> -                     **scheduledTransferLogLevelFilter** - ストレージ アカウントへの転送の最小重大度レベル。<br /><br /> -                     **scheduledTransferPeriod** - ストレージへのスケジュールされている転送の間隔。最も近い分単位に切り上げられます。 値は [XML "Duration Data Type"](http://www.w3schools.com/schema/schema_dtypes_date.asp) です。|  
|**EtwManifestProviderConfiguration**|必須属性: <br /><br /> **provider** - イベント プロバイダーの GUID<br /><br /> オプションの属性は次のとおりです。<br /><br /> - **scheduledTransferLogLevelFilter** - ストレージ アカウントへの転送の最小重大度レベル。<br /><br /> -                     **scheduledTransferPeriod** - ストレージへのスケジュールされている転送の間隔。最も近い分単位に切り上げられます。 値は [XML "Duration Data Type"](http://www.w3schools.com/schema/schema_dtypes_date.asp) です。|  

## <a name="etweventsourceproviderconfiguration-element"></a>EtwEventSourceProviderConfiguration 要素  
 [EventSource クラス](http://msdn.microsoft.com/library/system.diagnostics.tracing.eventsource\(v=vs.110\).aspx)から生成されたイベントの収集を構成します。 次の表で、子要素について説明します。  

|要素名|説明|  
|------------------|-----------------|  
|**DefaultEvents**|オプションの属性:<br /><br /> **eventDestination** - イベントを保存するテーブルの名前|  
|**Event**|必須属性: <br /><br /> **id** - イベントの ID。<br /><br /> オプションの属性:<br /><br /> **eventDestination** - イベントを保存するテーブルの名前|  

## <a name="etwmanifestproviderconfiguration-element"></a>EtwManifestProviderConfiguration 要素  
 次の表で、子要素について説明します。  

|要素名|説明|  
|------------------|-----------------|  
|**DefaultEvents**|オプションの属性:<br /><br /> **eventDestination** - イベントを保存するテーブルの名前|  
|**Event**|必須属性: <br /><br /> **id** - イベントの ID。<br /><br /> オプションの属性:<br /><br /> **eventDestination** - イベントを保存するテーブルの名前|  

## <a name="metrics-element"></a>Metrics 要素  
 高速クエリ用に最適化されたパフォーマンス カウンター テーブルを生成できます。 次の表で、子要素について説明します。  

|要素名|Description|  
|------------------|-----------------|  
|**MetricAggregation**|必須属性: <br /><br /> **scheduledTransferPeriod** - ストレージへのスケジュールされている転送の間隔。最も近い分単位に切り上げられます。 値は [XML "Duration Data Type"](http://www.w3schools.com/schema/schema_dtypes_date.asp) です。|  

## <a name="performancecounters-element"></a>PerformanceCounters 要素  
 パフォーマンス カウンターの収集を有効にします。 次の表で、子要素について説明します。  

|要素名|Description|  
|------------------|-----------------|  
|**PerformanceCounterConfiguration**|次の属性は必須です。<br /><br /> -                     **counterSpecifier** - パフォーマンス カウンターの名前。 たとえば、「 `\Processor(_Total)\% Processor Time`」のように入力します。 ホストでカウンター パフォーマンスの一覧を取得するには、`typeperf` コマンドを実行します。<br /><br /> -                     **sampleRate** - カウンターをサンプリングする頻度。<br /><br /> オプションの属性:<br /><br /> **unit** - カウンターの測定単位。|  

## <a name="performancecounterconfiguration-element"></a>PerformanceCounterConfiguration 要素  
 次の表で、子要素について説明します。  

|要素名|Description|  
|------------------|-----------------|  
|**annotation**|必須属性: <br /><br /> **displayName** - カウンターの表示名<br /><br /> オプションの属性:<br /><br /> **locale** - カウンター名を表示するときに使用するロケール|  

## <a name="windowseventlog-element"></a>WindowsEventLog 要素  
 次の表で、子要素について説明します。  

|要素名|Description|  
|------------------|-----------------|  
|**DataSource**|収集する Windows イベント ログ。 必須属性: <br /><br /> **name** - 収集する Windows イベントについて説明する XPath クエリ。 For example:<br /><br /> `Application!*[System[(Level >= 3)]], System!*[System[(Level <=3)]], System!*[System[Provider[@Name='Microsoft Antimalware']]], Security!*[System[(Level >= 3]]`<br /><br /> すべてのイベントを収集するには、"*" を指定します。|

