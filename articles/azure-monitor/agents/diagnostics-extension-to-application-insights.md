---
title: Azure Diagnostics のデータを Application Insights に送信する
description: Application Insights にデータを送信するように Azure Diagnostics のパブリック構成を更新します。
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 03/19/2016
ms.openlocfilehash: b9e9d6b1b5939804b24fd523bf8b7444ed41178f
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "101708596"
---
# <a name="send-cloud-service-virtual-machine-or-service-fabric-diagnostic-data-to-application-insights"></a>Cloud Services、Virtual Machines、または Service Fabric の診断データを Application Insights に送信する
Cloud Services、Virtual Machines、Virtual Machine Scale Sets、および Service Fabric では、Azure Diagnostics 拡張機能を使用してデータを収集します。  Azure Diagnostics のデータは、Azure Storage のテーブルに送信されます。  ただし、Azure Diagnostics 拡張機能 1.5 以降を使用して、すべてのデータまたはデータのサブセットを他の場所にパイプすることもできます。

この記事では、Azure Diagnostics 拡張機能から Application Insights にデータを送信する方法について説明します。

## <a name="diagnostics-configuration-explained"></a>診断構成の説明
Azure Diagnostics 拡張機能 1.5 では、シンクが導入されました。シンクとは、診断データを送信できるその他の場所のことです。

Application Insights のシンクの構成の例を以下に示します。

```XML
<SinksConfig>
    <Sink name="ApplicationInsights">
      <ApplicationInsights>{Insert InstrumentationKey}</ApplicationInsights>
      <Channels>
        <Channel logLevel="Error" name="MyTopDiagData"  />
        <Channel logLevel="Verbose" name="MyLogData"  />
      </Channels>
    </Sink>
</SinksConfig>
```
```JSON
"SinksConfig": {
    "Sink": [
        {
            "name": "ApplicationInsights",
            "ApplicationInsights": "{Insert InstrumentationKey}",
            "Channels": {
                "Channel": [
                    {
                        "logLevel": "Error",
                        "name": "MyTopDiagData"
                    },
                    {
                        "logLevel": "Error",
                        "name": "MyLogData"
                    }
                ]
            }
        }
    ]
}
```
- **シンクの** *name* 属性は、シンクを一意に識別する文字列値です。

- **ApplicationInsights** 要素では、Azure Diagnostics データの送信先となる Application Insights リソースのインストルメンテーション キーを指定します。
    - 既存の Application Insights リソースがない場合、リソースの作成方法とインストルメンテーション キーの取得方法の詳細については、「[新しい Application Insights リソースを作成する](../app/create-new-resource.md)」を参照してください。
    - Azure SDK 2.8 以降でクラウド サービスを開発する場合、このインストルメンテーション キーが自動的に設定されます。 この値は、クラウド サービス プロジェクトをパッケージ化するときの **APPINSIGHTS_INSTRUMENTATIONKEY** サービス構成設定に基づきます。 [Cloud Services での Application Insights の使用](../app/cloudservices.md)に関するページを参照してください。

- **Channels** 要素には、1 つ以上の **Channel** 要素が含まれます。
    - *name* 属性は、そのチャンネルを一意に参照します。
    - *loglevel* 属性では、チャンネルで許可されるログ レベルを指定できます。 使用可能なログ レベルを情報の多い順に並べると、次のようになります。
        - "詳細"
        - Information
        - 警告
        - エラー
        - Critical

チャンネルはフィルターのように機能し、対象のシンクに送信する特定のログ レベルを選択するために使用できます。 たとえば、詳細ログを収集してストレージに送信し、シンクにはエラーのみを送信することができます。

次の図にこの関係を示します。

![Diagnostics Public Configuration](media/diagnostics-extension-to-application-insights/AzDiag_Channels_App_Insights.png)

次の図は、構成値とそのしくみについてまとめたものです。 構成の階層内のさまざまなレベルに複数のシンクを含めることができます。 最上位のシンクはグローバルな設定として機能し、個々の要素のレベルに指定されたシンクはそのグローバル設定をオーバーライドします。

![Application Insights による診断シンクの構成](media/diagnostics-extension-to-application-insights/Azure_Diagnostics_Sinks.png)

## <a name="complete-sink-configuration-example"></a>完全なシンクの構成例
パブリック構成ファイルの完全な例を以下に紹介します。
1. Application Insights にすべてのエラーを送信する (**DiagnosticMonitorConfiguration** ノードで指定)。
2. アプリケーション ログにも詳細レベルのログを送信する (**Logs** ノードで指定)。

