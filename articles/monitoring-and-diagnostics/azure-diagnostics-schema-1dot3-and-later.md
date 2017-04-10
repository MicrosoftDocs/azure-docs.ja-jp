---
title: "Azure 診断 1.3、1.4、1.5、1.6、1.7 構成スキーマ | Microsoft Docs"
description: "Azure 診断のスキーマ バージョン 1.3 以降は、Microsoft Azure SDK に付属しています。"
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
ms.sourcegitcommit: 72b343df7df38a43e5d8fe72b453e56e29d0aba4
ms.openlocfilehash: b5d800db47284ca9fa82fc07ed67617ab32cd143
ms.lasthandoff: 02/22/2017


---
# <a name="azure-diagnostics-13-14-15-configuration-schema"></a>Azure 診断 1.3、1.4、1.5 構成スキーマ
> [!NOTE]
> Azure 診断は、Azure Virtual Machines、Virtual Machine Scale Sets、Service Fabric、および Cloud Services からパフォーマンス カウンターやその他の統計情報を収集するために使用するコンポーネントです。  このページは、これらのサービスのいずれかを使用している場合にのみ該当します。
>

Azure 診断は、Azure Monitor、Application Insights、Log Analytics など、他の Microsoft 診断製品と組み合わせて使用します。

Azure 診断構成ファイルは、診断モニターの開始時に、診断構成設定を設定するときに使用されます。  

このページは、バージョン 1.3、1.4、および 1.5 (Azure SDK 2.4 以降) に対して有効です。 新しい構成セクションには、追加されたバージョンがコメントで示されています。  

 次の PowerShell コマンドを実行して、パブリック構成ファイルのスキーマ定義をダウンロードします。  

