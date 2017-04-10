---
title: "Application Insights にデータを送信するための Azure 診断の構成 | Microsoft Docs"
description: "Application Insights にデータを送信するように Azure 診断のパブリック構成を更新します。"
services: monitoring-and-diagnostics
documentationcenter: .net
author: rboucher
manager: carmonm
editor: 
ms.assetid: f9e12c3e-c307-435e-a149-ef0fef20513a
ms.service: monitoring-and-diagnostics
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 03/19/2016
ms.author: robb
translationtype: Human Translation
ms.sourcegitcommit: 424d8654a047a28ef6e32b73952cf98d28547f4f
ms.openlocfilehash: f6848fef5b23a864496565334b22dc2e2e8d1492
ms.lasthandoff: 03/22/2017


---
# <a name="send-cloud-service-virtual-machine-or-service-fabric-diagnostic-data-to-application-insights"></a>Cloud Services、Virtual Machines、または Service Fabric の診断データを Application Insights に送信する
Cloud Services、Virtual Machines、Virtual Machine Scale Sets、および Service Fabric では、Azure 診断拡張機能を使用してデータを収集します。  Azure 診断のデータは、Azure Storage のテーブルに送信されます。  ただし、Azure 診断拡張機能 1.5 以降を使用して、すべてのデータまたはデータのサブセットを他の場所にパイプすることもできます。

この記事では、Azure 診断拡張機能から Application Insights にデータを送信する方法について説明します。

## <a name="diagnostics-configuration-explained"></a>診断構成の説明
Azure 診断拡張機能 1.5 では、シンクが導入されました。シンクとは、診断データを送信できるその他の場所のことです。

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

- **シンク**の *name* 属性は、シンクを一意に識別する文字列値です。

- **ApplicationInsights** 要素では、Azure 診断データの送信先となる Application Insights リソースのインストルメンテーション キーを指定します。
    - 既存の Application Insights リソースがない場合、リソースの作成方法とインストルメンテーション キーの取得方法の詳細については、「[新しい Application Insights リソースを作成する](../application-insights/app-insights-create-new-resource.md)」を参照してください。
    - Azure SDK 2.8 以降でクラウド サービスを開発する場合、このインストルメンテーション キーが自動的に設定されます。 この値は、クラウド サービス プロジェクトをパッケージ化するときの **APPINSIGHTS_INSTRUMENTATIONKEY** サービス構成設定に基づきます。 詳細については、[Application Insights と Azure 診断を使用したクラウド サービスの問題のトラブルシューティング](../cloud-services/cloud-services-dotnet-diagnostics-applicationinsights.md)に関するページを参照してください。

- **Channels** 要素には、1 つ以上の **Channel** 要素が含まれます。
    - *name* 属性は、そのチャンネルを一意に参照します。
    - *loglevel* 属性では、チャンネルで許可されるログ レベルを指定できます。 使用可能なログ レベルを情報の多い順に並べると、次のようになります。
        - 詳細
        - 情報
        - 警告
        - エラー
        - 重大

チャンネルはフィルターのように機能し、対象のシンクに送信する特定のログ レベルを選択するために使用できます。 たとえば、詳細ログを収集してストレージに送信し、シンクにはエラーのみを送信することができます。

次の図にこの関係を示します。

![Diagnostics Public Configuration](./media/azure-diagnostics-configure-applicationinsights/AzDiag_Channels_App_Insights.png)

次の図は、構成値とそのしくみについてまとめたものです。 構成の階層内のさまざまなレベルに複数のシンクを含めることができます。 最上位のシンクはグローバルな設定として機能し、個々の要素のレベルに指定されたシンクはそのグローバル設定よりも優先されます。

![Application Insights による診断シンクの構成](./media/azure-diagnostics-configure-applicationinsights/Azure_Diagnostics_Sinks.png)

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
      <PerformanceCounterConfiguration counterSpecifier="\Processor(_Total)\% Processor Time" sampleRate="PT3M" sinks="ApplicationInsights.MyLogData/>
      <PerformanceCounterConfiguration counterSpecifier="\Memory\Available MBytes" sampleRate="PT3M" />
      <PerformanceCounterConfiguration counterSpecifier="\Web Service(_Total)\Bytes Total/Sec" sampleRate="PT3M" />
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

前の構成の以下の行には、次の意味があります。

### <a name="send-all-the-data-that-is-being-collected-by-azure-diagnostics"></a>Azure 診断によって収集されているすべてのデータを送信する

```XML
<DiagnosticMonitorConfiguration overallQuotaInMB="4096" sinks="ApplicationInsights">
```

### <a name="send-only-error-logs-to-the-application-insights-sink"></a>Application Insights のシンクにエラー ログのみを送信する

```XML
<DiagnosticMonitorConfiguration overallQuotaInMB="4096" sinks="ApplicationInsights.MyTopDiagdata">
```

### <a name="send-verbose-application-logs-to-application-insights"></a>Application Insights に詳細なアプリケーション ログを送信する

```XML
<Logs scheduledTransferPeriod="PT1M" scheduledTransferLogLevelFilter="Verbose" sinks="ApplicationInsights.MyLogData"/>
```

## <a name="limitations"></a>制限事項

- **チャンネルでは、種類のみがログに記録されます。パフォーマンス カウンターは記録されません。** パフォーマンス カウンターの要素を含むチャンネルを指定した場合、そのチャンネルは無視されます。
- **チャンネルのログ レベルには、Azure 診断によって収集されるデータのログ レベルを超えるログを指定することはできません。** たとえば、Logs 要素でアプリケーション ログのエラーを収集できないため、Application Insight シンクに "詳細" ログを送信することにします。 *scheduledTransferLogLevelFilter* 属性では、シンクに送信するログと同じかそれを超えるレベルのログを常に収集する必要があります。
- **Azure 診断の拡張機能によって収集される BLOB データは Application Insights に送信できません。** たとえば、*Directories* ノードの下で指定されたデータがこれに該当します。 クラッシュ ダンプの場合、実際のクラッシュ ダンプは Blob Storage に送信され、Application Insights にはクラッシュ ダンプが生成されたという通知のみが送信されます。

## <a name="next-steps"></a>次のステップ
* [PowerShell](../cloud-services/cloud-services-diagnostics-powershell.md) を使用して、アプリケーションの Azure 診断の拡張機能を有効にします。
* [Visual Studio](../vs-azure-tools-diagnostics-for-cloud-services-and-virtual-machines.md) を使用して、アプリケーションの Azure 診断の拡張機能を有効にします。