```XML
<WadCfg>
  <DiagnosticMonitorConfiguration overallQuotaInMB="4096"
       sinks="ApplicationInsights.MyTopDiagData"> <!-- All info below sent to this channel -->
    <DiagnosticInfrastructureLogs />
    <PerformanceCounters>
      <PerformanceCounterConfiguration counterSpecifier="\Processor(_Total)\% Processor Time" sampleRate="PT3M" />
      <PerformanceCounterConfiguration counterSpecifier="\Memory\Available MBytes" sampleRate="PT3M" />
    </PerformanceCounters>
    <WindowsEventLog scheduledTransferPeriod="PT1M">
      <DataSource name="Application!*" />
    </WindowsEventLog>
    <Logs scheduledTransferPeriod="PT1M" scheduledTransferLogLevelFilter="Verbose"
            sinks="ApplicationInsights.MyLogData"/> <!-- This specific info sent to this channel -->
  </DiagnosticMonitorConfiguration>

<SinksConfig>
    <Sink name="ApplicationInsights">
      <ApplicationInsights>{Insert InstrumentationKey}</ApplicationInsights>
      <Channels>
        <Channel logLevel="Error" name="MyTopDiagData"  />
        <Channel logLevel="Verbose" name="MyLogData"  />
      </Channels>
    </Sink>
  </SinksConfig>
</WadCfg>
```
```JSON
"WadCfg": {
    "DiagnosticMonitorConfiguration": {
        "overallQuotaInMB": 4096,
        "sinks": "ApplicationInsights.MyTopDiagData", "_comment": "All info below sent to this channel",
        "DiagnosticInfrastructureLogs": {
        },
        "PerformanceCounters": {
            "PerformanceCounterConfiguration": [
                {
                    "counterSpecifier": "\\Processor(_Total)\\% Processor Time",
                    "sampleRate": "PT3M"
                },
                {
                    "counterSpecifier": "\\Memory\\Available MBytes",
                    "sampleRate": "PT3M"
                }
            ]
        },
        "WindowsEventLog": {
            "scheduledTransferPeriod": "PT1M",
            "DataSource": [
                {
                    "name": "Application!*"
                }
            ]
        },
        "Logs": {
            "scheduledTransferPeriod": "PT1M",
            "scheduledTransferLogLevelFilter": "Verbose",
            "sinks": "ApplicationInsights.MyLogData", "_comment": "This specific info sent to this channel"
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
                            "name": "MyTopDiagData"
                        },
                        {
                            "logLevel": "Verbose",
                            "name": "MyLogData"
                        }
                    ]
                }
            }
        ]
    }
}
```
前の構成の以下の行には、次の意味があります。

### <a name="send-all-the-data-that-is-being-collected-by-azure-diagnostics"></a>Azure Diagnostics によって収集されているすべてのデータを送信する

```xml
<DiagnosticMonitorConfiguration overallQuotaInMB="4096" sinks="ApplicationInsights">
```

```json
"DiagnosticMonitorConfiguration": {
    "overallQuotaInMB": 4096,
    "sinks": "ApplicationInsights",
}
```

### <a name="send-only-error-logs-to-the-application-insights-sink"></a>Application Insights のシンクにエラー ログのみを送信する

```xml
<DiagnosticMonitorConfiguration overallQuotaInMB="4096" sinks="ApplicationInsights.MyTopDiagdata">
```

```json
"DiagnosticMonitorConfiguration": {
    "overallQuotaInMB": 4096,
    "sinks": "ApplicationInsights.MyTopDiagData",
}
```

### <a name="send-verbose-application-logs-to-application-insights"></a>Application Insights に詳細なアプリケーション ログを送信する

```XML
<Logs scheduledTransferPeriod="PT1M" scheduledTransferLogLevelFilter="Verbose" sinks="ApplicationInsights.MyLogData"/>
```
```JSON
"DiagnosticMonitorConfiguration": {
    "overallQuotaInMB": 4096,
    "sinks": "ApplicationInsights.MyLogData",
}
```

## <a name="limitations"></a>制限事項

- **チャンネルでは、種類のみがログに記録されます。パフォーマンス カウンターは記録されません。** パフォーマンス カウンターの要素を含むチャンネルを指定した場合、そのチャンネルは無視されます。
- **チャネルのログ レベルには、Azure Diagnostics によって収集されるデータのログ レベルを超えるログを指定することはできません。** たとえば、Logs 要素でアプリケーション ログのエラーを収集できないため、Application Insight シンクに "詳細" ログを送信することにします。 *scheduledTransferLogLevelFilter* 属性では、シンクに送信するログと同じかそれを超えるレベルのログを常に収集する必要があります。
- **Azure Diagnostics の拡張機能によって収集される BLOB データは Application Insights に送信できません。** たとえば、*Directories* ノードの下で指定されたデータがこれに該当します。 クラッシュ ダンプの場合、実際のクラッシュ ダンプは Blob Storage に送信され、Application Insights にはクラッシュ ダンプが生成されたという通知のみが送信されます。

## <a name="next-steps"></a>次の手順
* Application Insights で [Azure Diagnostics 情報を表示する](../app/cloudservices.md)方法について説明します。
* [PowerShell](../../cloud-services/cloud-services-diagnostics-powershell.md) を使用して、アプリケーションの Azure Diagnostics の拡張機能を有効にします。
* [Visual Studio](/visualstudio/azure/vs-azure-tools-diagnostics-for-cloud-services-and-virtual-machines) を使用して、アプリケーションの Azure Diagnostics の拡張機能を有効にします。