```powershell  
(Get-AzureServiceAvailableExtension -ExtensionName 'PaaSDiagnostics' -ProviderNamespace 'Microsoft.Azure.Diagnostics').PublicConfigurationSchema | Out-File –Encoding utf8 -FilePath 'C:\temp\WadConfig.xsd'  
```  

 Azure の診断の詳細については、[Azure Cloud Services での診断の有効化](http://azure.microsoft.com/documentation/articles/cloud-services-dotnet-diagnostics/)に関するページをご覧ください。  

## <a name="example-of-the-diagnostics-configuration-file"></a>診断構成ファイルの例  
 一般的な診断構成ファイルの例を次に示します。  

```xml  
<?xml version="1.0" encoding="utf-8"?>  
<DiagnosticsConfiguration  xmlns="http://schemas.microsoft.com/ServiceHosting/2010/10/DiagnosticsConfiguration">   
  <PublicConfig>  
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

      </DiagnosticMonitorConfiguration>  

      <SinksConfig>   <!-- Added in 1.5 -->  
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
  </PublicConfig>  

  <PrivateConfig>  <!-- Added in 1.3 -->  
    <StorageAccount name="" key="" endpoint="" />
    <EventHub Url="https://myeventhub-ns.servicebus.windows.net/diageventhub" SharedAccessKeyName="SendRule" SharedAccessKey="{base64 encoded key}" />
    <SecondaryStorageAccounts>
       <StorageAccount name="secondarydiagstorageaccount" key="{base64 encoded key}" endpoint="https://core.windows.net" />
    </SecondaryStorageAccounts>
    <SecondaryEventHubs>
       <EventHub Url="https://myeventhub-ns.servicebus.windows.net/secondarydiageventhub" SharedAccessKeyName="SendRule" SharedAccessKey="{base64 encoded key}" />
    </SecondaryEventHubs>

  </PrivateConfig>  
  <IsEnabled>true</IsEnabled>  
</DiagnosticsConfiguration>  

```  

前述の XML 構成ファイルの JSON 形式を次に示します。  
```json
{
    "WadCfg": {
        "DiagnosticMonitorConfiguration": {
            "overallQuotaInMB": 10000,
            "DiagnosticInfrastructureLogs": {
                "scheduledTransferLogLevelFilter": "Error"
            },
            "PerformanceCounters": {
                "scheduledTransferPeriod": "PT1M",
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
    "StorageAccount": "diagstorageaccount"
}


{
    "storageAccountName": "diagstorageaccount",
    "storageAccountKey": "{base64 encoded key}",
    "storageAccountEndPoint": "https://core.windows.net",
    "EventHub": {
        "Url": "https://myeventhub-ns.servicebus.windows.net/diageventhub",
        "SharedAccessKeyName": "SendRule",
        "SharedAccessKey": "{base64 encoded key}"
    },
    "SecondaryStorageAccounts": {
        "StorageAccount": [
            {
                "name": "secondarydiagstorageaccount",
                "key": "{base64 encoded key}",
                "endpoint": "https://core.windows.net"
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

## <a name="reading-this-page"></a>このページについて  
 次のタグは、前の例で示されている順序とほぼ同じように示されています。  説明が見つからない場合は、このページで要素または属性を検索してください。  

## <a name="common-attribute-types"></a>一般的な属性の型  
 **scheduledTransferPeriod** 属性は、複数の要素に表示されます。 ストレージへのスケジュールされている転送の間隔です。最も近い分単位に切り上げられます。 値は [XML "Duration Data Type"](http://www.w3schools.com/schema/schema_dtypes_date.asp) です。

## <a name="diagnostics-configuration-namespace"></a>診断構成の名前空間  
 診断構成ファイルの XML 名前空間は、次のとおりです。  

```  
http://schemas.microsoft.com/ServiceHosting/2010/10/DiagnosticsConfiguration  
```  

## <a name="diagnosticsconfiguration-element"></a>DiagnosticsConfiguration 要素  
 バージョン 1.3 で追加。  

 診断構成ファイルの最上位要素。  

|子要素|Description|  
|--------------------|-----------------|  
|**PublicConfig**|必須。 このページの他の場所の説明を参照してください。|  
|**PrivateConfig**|省略可能。 このページの他の場所の説明を参照してください。|  
|**IsEnabled**|ブール値。 このページの他の場所の説明を参照してください。|  

## <a name="publicconfig-element"></a>PublicConfig 要素  
 パブリック診断構成について説明します。  

|子要素|説明|  
|--------------------|-----------------|  
|**WadCfg**|必須。 このページの他の場所の説明を参照してください。|  
|**StorageAccount**|データを格納する Azure ストレージ アカウントの名前。 Set-AzureServiceDiagnosticsExtension コマンドレットを実行するときに、パラメーターとして指定することもできます。|  
|**LocalResourceDirectory**|監視エージェントがイベント データを保存する仮想マシンのディレクトリ。 設定しない場合は、既定のディレクトリが使用されます。<br /><br /> worker/Web ロールの場合: `C:\Resources\<guid>\directory\<guid>.<RoleName.DiagnosticStore\`<br /><br /> 仮想マシンの場合: `C:\WindowsAzure\Logs\Plugins\Microsoft.Azure.Diagnostics.IaaSDiagnostics\<WADVersion>\WAD<WADVersion>`<br /><br /> 必須属性は次のとおりです。<br /><br /> - **path** - Azure 診断で使用するシステム上のディレクトリ。<br /><br /> - **expandEnvironment** - 環境変数をパス名で展開するかどうかを制御します。|  

## <a name="wadcfg-element"></a>WadCFG 要素  
 収集するテレメトリ データを特定して構成します。  

|子要素|Description|  
|--------------------|-----------------|  
|**DiagnosticMonitorConfiguration**|必須要素。<br /><br /> オプションの属性は次のとおりです。<br /><br /> - **overallQuotaInMB** - Azure 診断によって収集された、さまざまな種類の診断データで使用できるローカル ディスク領域の最大量。 既定の設定は 5120 MB です。<br /><br /> - **useProxyServer** - Azure 診断を、IE 設定で設定されたプロキシ サーバー設定を使用するように構成します。|  
|**CrashDumps**|このページの他の場所の説明を参照してください。|  
|**DiagnosticInfrastructureLogs**|Azure 診断によって生成されたログの収集を有効にします。 診断インフラストラクチャ ログは、診断システム自体のトラブルシューティングに役に立ちます。 オプションの属性は次のとおりです。<br /><br /> - **scheduledTransferLogLevelFilter** - 収集されたログの最小重大度レベルを構成します。<br /><br /> - **scheduledTransferPeriod** - ストレージへのスケジュールされている転送の間隔。最も近い分単位に切り上げられます。 値は [XML "Duration Data Type"](http://www.w3schools.com/schema/schema_dtypes_date.asp) です。 |  
|**Directories**|このページの他の場所の説明を参照してください。|  
|**EtwProviders**|このページの他の場所の説明を参照してください。|  
|**メトリック**|このページの他の場所の説明を参照してください。|  
|**PerformanceCounters**|このページの他の場所の説明を参照してください。|  
|**WindowsEventLog**|このページの他の場所の説明を参照してください。|  

## <a name="crashdumps-element"></a>CrashDumps 要素  
 クラッシュ ダンプの収集を有効にします。  

|属性|Description|  
|----------------|-----------------|  
|**containerName**|省略可能。 クラッシュ ダンプの保存に使用する Azure ストレージ アカウント内の BLOB コンテナーの名前。|  
|**crashDumpType**|省略可能。  Azure 診断を、小さいクラッシュ ダンプまたはフル クラッシュ ダンプを収集するように構成します。|  
|**directoryQuotaPercentage**|省略可能。  VM でのクラッシュ ダンプ用に予約する **overallQuotaInMB** の割合を構成します。|  

|子要素|説明|  
|--------------------|-----------------|  
|**CrashDumpConfiguration**|必須。 各プロセスの構成値を定義します。<br /><br /> 次の属性も必須です。<br /><br /> **processName** - Azure 診断でクラッシュ ダンプを収集するプロセスの名前。|  

## <a name="directories-element"></a>Directories 要素  
 ディレクトリ、IIS 失敗アクセス要求ログ、IIS ログのコンテンツの収集を有効にします。  

 オプションの **scheduledTransferPeriod** 属性。 前の説明を参照してください。  

|子要素|説明|  
|--------------------|-----------------|  
|**DataSources**|監視するディレクトリの一覧。|  
|**FailedRequestLogs**|この要素を構成に含めることで、IIS サイトまたはアプリケーションへの失敗要求に関するログの収集を有効にします。 また、**Web.config** の **system.WebServer** でトレース オプションを有効にする必要もあります。|  
|**IISLogs**|この要素を構成に含めることで、IIS ログの収集を有効にします。<br /><br /> **containerName** - IIS ログの保存に使用する Azure ストレージ アカウント内の BLOB コンテナーの名前。|  

## <a name="datasources-element"></a>DataSources 要素  
 監視するディレクトリの一覧。  

|子要素|Description|  
|--------------------|-----------------|  
|**DirectoryConfiguration**|必須。 必須属性: <br /><br /> **containerName** - ログ ファイルの保存に使用する Azure ストレージ アカウント内の BLOB コンテナーの名前。|  

## <a name="directoryconfiguration-element"></a>DirectoryConfiguration 要素  
 **Absolute** 要素または **LocalResource** 要素のいずれかを含めることができます。ただし、両方を含めることができません。  

|子要素|Description|  
|--------------------|-----------------|  
|**Absolute**|監視するディレクトリの絶対パス。 次の属性は必須です。<br /><br /> - **Path** - 監視するディレクトリの絶対パス。<br /><br /> - **expandEnvironment** - Path で環境変数を展開するかどうかを構成します。|  
|**LocalResource**|監視するローカル リソースの相対パス。 必須属性は次のとおりです。<br /><br /> - **Name** - 監視するディレクトリを含むローカル リソース<br /><br /> - **relativePath** - 監視するディレクトリを含む名前の相対パス|  

## <a name="etwproviders-element"></a>EtwProviders 要素  
 EventSource や ETW マニフェスト ベースのプロバイダーからの ETW イベントの収集を構成します。  

|子要素|説明|  
|--------------------|-----------------|  
|**EtwEventSourceProviderConfiguration**|[EventSource クラス](http://msdn.microsoft.com/library/system.diagnostics.tracing.eventsource\(v=vs.110\).aspx)から生成されたイベントの収集を構成します。 必須属性: <br /><br /> **provider** - EventSource イベントのクラス名。<br /><br /> オプションの属性は次のとおりです。<br /><br /> - **scheduledTransferLogLevelFilter** - ストレージ アカウントへの転送の最小重大度レベル。<br /><br /> - **scheduledTransferPeriod** - ストレージへのスケジュールされている転送の間隔。最も近い分単位に切り上げられます。 値は [XML "Duration Data Type"](http://www.w3schools.com/schema/schema_dtypes_date.asp) です。 |  
|**EtwManifestProviderConfiguration**|必須属性: <br /><br /> **provider** - イベント プロバイダーの GUID<br /><br /> オプションの属性は次のとおりです。<br /><br /> - **scheduledTransferLogLevelFilter** - ストレージ アカウントへの転送の最小重大度レベル。<br /><br /> - **scheduledTransferPeriod** - ストレージへのスケジュールされている転送の間隔。最も近い分単位に切り上げられます。 値は [XML "Duration Data Type"](http://www.w3schools.com/schema/schema_dtypes_date.asp) です。 |  

## <a name="etweventsourceproviderconfiguration-element"></a>EtwEventSourceProviderConfiguration 要素  
 [EventSource クラス](http://msdn.microsoft.com/library/system.diagnostics.tracing.eventsource\(v=vs.110\).aspx)から生成されたイベントの収集を構成します。  

|子要素|説明|  
|--------------------|-----------------|  
|**DefaultEvents**|オプションの属性:<br/><br/> **eventDestination** - イベントを保存するテーブルの名前|  
|**Event**|必須属性: <br /><br /> **id** - イベントの ID。<br /><br /> オプションの属性:<br /><br /> **eventDestination** - イベントを保存するテーブルの名前|  

## <a name="etwmanifestproviderconfiguration-element"></a>EtwManifestProviderConfiguration 要素  

|子要素|説明|  
|--------------------|-----------------|  
|**DefaultEvents**|オプションの属性:<br /><br /> **eventDestination** - イベントを保存するテーブルの名前|  
|**Event**|必須属性: <br /><br /> **id** - イベントの ID。<br /><br /> オプションの属性:<br /><br /> **eventDestination** - イベントを保存するテーブルの名前|  

## <a name="metrics-element"></a>Metrics 要素  
 高速クエリ用に最適化されたパフォーマンス カウンター テーブルを生成できます。 **PerformanceCounters** 要素で定義された各パフォーマンス カウンターが、パフォーマンス カウンター テーブルだけでなくメトリック テーブルにも保存されます。  

 **resourceId** 属性は必須です。  これは、Azure 診断プログラムのデプロイ先仮想マシンのリソース ID です。 [Azure Portal](https://portal.azure.com) から **resourceID** を取得します。 **[参照]**  ->  **[リソース グループ]**  ->  **<名前\>** の順に選択します。 **[プロパティ]** タイルをクリックし、**[ID]** フィールドの値をコピーします。  

|子要素|Description|  
|--------------------|-----------------|  
|**MetricAggregation**|必須属性: <br /><br /> **scheduledTransferPeriod** - ストレージへのスケジュールされている転送の間隔。最も近い分単位に切り上げられます。 値は [XML "Duration Data Type"](http://www.w3schools.com/schema/schema_dtypes_date.asp) です。 |  

## <a name="performancecounters-element"></a>PerformanceCounters 要素  
 パフォーマンス カウンターの収集を有効にします。  

 オプションの属性:  

 オプションの **scheduledTransferPeriod** 属性。 前の説明を参照してください。

|子要素|Description|  
|-------------------|-----------------|  
|**PerformanceCounterConfiguration**|次の属性は必須です。<br /><br /> - **counterSpecifier** - パフォーマンス カウンターの名前。 たとえば、「 `\Processor(_Total)\% Processor Time`」のように入力します。 ホストでカウンター パフォーマンスの一覧を取得するには、`typeperf` コマンドを実行します。<br /><br /> - **sampleRate** - カウンターをサンプリングする頻度。<br /><br /> オプションの属性:<br /><br /> **unit** - カウンターの測定単位。|  

## <a name="windowseventlog-element"></a>WindowsEventLog 要素  
 Windows イベント ログの収集を有効にします。  

 オプションの **scheduledTransferPeriod** 属性。 前の説明を参照してください。  

|子要素|Description|  
|-------------------|-----------------|  
|**DataSource**|収集する Windows イベント ログ。 必須属性: <br /><br /> **name** - 収集する Windows イベントについて説明する XPath クエリ。 For example:<br /><br /> `Application!*[Application[(Level <=3)]], System!*[System[(Level <=3)]], System!*[System[Provider[@Name='Microsoft Antimalware']]], Security!*[Security[(Level <= 3)]`<br /><br /> すべてのイベントを収集するには、"*" を指定します。|  

## <a name="logs-element"></a>Logs 要素  
 バージョン 1.0 および 1.1 に存在します。 1.2 にはありません。 1.3 で再び追加されています。  

 基本的な Azure ログのバッファー構成を定義します。  

|Attribute|型|Description|  
|---------------|----------|-----------------|  
|**bufferQuotaInMB**|**unsignedInt**|省略可能。 指定されたデータに使用できるファイル システム ストレージの最大量を指定します。<br /><br /> 既定値は 0 です。|  
|**scheduledTransferLogLevelFilterr**|**string**|省略可能。 転送されるログ エントリの最小重大度レベルを指定します。 既定値は **Undefined** で、すべてのログを転送します。 他の有効値は、(情報量が多いものから順に) **Verbose**、**Information**、**Warning**、**Error**、**Critical** となります。|  
|**scheduledTransferPeriod**|**duration**|省略可能。 最も近い分単位の値に丸められた、スケジュールされているデータ転送の間隔を指定します。<br /><br /> 既定値は PT0S です。|  
|**sinks** 1.5 で追加|**string**|省略可能。 sink の場所を指定して、診断データも送信します。 Application Insights など。|  

## <a name="sinksconfig-element"></a>SinksConfig 要素  
 診断データの送信先の一覧と、その場所に関連付けられている構成。  

|要素名|Description|  
|------------------|-----------------|  
|**シンク**|このページの他の場所の説明を参照してください。|  

## <a name="sink-element"></a>Sink 要素  
 バージョン 1.5 で追加。  

 診断データの送信先を定義します。 Application Insights サービスなど。  

|Attribute|型|Description|  
|---------------|----------|-----------------|  
|**name**|string|シンク名を特定する文字列。|  

|要素|種類|説明|  
|-------------|----------|-----------------|  
|**Application Insights**|string|データを Application Insights に送信するときにのみ使用されます。 アクセス先のアクティブな Application Insights アカウントのインストルメンテーション キーが含まれます。|  
|**Channels**|string|追加フィルタリングごとに&1; つ|  

## <a name="channels-element"></a>Channels 要素  
 バージョン 1.5 で追加。  

 シンクを通過するログ データのストリームのフィルターを定義します。  

|要素|種類|説明|  
|-------------|----------|-----------------|  
|**Channel**|string|このページの他の場所の説明を参照してください。|  

## <a name="channel-element"></a>Channel 要素  
 バージョン 1.5 で追加。  

 診断データの送信先を定義します。 Application Insights サービスなど。  

|属性|種類|Description|  
|----------------|----------|-----------------|  
|**logLevel**|**string**|転送されるログ エントリの最小重大度レベルを指定します。 既定値は **Undefined** で、すべてのログを転送します。 他の有効値は、(情報量が多いものから順に) **Verbose**、**Information**、**Warning**、**Error**、**Critical** となります。|  
|**name**|**string**|参照するチャネルの一意の名前|  

## <a name="privateconfig-element"></a>PrivateConfig 要素  
 バージョン 1.3 で追加。  

 省略可能  

 ストレージ アカウントのプライベート詳細 (名前、キー、およびエンドポイント) を保存します。 この情報は仮想マシンに送信されますが、その仮想マシンから取得することはできません。  

|子要素|説明|  
|--------------------|-----------------|  
|**StorageAccount**|使用するストレージ アカウント。 次の属性は必須です<br /><br /> - **name** - ストレージ アカウントの名前。<br /><br /> - **key** - ストレージ アカウントへのキー。<br /><br /> - **endpoint** - ストレージ アカウントにアクセスするためのエンドポイント。|  

## <a name="isenabled-element"></a>IsEnabled 要素  
 ブール値。 `true` を使用して診断を有効にするか、`false` を使用して診断を無効にします。

